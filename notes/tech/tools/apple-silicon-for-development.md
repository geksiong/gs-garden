---
title: Web & Mobile Application Development on Apple Silicon
date: 2021-06-06
tags:
  - macbook
---

## Introduction

Recently someone asked me whether my developers can use Apple M1 Macbooks for development. Macbooks have always been a favourite for developers, as they are at heart a Unix environment, and it is a no-brainer decision if you are in mobile application development. The popularity if Macbooks are such that I've even started to see open-source libraries being 'Mac first'.

The M1 Macbooks have been available since end-2020, so let's review the current state and what it means for web and mobile developers.

> Disclaimer: This is not a How-To guide. I'm still using an Intel Macbook myself, so I don't have first-hand experience. This discussion is just based on the literature so far that I can find at this point in time. I may be incorrect and things may have changed by the time you are reading this article. I may update this article as I find more information.

## Native vs Emulation

For native Mac apps, the current version of XCode now can cross-compile to both Intel and ARM architectures, and package them together using the new 'Universal 2' application format.

Universal 1 was actually for PowerPC and Intel, so this is not the first time that Apple had multiple architectures. However, this is probably the first time that Macs are the de-facto choice for most developers.

However, many of our web development tools are not native Mac apps, as many came from the Linux world with the objective to run on Linux servers. The current approach seems to centre around using Rosetta 2.

## Development Tools

### Google Chrome

Chrome is available for M1, and performance is faster than running on Rosetta 2 emulation.

### Electron Apps

Many popular desktop apps such as Slack, WhatsApp and VSCode are actually Electron web apps running in an embedded Chromium browser. Apple Silicon support was introduced since Electron 11.0 in Nov 2020, and they fixed a couple of issues in 12 and 13. Performance on ARM would depend on the version of Chromium.

### VSCode

VSCode has been available in stable for Apple Silicon since Feb 2021 with version 1.54.

### Homebrew

Homebrew 3.0.0 (released 5 Feb 2021) added official support for Apple Silicon, and you can install the ARM version under `/opt/homebrew`. However, not all formulae supports Apple Silicon yet, so for now the recommendation is to run Homebrew under Rosetta 2 using the `arch -x86_64` flag.

### Java

Oracle's JDK will run under Rosetta 2, but it appears they do not have native builds for Apple Silicon. To get native Apple Silicon support, currently you can install Azul's Zulu OpenJDK builds.

### Node.js

Node.js introduced Apple Silicon support with Node.js 16 on April 2020.

I might be tempted to think that Node.js development would be fine, but I'm also aware that there are some node_modules that on `npm install` will invoke gcc to build native dependencies, so there is still a risk that something breaks.

### Mobile Development

**XCode** is not an issue, of course.

**fastlane** should work, although I've read some users had problems initially which seems to stem from the underlying Ruby version used.

**Android Studio** support for Apple Silicon is still preliminary (currently Android Studio Arctic Fox Canary 15). The Intel version works with Rosetta 2 however.

**React Native** should work as reported by some. But some Pods may not be ready for Apple Silicon, or may need to be updated. Also, some recommendations I've read include clearing the DerivedData folder and performing a clean build.

However, I've also read some people having problems with YogaKit (the layout engine in React Native), and the solution apparently is to run XCode with 'Open with Rosetta' set to true, which contradicts other reports.

**Flutter** support for Apple Silicon is still in development, but apparently works via Rosetta 2.


## Virtualization

VirtualBox doesn't on Apple Silicon yet. Neither does VMware. This may be a major issue for some development teams whose workflows require running VMs locally.

There is apparently a Mac hypervisor called UTM based on QEMU that works. However, based on what I've read it runs only ARM images - the use case is to run ARM versions of Linux (and Windows) on your ARM Mac.

## Docker

There are two things about Docker to note here: there is the Docker tooling itself, and there is the building and running of Docker container images.

Docker Desktop for Mac is now available as a GA release, but you will still need to install Rosetta 2 as some binaries are still for Intel.

As for Docker images, the documentation says you can build and run images for both x86 and ARM architectures.

You can cross-compile to different architectures using the experimental `buildx` feature. But I don't think this would be a typical use case for most development teams, as the building of container images would be handled by the CI/CD pipelines.

The main use case for most teams would be to run existing Intel-based images under emulation e.g. to run dependent middleware or services for local testing, or for development inside the container. This can be achieved by adding `--platform linux/amd64` when running the container. However, Docker warns that running Intel-based containers under ARM is on 'best effort' basis only, and they may crash under emulation.

One known issue is `inotify` does not work under QEMU emulation. `inotify` is used for filesystem change notifications, and may be a deal-breaker for some applications. Another known issue is occasionally data drops.

Running Intel-based containers under ARM emulation also slower and use more memory. This could be another deal-breaker if you need to run your entire container stack locally for development and testing, or perhaps it already is, since there are no M1 Macbooks with more than 16 Gb RAM yet.

Docker recommends using ARM64 images whenever possible, and they expect more images to be built for ARM over time, so this issue will go away. However, I think they are the missing the point here about what developers need, although I can't really fault them for the current state of affairs.

### Kubernetes

There are instructions for Apple Silicon in the Kubernetes documentation, so I think it works. However, the caveats should be the same as for Docker.

## Summary

It is indeed possible to use an Apple M1 Macbook for development, and many people have done it, but you will need to know how to navigate the native vs emulation issues, and adapt your workflows accordingly. But your mileage may vary depending on the languages and frameworks you use.

In my view, the greatest problem would be running VMs and containers. It is a fact that most of the enterprise server world are currently on x86_64 architectures, and although ARM servers are available they are not mainstream. Development teams who need to work with VMs and containers may need to adapt their workflows if they want to use Apple M1 Macbooks for development. Furthermore not every developer might understand the implications of different CPU architectures. Hence, I'm currently hesitant to recommend using Apple Silicon for professional development work.
