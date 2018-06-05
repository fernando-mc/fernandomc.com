+++
Description = "I recently published a post on creating and deploying a Flask application on ECS. This post links to where I published that post elsewhere."
Tags = [
  "Docker",
  "ECS",
  "Python",
  "AWS",
  "AWS Flask",
  "Containers",
  "Web App",
]
Categories = [
  "AWS",
  "Containers",
]
title = "Creating Your First Containerized Flask Application with AWS ECS and Docker"
menu = "main"
publishdate = "2018-06-05T07:28:07-07:00"
date = "2018-06-05T07:28:07-07:00"
[image]
    feature = "/images/flask_ecs_docker/ecs_flask.png"
+++

Interested in creating and deploying your own Flask application using Docker and ECS? Well, good news, I just [published a post](https://wpengine.linuxacademy.com/linuxacademy-com/deploying-a-containerized-flask-application-with-aws-ecs-and-docker/) on how you can do this. Here are the highlights:

<!--more-->

1. You'll create a Docker image of your Flask application locally
2. You'll then push the Docker image up to ECR - AWS's Elastic Container Repository
3. You'll create and configure your own ECS service using your Docker image and deploy it to production. 

![Visualization of an ECS service with Container and Task definitions, service details and cluster details](/images/flask_ecs_docker/ecs_example.png)

By the end, you'll have deployed your containerized Flask application to production and be able to show it off to anyone. Just look how cute the penguins are:

![Website with many adorable penguins](/images/flask_ecs_docker/ecs_flask.png)

[Check it out](https://wpengine.linuxacademy.com/linuxacademy-com/deploying-a-containerized-flask-application-with-aws-ecs-and-docker/) and let me know what you think below!


