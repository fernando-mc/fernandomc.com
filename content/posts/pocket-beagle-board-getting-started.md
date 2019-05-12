+++
draft=true
Description = "Let's learn how to get up and running with a PocketBeagle beagle board."
Tags = [
  "Hardware",
  "Python",
  "Beagle Board",
  "PocketBeagle",
]
Categories = [
  "Projects",
]
title = "Getting Started with a PocketBeagle"
publishdate = "2019-05-11T13:03:03-07:00"
date = "2019-05-11T13:03:03-07:00"
[image]
    feature = "/images/beagleboard/materials.png"
+++

[Redmond Python](https://www.meetup.com/Redmond-Python-User-Group/), the Python Meetup I started in Redmond, WA was recently given a bunch of PocketBeagle Beagle Boards. So as I started playing around with them in preparation for a Meetup event to let folks do the same I decided to make a tutorial for anyone else wanting to learn about them.

PocketBeagles are "ultra-tiny-yet-complete open-source USB-key-fob computer[s]." and are a great board to get started learning hardware. You can read all about them on the [BeagleBoard](https://beagleboard.org/pocket) website but for now let's try getting started with them ourselves!

We'll be building a.............

<!--more-->

## Materials

In order to get started I needed a few different materials (pictured below):

1. The PocketBeagle
2. A MicroSD card
3. A MicroSD card reader for my computer (In my case a USB-C)
4. A MicroUSB to USB connector (I already have a USB-C to USB adapter for my mac)

![A photo of the four different materials we need for the PocketBeagle project](/images/beagleboard/full-materials.png)

With our materials all gathered we can get started setting up our PocketBeagle. 

## Downloading and Installing Our Firmware

Our furst (heh, heh 🐶) step will be to download our operating system image. The latest firmware images specifically for BeagleBoards can be found at the BeagleBoard webiste [here](bbb.io/latest). 

I downloaded the reccomended image as of May 2019:
![A screenshot showing the Debian image to download as Debian 9.5 2018-10-07 4GB SD IoT](/images/beagleboard/reccomended-image.png)

Now this can be a potentially confusing step - After we've downloaded this file we will need to uncompress it to the actual firmware image. My compressed image was around 515 MB and my uncompressed image was around 4-5 GB. 

![A screenshot showing the compressed and uncompressed images as they appear on a Mac](/images/beagleboard/disk-images.png)

After we have the uncompressed image (that ends with `.img`) we can insert our MicroSD card into our card reader and connect it to our computer. Then we can transfer that uncompressed image to our MicroSD card.

Then we eject our MicroSD safely from the computer, remove it from the MicroSD card reader, and connect it to the PocketBeagle.

Honestly, the PocketBeagle was so compact I didn't initially realize how to insert the MicroSD card at first. So here's an example:

![A photo showing how to insert the MicroSD card to the PocketBeagle](/images/beagleboard/sd-inside-pocketbeagle.png)

Now after the MicroSD card is inserted you can also connect the MicroUSB connector (shown above) and connect it to your computer. The 





