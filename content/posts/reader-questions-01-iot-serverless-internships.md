+++
Description = "My Answers to Questions from Blog Readers. This first episode covers transitioning to work in IoT, how to learn about AWS and Serverless, and more about my content workflows."
Tags = [
  "Questions",
  "Architecture",
  "Azure",
  "AWS",
  "Serverless",
  "Python",
  "IoT",
]
Categories = [
  "Q&A",
]
title = "Reader Questions Episode 1: IoT, Serverless, and Jobs"
publishdate = "2021-01-16T09:54:10-08:00"
date = "2021-01-16T09:54:10-08:00"
type = "blog"
sponsor_message = "Help me answer questions like this more often by signing up for a free trial of Pluralsight and taking my courses!"
sponsor_link = "https://pluralsight.pxf.io/mA1DD"
[image]
    feature = "/images/reader-questions/episode1.png"
    postheader = "/images/abstract-6-short.png"
+++


This is the first episode of Reader Questions! I've started getting more and more questions from readers of my blog and social media so I've decided to start posting my answers publicly so everyone can benefit. These questions and answers may be edited slightly to apply more generally and preserve anonymity but hopefully they'll be useful for you too!

Today's episode contains questions related to serverless, frontend development, IoT, internships, and more!
<!--more-->

**Question 1 - Where do I start with AWS and serverless?**: 
  > Hi! I’m a freshman in college trying to learn as much as I can about AWS and Serverless to get the opportunity to work for a company. I have been going through some of your blogs to grasp the basics of Serverless and was wondering if you could give me some advice. I started learning Javascript in early 2020 and know some Node.js and TypeScript but am unsure of what to specifically learn concerning AWS and Serverless. I have found many websites, books, and online courses but am overwhelmed by the options and where to start. What would you recommend? Are there any prerequisites I need for learning Serverless and AWS, for example should I learn React? I’d appreciate any guidance.

**My Answer:**

Sounds like you're off to a really great start! There's always a lot to learn so the classes can be overwhelming.

For folks looking for work/find internships I'd focus on the descriptions for jobs or particular programs that you are targeting. If you review the skills listed in the job or internship description that should give you a place to work backwards from. 

For example, if the description lists out skills like Python, DynamoDB, and AWS Lambda then you have three specific areas to start learning more about. But if it has other AWS services, programming languages, or frameworks, then you might want to focus somewhere else.

Ideally, if you have time to create projects that showcase those skills you'll be a better spot when you get into the interviews because you'll have examples that showcase the skills you learned and are claiming to have.

If you're looking for specific AWS/Serverless courses I am a bit biased to my own Pluralsight courses - I've made a few on AWS and serverless development. You can use [this link](https://pluralsight.pxf.io/mA1DD) to get a free trial of Pluralsight and then just pick a few courses out that you think sound like fun. Or, if you're already planning to learn other specific skills, Pluralsight has some great paths to get you started.

In relation to frontend development - It's an important skills and won't hurt at all. But it's not directly related to AWS/Serverless development. You could replace the frontend of an application with other technologies and still rely on the same AWS/Serverless backend technologies.

With that said, there are some "serverless" frontend tools that really help you with frontend development. Vendors like [Netlify](https://www.netlify.com/) and [Vercel](https://vercel.com/) are becoming the new standard in deployment because they do so much of the hard work for you and make it really easy to deploy your websites, improve performance, cache everything and other hard bits.

I hope this helps!

**Question 2 - From David: How do you get into IoT Development from Frontend?**

  > I am a Frontend Developer and would like to expand my skills. I got really interested in IoT in the last 6 months and experimented with different avenues to grasp the subject. Recently, I realized I should check out people who work in IoT and found you and your work on LinkedIn. What would be the next logical step for a Frontend (JavaScript, React) developer to expand their skills in the IoT realm?

**My Answer:**

Web frontend to IoT is can be a big shift but one I hope you'll find rewarding! From my understanding there is probably less direct overlap between those skill sets than say backend development to IoT.

The best way forward depends on what path you see yourself going down. If you're looking to do the equivalent of "frontend" development in the IoT world you might end up learning something like Qt or Android to develop embedded UIs. Here's a [great article](https://witekio.com/blog/qt-or-android-whats-best-for-your-embedded-device/) talking these from a coworker of mine - [Adrien Leravat](https://www.linkedin.com/in/adrienleravat).

But there is also plenty of opportunity to learn about embedded development more generally. For many of these possible paths, knowledge of an operating system is critical. Many of my coworkers know Linux *very* well so they can customize the embedded software that runs on different kinds of devices. Going down that route, you'd want to learn the fundamentals of Linux operating systems and things like bash scripting. You'd probably also want to have some exposure to a language like C++ which is a commonly used language in embedded development.

If you want to take my route, where I sit in the space between the hardware and the cloud then you'd want to learn a lot more about how cloud systems interact with IoT devices. Things like Public Key Infrastructure, X509 certificate chains, and the cloud services that handle these sorts of connections. You might then also learn more about the cloud services that let you build applications on top of all that. 

Here are some "Getting Started" options. If you want a general overview to AWS IoT I have a Pluralsight course you can take for free:

- [This link](https://pluralsight.pxf.io/RW5Bb) should get you at least a 10 day trial (let me know if it doesn't!) 
- And that should be plenty of time to take my course on [AWS IoT](https://www.pluralsight.com/courses/aws-iot-big-picture). Fair warning, this is a high-level course without much coding - really just a way to get started.

I also recently wrote a few detailed step-by-step guides on IoT devices that might give you a place to start:

- One on connecting a Raspberry Pi device to [AWS IoT Core](
https://witekio.com/blog/connect-raspberry-pi-aws-iot/
)
- Another on an hooking up an [Avnet MaaXBoard to Azure IoT Hubs](https://witekio.com/blog/maaxboard-to-azure-iot/)
- And a while back I wrote a hardware guide [on PocketBeagle](https://www.fernandomc.com/posts/pocket-beagle-board-getting-started/), but this doesn't have any cloud-based portions.

I hope this helps! Best of luck and keep me posted on how things go!

**Question 3 - From David: How do you produce so much content?** 

(This seemed like flattery to get me to answer the other questions that came with this email but I thought I'd include some information opn my writing and contnet making in this batch of questions in case it is actually helpful for anyone. This might seem like a roundabout answer initially but I promise it isn't!)

**My Answer**

For a long time I've prioritized publicly learning. It might come as a surprise to some folks, but when I made my first software engineering course I was not, in fact, a software engineer. I worked a technical support job that, to be honest, I was very frustrated with. I lucked out and had a few excellent mentors who helped me learn the right skills to transition to a data engineering role and later focus more on cloud services.

I constantly documented the new or challenging things I did. 

What did I do when I felt completely incompetent while struggling through a problem? I wrote down how I did it! When I couldn't find (good) explanations anywhere of how to do something I wrote a blog post. If I was learning a new framework, I pitched a course on it to Pluralsight. To this day, one of my most popular blog posts is a post on [Redshift Epochs and Timestamps](https://www.fernandomc.com/posts/redshift-epochs-and-timestamps/) that I wrote around four years ago while struggling through learning SQL. My most popular Pluralsight course on AWS Lambda was made while I was just starting as a data engineer.

This led to most of my later jobs. I snagged a job at Serverless Inc. at least in part because I made the course on AWS Lambda and the Serverless Framework for Pluralsight. I attribute getting a role at Witekio (my current employer) at least partly because I'd made courses and wrote guides on AWS that some of my coworkers were familiar with.

In my most recent job search, I prioritized jobs that were comfortable with these elements of my technical work. I was transparent with Witekio about my blog and publications for Pluralsight. The fact that they supported this work was a big part of why I accepted an offer with them. Additionally, knowing that I can produce public guides and tutorials with them helps it look like I'm producing more because I get to share it with Witekio's customers and my readers here too!

Anyway, this was a long answer. In short, what you're seeing as me "producing so much", is making sure that I prioritize fulltime work that lets me document my own learning process and understands that I learn and create content on my own outside of the typical workday!
