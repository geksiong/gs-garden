---
title: Which Java Should I Use?
description: The Java landscape nowadays is more complicated than in the good old days. These are my notes to make sense of the confusing choices out there.
date: 2020-08-06
updated: 2020-11-15

tags:
  - java
---
# Which Java Should I Use?

> The Java landscape nowadays is more complicated than in the good old days. These are my notes to make sense of the confusing choices out there.

## TLDR;

> I would say use AdoptOpenJDK's builds (https://adoptopenjdk.net), unless you are working for a client who can only use certified binaries, need commercial support, or has to use a legacy version of Java.
>
> But most of us probably has Oracle's OpenJDK builds installed on our machine.

## The Many Flavours of Java

> Actually, _all_ Java source codes start with **OpenJDK**. However, this does not mean that all the Javas out there are the same.

### OpenJDK

- Not named "Java" because the name is owned by Oracle. To be named "Java" it needs to pass Oracle's TCK certification to qualify for the name. Hence this is just the "JDK".
- OpenJDK is licensed under GPLv2 with linking exception.
- Website: https://openjdk.java.net
- You will notice though that the website just points you towards Oracle's OpenJDK builds (see below). _There are no instructions for getting older OpenJDK on Windows & Macs_

### OpenJDK builds by Oracle

- Website: https://jdk.java.net
- This is OpenJDK built by Oracle, but not branded Oracle. Oracle JDK is actually something else.
  - available for Windows/Linux/MacOS, and _sometimes_ Alpine Linux
- Only the current version is available on the website. **No LTS**.
- the Linux builds downloadable here are just _"Reference Implementations"_
  - just get them from your distro's offiicial package manager
- I believe the major distros are all bundling Oracle's OpenJDK builds (see below)
- As of Java 11, the code base is identical to Oracle JDK (not exactly true, read below)
- According to Wikipedia, these are supposedly TCK tested _(I'm not so sure about this though. If someone finds definitive proof of this please let me know)_
- Free for personal use or training. **No commercial support**

### AdoptOpenJDK

- Website: https://adoptopenjdk.net/
- AdoptOpenJDK is founded by a group of Java User Group members, which includes IBM, Microsoft, Pivotal, Red Hat, etc
- Two engines: Hotspot vs OpenJ9
  - OpenJ9 is apparently more memory-efficient for production use?
  - _need to study this further_
- There is Long Term Support, e.g. Java 8 and 11 are LTS
- You can use this in production, optional commercial support available from IBM
- _Note that these are not TCK certified_
- It makes sense to me to go with this, due to the LTS

### OpenJDK builds by Other vendors

- From Azul Zulu, Amazon Corretto, SapMachine, etc
- Offers certified binaries and various support/maintenance options

### Oracle JDK

- Oracle branded version, with commercial support
- You _cannot_ use this in production without purchasing commercial support
- From Java 11 onwards, is _"essentially"_ identical to OpenJDK
  - but _there are some differences_. Some are intentional (and cosmetic), while some are Oracle's own decision to implement first while still discussing with OpenJDK contributors
    - details here: [https://blogs.oracle.com/java-platform-group/oracle-jdk-releases-for-java-11-and-later](https://blogs.oracle.com/java-platform-group/oracle-jdk-releases-for-java-11-and-later)

## Installing AdoptOpenJDK

- I believe the major Linux distros are actually distributing Oracle's OpenJDK builds in their package repositories. AdoptOpenJDK has its own rpm and deb repositories.
- On MacOS, "java" and "openjdk" on Homebrew also installs Oracle's OpenJDK builds. There is another adoptopenjdk cask on Homebrew. As with many homebrew packages, they are packaged by contributors, not official.
- Note that AdoptOpenJDK 's Alpine Linux (and other OS) images are unofficial. The only official ones and based on Ubuntu and Windows.
  - This may be a potential issue for some customers?

## App Server Support

> I'll just focus on Apache Tomcat here.

- Tomcat 8.5 - Java 7 and later
- Tomcat 9 - Java 8 and later
- Tomcat 10 (currently still alpha) - Java 8 and later
- So the current generation of Tomcat still can run on Java 8

## Where's the JRE?

- In the past, we develop using JDK and deploy using only the JRE
- However now the trend (since Java 9 and above) is to distribute only the JDK
  - the JRE might still be available from some distros, you will need to check
  - you can't get the JRE from the JDK easily anymore, as the directory structure has changed

## Java in Containers

- Java 8 originally is not Container aware
  - It sees all of the host's CPU cores and memory. This can make apps make wrong decisions when in containers. In particular, reported memory affect GC.
  - _Docker support was backported into Java 8 (OpenJDK 8u192 onwards)__
- Java 9 supports the same Docker CPU and memory limits directives
  - _need to set them yourself_, otherwise you'll get the Java 8 behaviour
- Java 10 made some further changes
- Instructions:
  - If you are running Java inside a Docker container, make sure that you have Docker memory limits _and_ limits in the JVM or a JVM that understands these limits.
  - If you’re _not able to upgrade your Java version set your own limits using  `-Xmx`_.
  - For **Java 8**and**Java 9**, update to the latest version and use: `-XX:+UnlockExperimentalVMOptions -XX:+UseCGroupMemoryLimitForHeap`
  - For **Java 10**, make sure it understands the `UseContainerSupport` (update to latest) and just run it.
  - For **OpenJ9** (for now) set the limits using `-Xmx`, but soon there will be a version that understands the `UseContainerSupport` flag.
- **TODO: to update for Java 11**

## Difference between Java versions

> Below are some notable differences between the JDK versions. I'm not listing everything here. Just some info I gathered from various articles.

- Java is backwards compatible, what you know now can definitely still be used in future versions
- Below are some notable new features brought by each version of Java. Note that there are also removed features, which may affect your decision if you depend on them.
- **Java 8** (many changes)
  - lambdas
  - method references
  - repeating annotations
  - default methods for interfaces
  - Stream API
- **Java 9** (also quite big)
  - New helper methods for Collections
  - New methods for Streams
  - Optionals get the `ifPresentOrElse` method
  - Private methods in Interfaces
  - Improved try-with-resources statement
  - Extensions to the diamond operator
  - JShell - a REPL interface for Java
  - New HttpClient (but preview only)
  - Project Jigsaw
    - possible to package one .jar file containing different classes for different JVM versions.
- **Java 10**
  - `var` keyword (also called local-variable type inference)
    - only applies to variables inside methods
- **Java 11**
  - New methods for Strings & Files
  - Ability to run Java source files without compile step (moving towards scripting?)
  - `var` can now be used in lambda parameters
  - Java 9's HttpClient is final
  - Flight Recorder, No-op GC, Nashorn JS engine deprecated
- **Java 12**
  - Unicode 11 support
  - New switch expressions
  - ...and others
- **Java 13**
  - Unicode 12.1 support
  - Preview only
    - switch statement can return a value
    - multiline strings

## Tooling Considerations

- Some build tools initially had bugs with Java 9 and above, make sure you update your tooling
- Hopefully your client doesn't limit your choice of tools.

## What about Java EE?

- Open source version has since moved to **Jakarta EE** (not to be confused with Jakarta Project which includes Apache Tomcat )
- To develop for Java EE (or Jakarta EE) you need an application servr like JBoss or Glassfish
  - Apparently when you download Oracle Java EE you basically get Glassfish

## What about Kotlin?

- Not a Java transpiler! It compiles directly to the JVM
- Produces Java 6 bytecode by default
- Currently can target Java 8 to 13
- I'm not aware of any Kotlin-to-Java transpiler at this moment.

## Migrating from Java 8 to Java 11

> Note: it may not be the case that your existing Java 8 app will run on Java 11
>
> It is generally acknowledged that eventually all apps will need to migrate to java 11

- [https://docs.microsoft.com/en-us/azure/developer/java/fundamentals/reasons-to-move-to-java-11](https://docs.microsoft.com/en-us/azure/developer/java/fundamentals/reasons-to-move-to-java-11)
- https://docs.microsoft.com/en-us/azure/developer/java/fundamentals/transition-from-java-8-to-java-11

## Conclusion

- Since AdoptOpenJDK offers LTS versions of the JDK, it makes sense to go with them.
- On Linux, using a particular (Oracle) OpenJDK version shouldn't be a problem since you can always get a particular version from the package repositories. On MacOS there's Homebrew.
- Windows users are in a bit of a quandary since Oracle only publishes the current version. Not saying there are no ways to get the version you want, but please know your sources.

## References

- [https://dzone.com/articles/a-guide-to-java-versions-and-features](https://dzone.com/articles/a-guide-to-java-versions-and-features)
- [https://dzone.com/articles/when-will-java-11-replace-java-8-as-the-default-ja](https://dzone.com/articles/when-will-java-11-replace-java-8-as-the-default-ja)
- [https://blogs.oracle.com/java/java-on-container-like-a-pro](https://blogs.oracle.com/java/java-on-container-like-a-pro)
- [https://blog.softwaremill.com/docker-support-in-new-java-8-finally-fd595df0ca54](https://blog.softwaremill.com/docker-support-in-new-java-8-finally-fd595df0ca54)

—

## Misc

### Comparing Docker base images sizes

- adoptopenjdk/openjdk11:alpine - 342 Mb
- openjdk:15-alpine3.11 - 341 Mb
  - there is almost no difference between Oracle's & (unofficial) AdoptOpenJDK's build (note we are comparing JDK11 vs JDK15)
- adoptopenjdk/openjdk11:alpine-jre - 149 Mb
  - bundling just the JRE saves almost 200 Mb
- Google's "distroless" java 11 image - 196 Mb
  - about 50Mb more than alpine's JRE
  - Google uses debian 10, their base image is about 17 Mb, so Java takes up about 180 Mb
  - i think this might be a JRE instead of a JDK. Bundling the JRE also makes sense as this is for running in prod only.
- comparing against node. node 14's alpine image is 117 Mb.
