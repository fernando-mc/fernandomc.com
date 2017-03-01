+++
publishdate = "2017-02-19T14:02:54-05:00"
date = "2017-02-19T14:02:54-05:00"
title = "Redshift Epochs and Timestamps"
draft = false
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

I've noticed that I end up looking for the same small subset of Redshift time-related conversion operations when I need to do things like change epochs to timestamps, deal with timezones or manage time ranges. To save myself some time I decided to throw them all into one post that I can reference later - I'm also hoping these will 
<!--more-->

Here are some basic examples for the following cases:

1. Timestamp with Time Zone to Epoch
2. Timestamp to Epoch

**Timestamp with Time Zone to Milisecond Epoch**

```sql
select 
    extract(
        'epoch' from timestamp with time zone '2016-02-20 00:00:00 US/Eastern'::timestamptz
)::bigint * 1000
```

Let's break it down.

CHECK THIS 1. The `extract()` fuction is synomous with date_part() and allows us to extract a epoch.
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





aws events put-targets --rule daily_tasks --targets = '{"Id"
 : "1", "Arn": "arn:aws:lambda:us-east-1:345638589857:function:woof_garden_cucko
o"}'
