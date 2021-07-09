

# Home on the range

## Abstract / Elevator Pitch (300 characters)

Building complex queries with individual `start` and `end` fields is inconvenient, inefficient, and not very effective, but working with ranges is a topic that is often glossed over. We will work through an example project that demonstrates the wide variety of using and querying with ranges.

## Audience Level

- Intermediate - Participants should already be familiar with building projects in django.

## Objectives

Audience members will learn why ranges are more useful than distinct start and end values, will become familiar with range-based terminology, will have the opportunity to see a number of approaches to using and querying with ranges, and will have resources for further reading and learning.

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

We will build out a Swimming Pool Resource Scheduler project that makes heavy use of ranges (probably more than would be used in most projects) in order to demonstrate various approaches. Among other things, the project will be used to perform the following tasks (2.25 hrs)

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

A before-and-after look (10 min)
    - We will go over a few side-by-side examples of queries using distinct start and end values vs. ranges to highlight the benefits

Resources (5 min)
    - Django docs
    - The django source code and tests
    - PostgreSQL docs on ranges

## Notes

Ranges (in queries and models) are something I struggled with for a long time. After starting out using lots of `start` and `end` fields in my projects, I found that they result in a lot more complication down the road, especially if you are trying to perform more advanced queries. Ranges seemed overly complicated for a long time. I had to do a lot of research to master working with ranges, and I hope to keep others from this misery. Now I use ranges in dozens of places in my own projects. In this talk, I will work through an example project that demonstrates a number of approaches to working with ranges, a topic that is often glossed over. 

Format: 3 hour tutorial

## Tags

    Django, PostgreSQL, database, intermediate, best practices
