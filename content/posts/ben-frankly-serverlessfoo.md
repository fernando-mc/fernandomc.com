+++
Description = "Ben Frankly Announcement - Serverlessfoo.com project."
Tags = [
  "Serverless",
  "Serverless Framework",
  "DynamoDB",
  "AWS",
  "Serverlessfoo",
  "Projects"
]
Categories = [
  "AWS",
  "Serverless",
  "Projects"
]
publishdate = "2017-10-10T10:51:05-07:00"
date = "2017-10-10T10:51:05-07:00"
+++

I've just released another [serverlessfoo.com](https://www.serverlessfoo.com) project - Ben Frankly: Benjamin Franklin's Frank Opinions of You.

[![A screenshot of the Ben Frankly Project](/images/ben_frankly/ben-frankly.png)](/posts/ben-frankly-serverlessfoo/)

After reading Benjamin Franklin's autobiography I noticed how he has a knack for memorably describing people. So I parsed through the autobiography and compiled a selection of those descriptions into another Micro-API using the Serverless Framework. 

<!--more-->

This API did a few things slightly differently. I've tried to make 'random' APIs with DynamoDB in the past but it felt super wasteful to use an inefficient DynamoDB scan to return data from the table. Because of that I opted to pre-assign numeric IDs to the data in the table that could randomly be calculated beforehand. Now there's a relatively minor calculation to create a random item's DynamoDB partition key and then that ID is used to lookup the item.

There's still a bit of work to do to make the API handle genders correctly. In the future I'd like an optional gender pronoun selector to automatically overwrite the gendered words. There's also probably a variety of quotes I've missed and things I could add from another of Ben's writings.

Here's hoping Ben thinks you're better than "a worthless fellow, tho' an excellent workman"!