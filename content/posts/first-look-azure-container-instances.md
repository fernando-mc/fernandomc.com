+++
Description = "A first look at Azure Container Instances"
Tags = [
  "Architecture",
  "Azure",
  "AWS",
  "Containers",
  "Python",
]
Categories = [
  "Architecture",
  "Azure",
  "AWS",
]
title = "First Look at Azure Container Instances"
publishdate = "2020-10-14T09:17:47-07:00"
date = "2020-10-14T09:17:47-07:00"
type = "blog"
[image]
    feature = "/images/azure-container-instances/first-look-azure-container-instances.png"
    postheader = "/images/abstract-6-short.png"
+++

I recently spent quite a bit of time setting up an Amazon Elastic Container Service cluster on AWS. Creating all the required resources from scratch was a bit more involved than I'd hope for so it left me wondering if there was a better way for developers who want on-demand containers without having to set up and manage the networking infrastructure and resources required to start working with ECS.

<!--more-->

So, I figured let's give Azure Container Instances a try! I think it worked out pretty well. Let's take a closer look, if you prefer the Tweet-consumable version of this, I also made a thread on my experience with it:

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">Tweeting my first look at <a href="https://twitter.com/Azure?ref_src=twsrc%5Etfw">@azure</a> container instances, I was fed up with having to literally create dozens of <a href="https://twitter.com/hashtag/AWS?src=hash&amp;ref_src=twsrc%5Etfw">#AWS</a> networking resources to get a single ECS container running so let&#39;s see how <a href="https://twitter.com/hashtag/Azure?src=hash&amp;ref_src=twsrc%5Etfw">#Azure</a> does it!</p>&mdash; Fernando (@fmc_sea) <a href="https://twitter.com/fmc_sea/status/1313634251196096512?ref_src=twsrc%5Etfw">October 7, 2020</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

## Assumptions

For this demo, I assume you already have an Azure subscription and you have the Azure CLI setup on your machine and that you have a little experience with Docker but I don't directly use it in this guide.

## Creating Our First Azure Container Instance

The first thing we do is to create a resource group that our container will be put in. We can do this from the Azure CLI with:

```bash
az group create \
--name fmc-rg-container-instances \
--location westus
```

Then I just create the container? After coming from ECS that seems... too easy? But let's try it using the default image that Azure gives us in the docs and trying to claim my snazzy `fernando` subdomain:

```bash
az container create \
--resource-group fmc-rg-container-instances \
--name fmc-container \
--image mcr.microsoft.com/azuredocs/aci-helloworld \
--dns-name-label fernando \
--ports 80
```

For me, the command stalled for about 60 seconds before it provided a `- Running ...` prompt to me. Seems like a weird experience to wait on a CLI command, I'd expect it to register the command and then I could check on the status but I supposed this isn't a bad thing as I'm probably going to want to know when it finishes. 

Presumably there's some sort of `--dont-show-me-the-status-just-create-it-and-ill-check-later` flag I don't know about to solve problems with scripting this out. What's that you say? It's `--no-wait`? Ahh. Well, yeah I suppose that makes more sense.

One confusing part is that the documentation says I'm supposed to check on the result with this:

```bash
az container show \
--resource-group fmc-rg-container-instances \
--name fmc-container \
--query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" \
--out table
```

So this part was a bit confusing. Do I need to wait and then run the command? Or can I just wait as it seems like it's ready when it finishes?

When I do run the show command it returns the ProvisioningState of my container:

```
FQDN                               ProvisioningState
---------------------------------  -------------------
fernando.westus.azurecontainer.io  Succeeded
```

HAH! I totally snagged the `fernando` subdomain - Sorry other Fernandos in the West US - I GOT HERE FIRST! With this provisioned, let's check it out.

![Screenshot of Azure Container Instance Demo App](/images/azure-container-instances/azure-container-instance-demo-app.png)

A beautiful, pretty boring demo app. But there is some magic in that I only had to run **two commands** to get it up. 

Getting this same damn thing with AWS ECS took me about twenty minutes of configuring a VPC, Subnets, Security Groups, Internet Gateway, ECS Cluster and Task and some other things I probably forgot.

## Customizing Our Azure Container Instance

Ok so what if I want something more interesting in here though. I've been playing around with [Theia](https://github.com/eclipse-theia/theia) recently for other tinkering so let's see how hard it is to get a custom Docker image up as a Container Instance.

We could probably just run the same commands as before and replace the image name with the Docker image name I want right? Let's give it a shot:

```bash
az container create \
--resource-group fmc-rg-container-instances \
--name fmc-theia-container \
--image theiaide/theia-python:latest \
--dns-name-label fernando-theia \
--ports 80
```

After 3 minutes and 35 seconds (yes I timed you Azure Container Instances team!) the CLI told me the container instance was up and running. To get the new domain name again I used: 

```bash
az container show \
--resource-group fmc-rg-container-instances \
--name fmc-theia-container \
--query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" \
--out table
```

And here we are:
```
FQDN                                     ProvisioningState
---------------------------------------  -------------------
fernando-theia.westus.azurecontainer.io  Succeeded
```

Unfortunately, I clearly don't know how to use Theia at all so loading the container at that URL timed out. Possibly because I need to configure a different port. 

From what I can tell, there is no `az container update` which is mildly confusing but I supposed I can just try the create command again with a new port number and hope for the best!

```
az container create \
--resource-group fmc-rg-container-instances \
--name fmc-theia-container \
--image theiaide/theia-python:latest \
--dns-name-label fernando-theia \
--ports 3000
```

I love it when guesses work out! It appears like this operation worked and it only took ~10 seconds as opposed to the actual creation of the container instance which was a few minutes.

### Checking Azure Container Instance Logs

Unfortunately, even though my configuration now appears to expose TCP traffic on port 3000 I'm still unable to get any response. Let's see what's happening under the hood:

```bash
az container logs --resource-group fmc-rg-container-instances \
--name fmc-theia-container
```

Ahh the logs! Here's what's going on:

```bash
yarn run v1.22.4
$ /home/theia/node_modules/.bin/theia start /home/project --hostname=0.0.0.0
root INFO Configuration directory URI: 'file:///root/.theia'
root INFO Configuring to accept webviews on '.+.webview..+' hostname.
root WARN The directory referenced by local-dir:/root/.theia/plugins does not exist.
root WARN The directory referenced by local-dir:/root/.theia/extensions does not exist.
root INFO Theia app listening on http://0.0.0.0:3000.

... There were more logs but I cut them out because you can only look at so many lines

root INFO Deploying backend plugin "xml@1.39.1-prel" from "/home/theia/plugins/vscode-builtin-xml/extension"
root INFO Deploying backend plugin "yaml@1.39.1-prel" from "/home/theia/plugins/vscode-builtin-yaml/extension"
root INFO Deploying backend plugin "EditorConfig@0.14.4" from "/home/theia/plugins/vscode-editorconfig/extension/out/editorConfigMain.js"
root INFO Deploying backend plugin "python@2020.1.58038" from "/home/theia/plugins/vscode-python/extension/out/client/extension"
root INFO Deploy plugins list took: 337.3 ms
```

Basically, it appears like Theia is configured with 0.0.0.0 with port 3000 and from the command I ran earlier I know port 3000 should be exposed? This, in combination with the long list of installation logs and out means that in all likelihood I just didn't wait long enough for all these dependencies to install. 

After visiting the link again I now have the Theia container running and functioning as I expected:

![Screenshot of Theia container running in my browser](/images/azure-container-instances/theia.png)

It comes prebuilt with Node and Python, perfect for some messing around with and integrating in later projects.

### Custom Port Mapping

But what if I'm not a fan of making myself go to port 3000 when I visit this? In other systems I might be able to set up some port mapping from port 3000 to port 80 so I can visit the domain without the port suffix. 

Unfortunately, it appears in [this Azure Feedback post](https://feedback.azure.com/forums/602224-azure-container-instances/suggestions/34082284-support-for-port-mapping) that there is no support for anything other then symmetrical port mapping. This means I either have to modify my container or be satisfied with port 3000. Ahh well! Another day.

## Cleaning Up

Finally, time to make sure I clean up my resources so I'm not paying for them:

Delete the theia container group:
```bash
az container delete \
--name fmc-theia-container \
--resource-group fmc-rg-container-instances
```

Delete the first demo container group:
```bash
az container delete \
--name fmc-container \
--resource-group fmc-rg-container-instances
```

Delete the resource group:
```bash
az group delete \
--name fmc-rg-container-instances
```

Remember to answer `y` to those prompts.

## Closing Thoughts

I found getting started here pretty fun. I do have some option questions that I might dive into in later posts. For example:

1. What is the distinction between containers and container groups? How do these function together/separately?
2. Are there other resources here that I need to be aware of in order to clean them up after? (Hopefully no?)
3. How do I integrate applications in these containers with other Azure services?

Overall though, I find this model of deploying containers a lot more intuitive and simple to get started with. Do you want to see more about Azure or about Azure Container Instances? Leave a comment below or reach out to me and let me know!
