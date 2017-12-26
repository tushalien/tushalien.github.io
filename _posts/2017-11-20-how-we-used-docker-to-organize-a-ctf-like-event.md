---
layout: post
title:  How We used Docker to Organize a CTF like Event
snip:   The contest was all about solving challenges based on Linux, networking and basic scripting.
---




## *Overview of the Contest*

The contest was all about solving challenges based on Linux, networking and basic scripting. There were a few questions on reverse engineering too. ( we call it RevEngg :P) .The teams were expected to work and execute commands as if it were their own system. Also, we wanted to keep the working environment of each team completely separate from others so that a particular change by a team is not reflected to others.

Say it our ignorance or involvement in other events, we hardly got 15–18 hrs to setup our server and test it. For those who are not from this field, it might sound like a plenty of time but trust me it’s not, especially, if it’s your first time organizing such kind of contest.

## **What Options did we have?**

We could implement the whole event in an old-school traditional way. We could create a Virtual Machine (VM) image and then install that image on all the desktops. But, the problem is with VMs are that they are actually quite slow. There are network related issues when working with VM now and then. Also, the fact that it would have taken us a long time if we were to install image on each desktop made us look for another way.. Something simpler, more efficient and meant for lazy lads :P

The other option was to create a remote server with each team having its own credentials to login to the server. At first glance, this seems to have no issue and is expected to work just fine. But, going this way meant setting up a lot of complex permissions specifying what a user should be allowed to do along with restrictive memory allocation techniques.

On top of this, if someone manages to reboot system or even just run a fork bomb, everyone is doomed!( yeah, we too!!) Moreover, one can easily distract others using commands like wall, mesg and you surely don’t want to lose your focus, not in a CTF event, at least.
> “There are endless things that can and will go wrong when sharing the same system.”

After eliminating the two possible methods, we were left with our third and perhaps the last option, ‘Docker’. So, we decided to give it a shot although we hadn’t much of an experience working on it.

## **Docker to our Rescue ..**

For those who don’t know about docker, *“Docker is a tool designed to make it easier to create, deploy, and run applications by using containers. ”* In simple words, we create one image of a program and then you can have multiple running instances of the same image known as containers each identified by a unique id. All the containers have their separate working environment unless specified otherwise.

This is exactly what we wanted, for each team to have their own working environments completely unaffected by others.We definitely have the security issues even with docker if used simply out of the box but that can be ignored given the small scale of the contest.

So first, we pulled up a base Ubuntu image from the docker hub.(Docker hub is very much like GitHub but for docker images. ) Then all we needed was to copy the required challenges from our local disk to that docker image. So basically, we ran a bunch of commands and our image was ready to be deployed. Finally, we wrote a bash script that simply checks if the current ssh session is from a certain user and starts a docker container.

This much homework was probably enough for what was a 4-hour basic event. However, we had still few hours left and after testing it a couple of times, we faced a serious issue that could very well go unnoticed, but nevertheless, we jumped to the docker documentation to find a solution.

## **Issue with Docker**

![](https://cdn-images-1.medium.com/max/2000/1*tDU_KOEbmB4wuhTsZhMUjQ.png)

As explained above, all the containers are by default volatile in nature, meaning, once you exit and remove the container, all your changes are gone. It could be quite critical in the case where challenges have a lot of steps to perform. Suppose, a team completed 4 out of the 5 steps needed to complete a challenge and then they are logged out, either by their negligence or network issue.

Docker, by default, doesn’t come with persistent storage, which presents some issues while working with containers, however, there are ways to achieve persistent storage. There are also few workarounds to what we wanted to achieve without actually employing the concept of persistent storage ( Remember we had only a couple of hours left .)

## How we solved it

We wanted the user to resume his session (container) without losing any of his changes. This meant identifying all the containers and associating them with the corresponding teams.This created a problem of identifying teams and ensuring that they are presented with the same environment even after logouts.

We then decided to use IP as an identifier for each team since the IPs were static and a team could use only one system! So, whenever a team logged in with the credentials, a script was run to check if there was already a container with the name of that IP, if it existed then we just started that container otherwise a new container was created named as “user IP” and was started. So instead of the user logging in to our actual system, we ran a docker environment so that our actual system was safe from any tampering! Also, when a container exited its state was saved unless removed from the file-system, thus giving us access to the same container and the files!

## Final thoughts…

What we implemented with Docker might not be the perfect. In fact, it is safe to say that it was nowhere close to the ideal setup. I, however, believe that coming from nowhere and with zero prior experience, we were able to make use of docker to achieve our goal in a just right way. We surely are going to work on it and plan to make it a docker-compose ready image. So, the next time, the organizing members won’t have to suffer the pain we did :P

Special thanks to [Jatin Rungta](undefined) for setting up the server and dealing with the Docker stuff..

### Thanks for reading! If you liked it, please support by clapping and sharing the post.
