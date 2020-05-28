+++
Description = "A look at the architecture behind the Voices of COVID project."
Tags = [
  "Architecture",
  "Serverless",
  "Python",
  "Vue.js",
]
Categories = [
  "AWS",
  "Projects",
]
title = "Voices of COVID - Technical Overview"
publishdate = "2020-05-27T08:50:04-07:00"
date = "2020-05-27T08:50:04-07:00"
[image]
    feature = "/images/abstract-7-short.png"
+++

I recently created a project aimed at collecting the stories of people impacted by the COVID-19 pandemic called [Voices of COVID](https://voicesofcovid.com). This project is designed to collect audio stories from people impacted by the pandemic and make them searchable. For more about the motivation behind it you can review [this post](https://fernandomc.com/posts/voicesofcovid/). But for now, let's look at how it's put together!
<!--more-->

# The Architecture

To start, let's look at the project's architecture:

![A Diagram of the Voices of COVID architecture.](/images/20-projects-20-days/voicesofcovid-diagram.png)

This is a bit much to digest all at once so let's break it down by the flow of data in the application.

1. The user starts by interacting with a website hosted in Amazon S3.

![A Diagram of the Voices of COVID architecture.](/images/20-projects-20-days/voicesofcovid-p0.png)

2. From there, they can use the frontend to create an audio file. After creating the file, they can upload it to S3 using a presigned S3 URL.

![A Diagram of the Voices of COVID architecture.](/images/20-projects-20-days/voicesofcovid-p1.png)

3. This triggers a Lambda function to: process the S3 upload, create a temporary entry in a DynamoDB table with the metadata about the submission, and send an email approval message request to an admin.

![A Diagram of the Voices of COVID architecture.](/images/20-projects-20-days/voicesofcovid-p2.png)

4. The admin can then use a link in the email to listen to the audio file and optionally approve the request if the audio is okay. On approval, a Lambda function is used to fetch the metadata about the audio file and insert it into Algolia to make it searchable.

![A Diagram of the Voices of COVID architecture.](/images/20-projects-20-days/voicesofcovid-diagram.png)

As soon as data gets into Algolia, the frontend will display it for all users.

# The Code

To review the code for this project - (or to contribute!) you can go to the [GitHub repository](https://github.com/fernando-mc/voicesofcovid.com). Let's do a quick review of the code!

The `frontend` folder contains all of the code to put together the Vue.js project that powers the frontend application.

The `backend` folder and the files in the root of the repository help power the backend architecture described above.

# How you can help!

If you'd like to help with this project, you can file issues or suggested feedback on the [GitHub repository](https://github.com/fernando-mc/voicesofcovid.com). Right now, there is still a lot of improvement to be made to the frontend portion of the project to make it responsive on mobile devices. 

If you have feature suggestions or notice improvements to be made please leave an issue on the project!