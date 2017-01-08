+++
draft = false
title = "AWS Workspaces - How Not to Buy a Windows Notebook"
Description = ""
Tags = [
  "Serverless",
  "Python",
  "AWS",
  "AWS Workspaces",
  "Windows 10",
  "Testing",
]
Categories = [
  "AWS",
]
menu = "main"
date = "2017-01-11T14:08:12-05:00"

+++

A few days ago I needed to test a Lambda function build script for my recent Pluralsight course on a Windows machine. One of my course viewers notices that the bash build script I'd written for unix environments was incompatible with Windows bash. Because of this I needed to write a custom .bat script for windows environments.

Only one problem, I don't own a Windows machine, and I don't want to.

So what were my options? I could:

1. Buy a $130 Windows netbook I saw for sale. But I don't want _another_ device.
2. Dual boot Windows on my personal machine. Not a bad option but a little time-intensive to get going. 
3. I could install a Windows VM on something like Virtual Box. Also not bad but I don't want a 20GB download that I have to keep around.
4. Use something I discovered when searching for Cloud VMs - Amazon Workspaces.

Partly because it was the option I hadn't thought of yet, and partly because I _really_ didn't want to wait two hours for the 20GB download I opted to try out [AWS Workspaces](https://aws.amazon.com/workspaces/).

**AWS Workspaces**

With some oversimplification - AWS Workspaces are essentially the same user experience as a local virtual machine except you connect to them with a local client

Within a few minutes in the AWS console I spun up a user account for a Windows 10 machine with the cheapest pricing plan available.

I opted for the Value Bundle with 1 vCPU, 2 GB Memory, and 10 GB User Storage. You can choose other bundles with addtional resources, but my needs only required enough memory for Python, Pip, a few dependencies, and web browsing. As of this post AWS offers two ways to pay for the workspace. You can either choose to pay a flat fee (in my case $25/month) or pay a smaller monthly fee and an hourly surcharge ($7.25 + $0.22/hour for this bundle).

Unfortunately, I made the mistake of signing up with the $25/month package before switching over to the smaller monthly fee and hourly rate. Still, I ended up saving $105 against the laptop I almost purchased and a few hours waiting for the download of a Windows 10 VM. Both of which I consider a win.

After I created the workspaces account in the AWS console it took about twenty minutes to download the workspaces client and get up and running inside my Windows 10 VM.

Now, compared to the sizeable memory of my phyiscal machine it felt a bit slow, but in all fairness it was pretty darn cheap because I didn't pay for performance. After debugging a few windows errors I installed python and was able to test and debug my batch scripts in the Windows 10 environment.

**Conclusions**

Of all the options you might have for testing on a Windows machine without atually owning one I'd say that using AWS workspaces is one of the top two. 

If I'd planned ahead I think I would have been equally satisified using a Windows VM on my own machine, but given the time investment the AWS workspaces route was a better fit for me.

Do you have better ways to test in other environments?