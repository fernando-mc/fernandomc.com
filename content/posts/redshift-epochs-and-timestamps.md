+++
publishdate = "2017-06-20T14:02:54-05:00"
date = "2017-06-20T14:02:54-05:00"
title = "Redshift Epochs and Timestamps"
Description = "A look at a few common AWS Redshift date and time operations"
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

In this post I'll show examples for doing the following Redshift operations:

1. Changing dates to epochs
2. Changing epochs to dates
3. Dealing with millisecond epochs in both of these scenarios
4. Handling time zones in timestamp data

**1. Changing from Dates to Epochs**

To change from date to epoch in Redshift you can use either extract or date_part.

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

This assumes that your table contains timestamp values in the `date_day_value_in_table` column. But what if you wanted to do the reverse? 

**2. Changing from Epochs to Dates**

In order to go from an epoch to a date you can use the following SQL:

```sql
select timestamp 'epoch' + 1496275200 * interval '1 second'

-- Result: 2017-06-01 00:00:00
```

Initially this is super opaque so let's break it down.

When we do `select timestamp 'epoch'` by itself we get `1970-01-01 00:00:00` which is special because it is the time when we started the epoch count. In other words - an epoch of 0 is the same as the date 1970-01-01 00:00:00.

So we could also change the above `timestamp 'epoch'` to `'1970-01-01'::date` and this would behave the same way. For example:

```sql
select '1970-01-01'::date + 1496275200 * interval '1 second'

-- Result: 2017-06-01 00:00:00
```

Now it should make a bit more sense why we add `1496275200 * interval '1 second'`. This is effectively adding a second to the starting date of 1970-01-01 for each of the epoch seconds.

**3. Dealing with Millisecond Epochs**

If we're dealing with milisecond epochs we're going to either divide or multiple the epoch value by 1000 whenever we do conversions on it.

If we're getting a date from a millisecond epoch we would divide by 1000 before we add the seconds to the 1970 date.

```sql
select '1970-01-01'::date + 1496275200000/1000 * interval '1 second'

-- Result: 2017-06-01 00:00:00
```

If we're calculating a millisecond epoch from a date we're basically going to do the same operation we did earlier with a small modification. We will need to cast our result from an int to a bigint or we'll see an error something like this:

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

**4. Timezone Conversions**

Now let's get to some fun stuff. What do we do if we want to get an epoch value for June 1 2017 at 3pm Eastern Time? Epochs are by definition counted from `1970-01-01 00:00:00` in UTC so this is a little awkward. Fortunately, Redshift has some easy solutions for us.

If we have a timestamp with a time zone value in our data we can extract the epoch the same way we've done before with a few small additions:

```sql
select 
    extract(
        'epoch' from timestamp with time zone '2017-06-01 00:00:00 US/Eastern'::timestamptz
)::bigint * 1000

-- Result: 1496289600000
-- This is GMT: Thursday, June 1, 2017 4:00:00 AM
-- Which is Midnight Eastern Time
```

So let's break this down.

- The `extract()` fuction allows us to extract a epoch.
- We then specify that we're extracting `from timestamp with time zone` to indicate our timestamp will have a zone - in this case `US/Eastern`. 
- In this case we assuming that we're selecting a string that we cast to a timestamp with TZ value. 
- Lastly we cast the resulting extracted value to a bigint to allow us to multiply it into a milisecond timestamp without causing an integer overflow error mentioned above.

If our data table was setup with an entire column containing timestamps with TZ information we could modify our query to:

```sql
select 
    extract(
        'epoch' from timestamp with time zone your_timestamp_tz_column
)::bigint * 1000
```

Hopefully this was helpful for you as you learn more about Redshift date and time operations. Still have some questions? Leave a comment below or send me a message on [Twitter]({{% my_twitter %}}).
