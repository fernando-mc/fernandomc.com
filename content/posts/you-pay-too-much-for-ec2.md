+++
Description = "You pay too much for Amazon EC2. There. I said it. Here's how you can save."
Tags = [
  "Architecture",
  "AWS",
  "EC2",
  "Spot Instances",
  "Reserved Instances",
  "Cost Optimization",
  "Cost",
  "AWS Cost Optimization",
  "Pricing",
  "Savings",
]
Categories = [
  "AWS",
  "Architecture",
]
title = "You Pay Too Much for EC2"
publishdate = "2019-07-24T09:44:19-07:00"
date = "2019-07-24T09:44:19-07:00"
[image]
    feature = "/images/you-pay-too-much-for-ec2/destroyed-money.png"
    credit = "TaxCredits.net"
    credtlink = "https://www.flickr.com/photos/76657755@N04/7408506410/"
+++

I'm about to tell you something ridiculous. You might be paying 70% or more on EC2 than you need to be. With a combination of Spot and Reserved Instances you can dramatically lower your EC2 bill on AWS.

<!--more-->

# Comparing Apples to Apples

Now just to make sure that we're being fair as we're evaluating different purchasing options, let's make sure we're looking at the same instance type for every purchasing method we're evaluating. For the purposes of these comparisons I will:

- Use the cost of a `t3a.nano` instance
- Look exclusively at the cost in the AWS Northern Virginia region
- Compare the relative costs over several different periods
- Ignore costs associated with other services (data transfer, EBS Volumes etc.)

It's also important to note that each of these EC2 purchasing options have their own use cases and wont work identically to one another.

With those caveats out of the way... Let's get started!

# On-Demand Instances

On-Demand Instances are your typical, run-of-the-mill EC2 instance purchasing option. When you spin up an instance inside the AWS console for the first time, chances are it was an on-demand instance.

If we spin up a `t3a.nano` instance on-demand in Northern Virginia it costs	`$0.0047 per Hour`. That means that it will cost:

- $0.1128 - per day
- $3.384 - per 30-day month
- $41.172 - per year
- $123.516 - every 3 years

This is our baseline for the relatively cheap `t3a.nano` instance, but we can do better.

# Spot Instances

Spot instances are unused EC2 instances that AWS basically sells to you cheap because they're not making any money off of it in that moment. These instances have **up to 90%** savings off of regular instances. Though I've typically seen them floating at about 70% off depending on the instance type. Let's take a look at the price of a `t3a.nano` Spot Instance as of the writing of this post.

![Price of t3a.nano on spot](/images/you-pay-too-much-for-ec2/t3a-nano-spot-price.png)

In the image above you can see that the hourly price is `$0.0014 per Hour`. Yeah you read that right. That translates to **70%** savings over the on-demand price! That means that it will cost:

- $0.0336 - per day
- $1.008 - per 30-day month
- $12.264 - per year
- $36.792 - every 3 years


## Drawbacks and Use Cases of Spot Instances

Now, this isn't a direct comparison. Spot instances aren't guaranteed to stick around. That's why they're so cheap. If Amazon needs them back at some point they'll send you a shutdown warning to allow you to turn off your applications gracefully and then they will take the instances back. However, if you can design an application to be tolerant to this possibility you can get significant savings. Here's two ideas of great ways to use them:

**Backlogged Work (Queues)**

Consider using Spot Instances to process work that you don't need done immediately. For data engineers, you could use spot instances to run nightly batch jobs over massive amounts of data with a fraction of the on-demand price. This also works amazingly if you have memory-intensive jobs that need to run such as training ML models, rendering, or transcoding.

**As Part of Larger Groups of Compute**

If you're using larger clusters of instances in something like an Auto Scaling Group or EC2 Fleet you should seriously consider adding Spot Instances to benefit from the cost savings they provide. When they're not available, you can always fall back to on-demand instances or other options like Reserved Instances.

# Reserved Instances

As the name suggests, Reserved Instances (or RIs) are a way for you to reserve EC2 instance capacity in AWS. However, it's important to realize these aren't actually *instances* you're buying. Instead, they are *billing credit* applied to future use of EC2 instances that the Reserved Instance your purchased supports. 

Think of this like a yearly gym membership: You can probably get a great discount for committing to the full year, but you're on the hook for the entire time even if you don't decide to go. AWS does have a marketplace that allows you to sell Reserved Instances under specific conditions. But, to start, it's probably best to only use RIs if you think you're very likely to have a level of use that will justify the purchase.

Two of the most common RIs are convertible and standard (there are also Scheduled RIs but let's ignore those for now). Convertible RIs are slightly more flexible in how you can use them on specific types and sizes of instances and Standard RIs are more strict. Because of this, Standard RIs offer better savings while the savings from Convertible RIs are slightly less.

We can make either 12 or 36-month reservations with larger discounts for the longer length of commitment.

## 12-Month Reservation

![Price of 12 month reserved t3a.nano purchase](/images/you-pay-too-much-for-ec2/t3a-nano-12mo-reserved.png)

As you can see above, the total cost of a `t3a.nano` for 12 months is $24 for standard and $28 for convertible. This translates approximately to:

- $0.0032 per hour for convertible 12-mo RI
- $0.0027 per hour for standard 12-mo RI

Because the the 12-month convertible RI is the most expensive of the RI options let's put that into the same terms as our earlier comparisons. Using this kind of Reserved Instance to purchase the `t3a.nano` would cost:

- $0.0767 - per day
- $2.301 - per 30-day month
- $28 - per year
- $84 - every 3 years

This represent about a **32% discount** over on-demand instances with the 12-month convertible RI!

## 36-Month Reservation

![Price of 36 month reserved t3a.nano purchase](/images/you-pay-too-much-for-ec2/t3a-nano-36mo-reserved.png)

As you can see above, the total cost of a `t3a.nano` for 36 months is $56 for convertible and $46 for standard. This translates to approximately:

- $0.0021 per hour for convertible 36-mo RI
- $0.0018 per hour for standard 36-mo RI

Because the 36-month standard reservation provides the most savings, let's use that to get a look at the highest level of savings on this instance type. A 36-month standard RI translates to approximately: 

- $0.042 - per day
- $1.28 - per 30-day month
- $15.33 - per year
- $46 - every 3 years

This represents a discount of **63%** over on-demand instances!

## Drawbacks and Use Cases of Reserved Instances

The first drawback of Reserved Instances is that you're on the hook for paying for them. You're going to end up giving AWS money for the purchase regardless of if you use the instances for anything or not. This is slightly offset by the ability to sell your Reserved Instances in the AWS Marketplace, but is still something to contend with.

Another potential drawback is that each type of reserved instance has limitations on what sort of EC2 instance you can use it with. It's not a simple X% discount across the board. You do have to be strategic about the kinds of instances you use and the sorts of Reserved Instances you buy.

So what are some good use cases or scenarios for Reserved Instances?

**Reserved Instances and Large Organizations**

If your EC2 bill is consistently over $1000 a month and you're not using Reserved Instances I think there is a 99% chance you're leaving money on the table. Unless you're sporadically using a huge variety of EC2 instances and only for short periods (in which case I really hope you're using Spot Instances!) then you should take the time to figure our what Reserved Instance purchase is right for you.

**Capacity Reservation**

In addition to the cost savings, some ways of using Reserved Instances allow you to guarantee that AWS will have a specific level of capacity for you. This might be unlikely, but it could be the critical difference between having the capacity you need during high-traffic events like like Black Friday or Cyber Monday. It's totally possible that during those times AWS becomes overwhelmed and can't provide on-demand instances in some regions.

# More Resources

It's essential that you consider how Spot Instances and Reserved Instances fit in with your cost optimization strategies. Spot Instances may take some architectural planning and Reserved Instances may require some additional budgeting, but not taking these options into account is just leaving money on the table.

If you'd like to learn more about how to optimize cost on AWS you can check out my Pluralsight course - [Architecting for Cost on AWS](https://www.pluralsight.com/courses/aws-architecting-cost). You can always [reach out](/contact) for a free trial to Pluralsight or about my consulting services through [Stormlight Consulting](https://www.stormlightconsulting.com).

Have you worked with Spot and Reserved Instances? Do you have other cost optimization techniques you'd like me to explore? Leave a comment below!