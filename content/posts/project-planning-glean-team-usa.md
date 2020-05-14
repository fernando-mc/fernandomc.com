+++
Description = "A look at using Amazon Translate to translate text using Boto3 and the AWS SDK for JavaScript"
Tags = [
  "Project Planing",
]
Categories = [
  "Projects",
]
title = "Project Planning with Glean Team USA"
publishdate = "2020-05-14T12:22:16-07:00"
date = "2020-05-14T12:22:16-07:00"
[image]
    feature = "/images/20-projects-20-days/planning.png"
    credit = "HUHEZI"
	  creditlink = "https://www.flickr.com/photos/huhezi/24975955105/"
+++

In this post, we look at a project I had planned for my [Twenty Projects in Twenty Days](https://fernandomc.com/posts/twenty-projects-in-twenty-days/) series. Glean Team USA was a project dedicated to showcasing different opportunities for gleaning - or getting free food from farmers who can't pick or sell it themselves. With any substantive new project I take on though, I went through a research and planning phase that yielded some unexpected results! Let's take a look at the planning and design process and what happened.

As a spoiler, this is a post about the project planning process and doesn't actually contain any code! If you really want to see some more code check out my other projects in this series!

<!--more-->

## Planning a Project

I initially anticipated this project would be an attempt to use a mapping API to map available gleaning locations across the United States. Due to fluctuations in demand with COVID-19 and the increased risk of hunger for people across the United States, I thought creating a tool to map these locations could be useful. This project would give folks a chance to find gleaning locations near them and connect with other gleaners or farmers with available food.

I ended up live streaming part of the planning, research and brainstorming process for anyone to watch. As part of the planning I made sure to ask some important questions:

1. What are we trying to build?
2. Can we build it?
3. Why are we building it?
4. Does this exist already?

I think having a few simple questions like these answered before you start a project can really inform what you do next. It doesn't necessarily mean that you need to let the answers dictate if or how you build a project, but having that information prior to starting will help a lot!

**What are we trying to build?**

with this question, I like to make sure I have some semblance of what I have in my head. I like writing down the technologies and tools I anticipate using as well as what I think the user experience will look like, what the architectural design might be and other related questions. In this case, I collected a few areas I knew I'd need to work on:

- Mapping APIs: I knew that tools like Google Maps APIs and the Mapbox APIs might make sense as a starting point to evaluate how I would plot out the location data on a map.
- Frontend: I also knew I'd need a frontend that used tools I was familiar with so I had planned to use something like Semantic UI and Vercel
- Data storage and retrieval: For this I planned to use the Serverless Framework to create a few APIs to store data in Amazon DynamoDB
- Data collection: I wanted there to be options both for submitting data directly on the site through the Serverless Framework APIs, but I also considered going out and scraping a lot of data to start the project with.

**Can we build it?**

This is what I like to think about as a moment to give my beautiful dream project a reality check. In this case, I like to ask a lot of supplementary questions:

- Is the scope of what I'm imagining for this project possible for me to do in the time I want to dedicate to it?
- Do I currently have the skills I need to build this?
- Can I pick up any skills I don't have yet within the time I want to work on this?
- Is this going to break my budget for this project?
- Do I need more help to build a project like this?

When I answer these questions, I get a better picture of if the project I have in mind will be a good one for me to work on right now. For this project, I was fairly confident I had the technical skills I needed or could pick up the thing I needed to learn pretty quickly. 

For example, I knew that I'd built similar frontends and APIs before so that was handled. I also knew that the AWS infrastructure, the frontend deployment and the user submitted data were things I'd worked with before too. 

However, I needed to learn a little more about a Mapping API and mapping visualizations. I felt this was a small enough learning gap that it made sense to continue. In fact, for most projects I do there's always going to be *some* learning gap - or else I'd probably get bored of them!

So now with this general idea of what I'm trying to build in mind, along with a confidence that I can build it we also have to ask *why* are we building it?

**Why are we building it?**

There's a lot of potential answers to this question that are completely fine, for most of my technical projects the answer is often "I think it's a cool idea!". However, for this project I actually want it to be helpful. The goal here for me was to help people seek out available produce, reduce food waste, and get more people fed. So I need to understand if this is going to do that!

There are a few ways to see if this project would do that, one of the most obvious to me would have been to talk with folks involved in this work and ask them how I could help, but given my time constraints this was a challenge. Instead, I opted to do as much online research as I could about existing gleaning organizations and how they operate. Which brings me to my last big question! Am I wasting my time reinventing the wheel?

**Does this exist already?**

As it turns out, yes [it does](http://www.fallingfruit.org/)! In fact, there are [multiple](https://www.cityfruit.org/harvest/join-harvest) useful and [different](https://github.com/scyclops/fruitdrop) tools and organizations that very closely match the ideas of what I was hoping to build.

This complicated things a bit! If this was a for-profit endeavor or a hobby project it wouldn't have necessarily deterred me at all - Competitors aren't always a bad sign when you're going into business. And hobby projects are to learn things and have a great time! With a tool like this, that I imagined being used by a community of people, it makes sense to to be much more involved with these organizations before I built something out without talking to them.

## What Next?

Because of my research I opted to suspend this project for the moment. After I have a chance to talk to the people involved in all the amazing gleaning organizations I found I might revisit it in the future with their help. For now though, I hope this process gives you a bit of a framework for evaluating your own new projects!
