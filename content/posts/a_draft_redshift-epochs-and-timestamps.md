+++
publishdate = "2017-06-20T14:02:54-05:00"
date = "2017-06-20T14:02:54-05:00"
title = "Redshift Epochs and Timestamps"
Description = "A look at a few common AWS Redshift time conversion operations"
Tags = [
  "PostgreSQL",
  "AWS",
  "AWS Redshift",
  "Redshift",
]
Categories = [
  "AWS",
  "Redshift",
]
menu = "main"

+++

I've noticed that I end up looking for the same small subset of Redshift time-related conversion operations when I need to do things like change epochs to timestamps, deal with timezones or manage time ranges. To save myself some time I decided to throw them all into one post that I can reference later - I'm also hoping these will be useful to others who find themseleves interacting with Redshift.
<!--more-->

In this post I'll show examples for doing the following operations:

1. Converting between dates and (millisecond) epochs. So going from an epoch to a date and also going from a date to an epoch.
2. 


**Changing between Dates and Epochs**

From date to epoch you can use either extract or date_part.

```sql

select 
  extract('epoch' from timestamp '2017-06-01') as extract_test,
  date_part(epoch, '2017-06-01') as date_part_test;

-- This returns:
-- extract_test | date_part_test
-- 1496275200 | 1496275200
```

When actually selecting from a table the query might look like this:
```sql

select 
  extract('epoch' from timestamp date_day_value_in_table) as extract_test,
  date_part(epoch, date_day_value_in_table) as date_part_test
from a_table_with_dates
```

In order to go from an epoch to a date you can use the following:

```sql
select timestamp 'epoch' + 1496275200 * interval '1 second'

-- 2017-06-01 00:00:00
```

Initially this is super opaque so let's break it down.

When we do `select timestamp 'epoch'` by itself we get `1970-01-01 00:00:00` which is special because it is the time when we started the epoch count. In other words - epoch 0 is the same as the date 1970-01-01 00:00:00.

So we could change the above to `'1970-01-01'::date` and this would behave the same way. For example:

```sql
select '1970-01-01'::date + 1496275200 * interval '1 second'

-- 2017-06-01 00:00:00
```

Now it should make a bit more sense why we add `1496275200 * interval '1 second'`. This is effectively adding a second to the starting date of 1970-01-01 for the epoch seconds.

As a quick aside. If we're dealing with milisecond epochs we're going to either divide or multiple the epoch value by 1000 whenever we do conversions on it.

If we're getting a date from a millisecond epoch we would divide by 1000 before we add the seconds to the 1970 date.

```sql
select '1970-01-01'::date + 1496275200000/1000 * interval '1 second'

-- 2017-06-01 00:00:00
```

If we're calculating a millisecond epoch from a date we're basically going to do the opposite with a small modification. We will need to cast our result from an int to a bigint or we'll see an error something like this:


`ERROR:  integer multiplication between (arg1: 1496275200, arg2: 1000) result out of range (1626580992)`

We can still use either of the methods we used above either with extract or date_part, as long as we cast the resut to bigint with `::bigint`.
```sql
select 
  extract('epoch' from timestamp '2017-06-01')::bigint * 1000 as extract_test,
  date_part(epoch, '2017-06-01')::bigint * 1000 as date_part_test;

-- This returns:
-- extract_test | date_part_test
-- 1496275200000 | 1496275200000
```







1. Date, epoch, and timestamp conversions
1. Timestamp with Time Zone to Epoch
2. Timestamp to Epoch

**Timestamp with Time Zone to Milisecond Epoch**

```sql
select 
    extract(
        'epoch' from timestamp with time zone '2017-06-01 00:00:00 US/Eastern'::timestamptz
)::bigint * 1000
```

Let's break it down.

1. The `extract()` fuction allows us to extract a epoch.
2. We then specify that we're extracting `from timestamp with time zone` to indicate our timestamp will have a zone such as `US/Eastern`. 
3. In this case we assuming that we're selecting a string that we cast to a timestamp with TZ value. If our table is setup with an entire column containing timestamps with TZ information we could modify our query to:
```sql
select 
    extract(
        'epoch' from timestamp with time zone your_timestamp_tz_column
)::bigint * 1000
```
4. Lastly we cast the resulting extracted value to a bigint to allow us to multiply it into a milisecond timestamp without causing an integer overflow error.


- Specific TZ Timestamp --> Epoch (Always in UTC)
- Timestamp --> Epoch

- Epoch --> Timestamp
- Epoch --> Timestamp with TZ

- Dealing with Timezones outside of redshift
- Date trunc and date add?

2. Write Post
4. Check publish date and date
6. Check summary
