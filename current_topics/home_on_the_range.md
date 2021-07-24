
# Home on the range with Django - getting comfortable with ranges and range fields

## Abstract / Elevator Pitch (300 characters)

Building complex queries with individual `start` and `end` fields is inconvenient, inefficient, and not very effective, but working with ranges is a topic that is often glossed over. We will work through an example project that demonstrates the wide variety of using and querying with ranges.

## Audience Level

- Intermediate - Participants should already be familiar with building projects in django.

## Objectives

Audience members will learn why ranges are more useful than distinct start and end values, will become familiar with range-based terminology, will have the opportunity to see a number of approaches to using and querying with ranges, and will have resources for further reading and learning. These resources will include a link to a GitHub repository containing all of the examples from the tutorial.

## Outline

The naive approach to ranges (5 min)
- Using separate start and stop model fields
- Querying with start and stop values
- It quickly gets complicated

Range visualization for concrete understanding (10 min)
- Terminology
    - Inclusive vs Exclusive
    - Overlap
    - Contains
    - Contained By
    - Comparisons (fully_lt, fully_gt, etc)

A before-and-after look (10 min)
- We will go over a few side-by-side examples of queries using distinct start and end values vs. ranges to highlight the benefits

We will build out a Swimming Pool Resource Scheduler project that makes heavy use of ranges (probably more than would be used in most projects) in order to demonstrate various approaches. (2.5 hrs)

The initial model layout can be visualized in the following diagram:

![](https://lucid.app/publicSegments/view/8031b83b-fde9-48d8-a812-ae249f283690/image.png)

The initial (stripped down) models.py file using distinct lower and upper values is:

```python
Pool models

from django.db import models
from django.utils.translation import ugettext_lazy as _


class Pool(models.Model):
    """An instance of a Pool. Multiple pools may exist within the municipality"""

    name = models.CharField(_("Pool Name"), max_length=100)
    address = models.TextField(_("Address"))
    depth_minimum = models.IntegerField(_("Depth Minimum"), help_text=_("What is the depth in feet of the shallow end of this pool?"))
    depth_maximum = models.IntegerField(_("Depth Maximum"), help_text=_("What is the depth in feet of the deep end of this pool?"))
    
    class Meta:
        verbose_name = _("Pool")
        verbose_name_plural = _("Pools")


class Closure(models.Model):
    """A way of recording dates that a pool is closed"""

    pool = models.ForeignKey(Pool, on_delete=models.CASCADE, related_name="closures")
    start_date = models.DateField(_("Pool Closure Start Date"))
    end_date = models.DateField(_("Pool Closure End Date"))
    reason = models.TextField(_("Closure Reason"))
    
    class Meta:
        verbose_name = _("Closure")
        verbose_name_plural = _("Closures")


class Lane(models.Model):
    """Each pool may have multiple lanes, each of which can be reserved by multiple people"""

    name = models.CharField(_("Lane Name"), max_length=50)
    pool = models.ForeignKey(Pool, on_delete=models.CASCADE, related_name="lanes")
    max_swimmers = models.PositiveSmallIntegerField(_("Maximum Swimmers"), )
    per_hour_cost = models.DecimalField(_("Per-Hour Cost"), max_digits=5, decimal_places=2)

    class Meta:
        verbose_name = _("Lane")
        verbose_name_plural = _("Lanes")


class Locker(models.Model):
    """Each pool may have multiple lockers, each of which can be reserved by only one person at a time"""

    # Using CharField, because sometimes locker number might be "A23" or "56-B"
    number = models.CharField(_("Locker Number"), max_length=20)
    pool = models.ForeignKey(Pool, on_delete=models.CASCADE, related_name="lockers")
    per_hour_cost = models.DecimalField(_("Per-Hour Cost"), max_digits=5, decimal_places=2)

    class Meta:
        verbose_name = _("Locker")
        verbose_name_plural = _("Lockers")


class LaneReservation(models.Model):
    """A lane reservations defines a set of users, a period of time, and a pool lane"""

    users = models.ManyToManyField(User, related_name="lane_reservations")
    lane = models.ForeignKey(Lane, on_delete=models.CASCADE, related_name="lane_reservations")
    period_start = models.DateTimeField(_("Reservation Period Start"))
    period_end = models.DateTimeField(_("Reservation Period End"))

    class Meta:
        verbose_name = _("Lane Reservation")
        verbose_name_plural = _("Lane Reservations")
    

class LockerReservation(models.Model):
    """A locker reservation defines a user, a period of time, and a pool locker"""

    user = models.ForeignKey(User, related_name="locker_reservations")
    locker = models.ForeignKey(Locker, on_delete=models.CASCADE, related_name="locker_reservations")
    period_start = models.DateTimeField(_("Reservation Period Start"))
    period_end = models.DateTimeField(_("Reservation Period End"))

    class Meta:
        verbose_name = _("Locker Reservation")
        verbose_name_plural = _("Locker Reservations")

```

The final (stripped down) models.py with ranges is:

```python
Pool models

from django.db import models
from django.utils.translation import ugettext_lazy as _


class Pool(models.Model):
    """An instance of a Pool. Multiple pools may exist within the municipality"""

    name = models.CharField(_("Pool Name"), max_length=100)
    address = models.TextField(_("Address"))
    depth_range = models.IntegerRangeField(_("Depth Range"), help_text=_("What is the range in feet for the depth of this pool (shallow to deep)?"))
    
    class Meta:
        verbose_name = _("Pool")
        verbose_name_plural = _("Pools")


class Closure(models.Model):
    """A way of recording dates that a pool is closed"""

    pool = models.ForeignKey(Pool, on_delete=models.CASCADE, related_name="closures")
    dates = models.DateRangeField(_("Pool Closure Dates"))
    reason = models.TextField(_("Closure Reason"))
    
    class Meta:
        verbose_name = _("Closure")
        verbose_name_plural = _("Closures")


class Lane(models.Model):
    """Each pool may have multiple lanes, each of which can be reserved by multiple people"""

    name = models.CharField(_("Lane Name"), max_length=50)
    pool = models.ForeignKey(Pool, on_delete=models.CASCADE, related_name="lanes")
    max_swimmers = models.PositiveSmallIntegerField(_("Maximum Swimmers"), )
    per_hour_cost = models.DecimalField(_("Per-Hour Cost"), max_digits=5, decimal_places=2)

    class Meta:
        verbose_name = _("Lane")
        verbose_name_plural = _("Lanes")


class Locker(models.Model):
    """Each pool may have multiple lockers, each of which can be reserved by only one person at a time"""

    # Using CharField, because sometimes locker number might be "A23" or "56-B"
    number = models.CharField(_("Locker Number"), max_length=20)
    pool = models.ForeignKey(Pool, on_delete=models.CASCADE, related_name="lockers")
    per_hour_cost = models.DecimalField(_("Per-Hour Cost"), max_digits=5, decimal_places=2)

    class Meta:
        verbose_name = _("Locker")
        verbose_name_plural = _("Lockers")


class LaneReservation(models.Model):
    """A lane reservations defines a set of users, a period of time, and a pool lane"""

    users = models.ManyToManyField(User, related_name="lane_reservations")
    lane = models.ForeignKey(Lane, on_delete=models.CASCADE, related_name="lane_reservations")
    period = models.DateTimeRangeField(_("Reservation Period"))

    class Meta:
        verbose_name = _("Lane Reservation")
        verbose_name_plural = _("Lane Reservations")
    

class LockerReservation(models.Model):
    """A locker reservation defines a user, a period of time, and a pool locker"""

    user = models.ForeignKey(User, related_name="locker_reservations")
    locker = models.ForeignKey(Locker, on_delete=models.CASCADE, related_name="locker_reservations")
    period = models.DateTimeRangeField(_("Reservation Period"))

    class Meta:
        verbose_name = _("Locker Reservation")
        verbose_name_plural = _("Locker Reservations")

```

We will use the models in this project to perform many of the following tasks:

- Set constraints for the various range fields
- Access the lower and upper values of a range fields in views and templates
- Get reservations that start at a specific datetime
- Get reservations that overlap with a single date
- Get reservations that overlap with a range
- Check if a reservations start datetime matches any value in a list
- Get reservations with a start or end that falls between two dates
- Get reservations whose lower/upper date falls within a range
- Get reservations for the current week
- Get reservations that end before now
- Get reservations that start on or before September
- Get reservations that end in May OR September
- Given a list of datetimes, get all reservations that overlap with the items in the list
- Order the queryset by lower. If two reservations have the same lower, also sort by upper. (This is the default behavior in django)
- Get reservations with an overdue start (the reservation time started, but the party has not yet checked in)
- Get reservations with an overdue end (the reservation time ended, but the party has not checked out)
- Given a datetime (and other filtering criteria), get the count of reservations at that moment
- For a particular Lane (or set of Lanes), get the aggregate count/sum of reservations during each hour of a daterange
- Sum of all swimmers at a given moment
- Sum of all swimmers at each time period change
    - For a particular Lane or entire Pool, get the time and value of all changes in number of swimmers
    - Given a start time stop time and a Lane, return a queryset of the swimmers at each 15 minute increment.
- Calculated overall usage (number of swimmers * time interval = swimmer hours) within a time range
    - Total usage by day/week/month by Pool/Lane
    - Usage by range:  Given a list of ranges, calculate the usage during each range
- Prevent overlapping reservation_period for the same resource (here, it's Rooms), where the reservation has not already been cancelled
- Add validation to model field for minimum and maximum datetimes
- Aggregate the minimum Lower and maximum Upper reservations dates for all reservations in a queryset
- Similarly, annotate the minimum Lower and maximum Lower reservations dates for all reservations in a queryset
- Assuming each reservation is associated with a Resource, annotate Resources with the most recently ending reservation (similar for most recent starting or longest-ago starting/ending reservation)
- Multiple ways of saving a model instance with DateTimeRangeField

Resources (5 min)
- Django docs
- The django source code and tests
- PostgreSQL docs on ranges

## Notes

Ranges (in queries and models) are something I struggled with for a long time. After starting out using lots of `start` and `end` fields in my projects, I found that they result in a lot more complication down the road, especially if you are trying to perform more advanced queries. Ranges seemed overly complicated for a long time. I had to do a lot of research to master working with ranges, and I hope to keep others from this misery. Now I use ranges in dozens of places in my own projects. In this talk, I will work through an example project that demonstrates a number of approaches to working with ranges, a topic that is often glossed over. 

Format: 3 hour tutorial

## Tags

    Django, PostgreSQL, database, intermediate, best practices
