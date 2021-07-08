
# Home on the range

## Abstract / Elevator Pitch (300 characters)

Building complex queries with individual `start` and `end` fields is inconvenient, inefficient, and not very effective, but working with ranges is a topic that is often glossed over. We will work through an example project that demonstrates the wide variety of using and querying with ranges.

## Audience Level

- Intermediate - Participants should already be familiar with building projects in django.

## Objectives

Audience members will 

## Outline

We will build out a Swimming Pool Scheduler project that makes heavy use of ranges (probably more than would be used in most projects) in order to demonstrate various approaches. Among other things, the project will be used to perform the following tasks:

- Access the lower and upper values of a DateTimeRangeField in the model instance
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


## Notes

Ranges (in queries and models) are something I struggled with for a long time. I eventually found that building complex queries with individual `start` and `end` fields was inconvenient and not very effective. Now I use ranges in dozens of places in my own apps. We will work through an example project that demonstrates a number of approaches to working with ranges. Working with ranges is a topic that is often glossed over. I had to do a lot of research to master working with ranges, and I hope to keep you from this misery.

Format: 3 hour tutorial

## Tags

    Django, PostgreSQL, database, intermediate, best practices
