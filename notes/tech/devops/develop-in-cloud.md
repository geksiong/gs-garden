---
title: Developing in the Cloud
description: How mature is the concept of doing development for the cloud, in the cloud?
date: 2023-07-05

tags:
  - cloud
  - ide
  - devops
---
*Ongoing work in progress*

## Background

For the longest time we have always been developing and testing apps directly on our laptops, and deploying them to the remote environment for further integration testing. Working on multiple projects locally requires developers to juggle different middleware and their configured ports, versions of tools, and problems such as "it works on my machine" were common. Years ago, an idea started to float around, that we should be able to provision each developer a standardised development environment, all setup and ready for use.

As cloud technology matures, how we are doing development has also been evolving. But have we arrived at the original yet?

## Virtual Machines

- Just distribute VMs
- But your IDE is outside the VM (assuming you want to use GUI and not going for VNC or remote X Windows)
- Luckily many IDEs now have remote development capabilities, whether it is just modifying files via ssh or bind mounts, or with a full IDE server running inside the VM.

## Containers

- Instead of installing and running dependencies directly, you can just pull and run different runtime dependencies with tools such as Docker Compose.
- Some people run Kubernetes Pods, same concept.
- Easy to maintain dependencies.

## Devfiles

- What if we want to do the development on a cloud server instead of on our laptop?
- Devfiles is an initiative by RedHat, AWS, JetBrains
- Basically a single file can provision you a remote env with everything you need.
- Sounds a lot like Docker, except that the goal here is not to run it on your laptop but in the cloud.

## Cloud IDEs

- All these while your IDE is still running locally.
- IDE vendors are moving their IDEs into the browser already.
- So the future looks like I don't need to install anything on my laptop anymore. IT support also don't have to worry about us 'hackers' installing weird apps on our machines. We just need a web browser.
