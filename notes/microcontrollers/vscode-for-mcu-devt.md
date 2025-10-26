---
title: Using VS Code for Microcontroller Development
date: 2023-03-14
---
## VS Code and PlatformIO extension

I've been using Arduino IDE for my experiments with the NodeMCU board, but the developer experience pales in comparison with other IDEs I've used. Since I use VS Code for most of my coding, I tried the PlatformIO extension and find it to be a much better replacement.

With PlatformIO I can:

- search for various boards and libraries without any further configuration
- view information about the libraries e.g. code examples, files, changelog, before you install
- only need to link on a rebuild which is so much faster. For some reason Arduino seems to rebuild everything.
- all the nice VS Code editor features such as git integration, quickly jumping to functions, IntelliSense, plus all the plugins I use such as Vim emulation, custom syntax highlighting, etc

Things I haven't tried yet:

- uploading and debugging. This is not available on Arduino IDE 2.0, and I really want some help to explain the crash dump.

### Convert .ino to .cpp

VS Code does not have support for .ino files, so need to convert to .cpp.

Arduino IDE actually included some magic for easier C/C++ coding experience. The main one that affected me is that Arduino IDE allows functions to be declared after they are used, so need to re-organise the code a bit.

### Debugging

Coming soon

### Configuring SonarLint extension

I already have the SonarLint extension installed.

To configure SonarLint:

- PlatformIO > Advanced > Compilation Database
- Shift + Ctrl + P (or your platform equivalent): > SonarLint: Configure the compilation database for C and C++ analysis - it should list the file generate from the step above

The thing I'm not so sure about is that SonarLint seems to advocate using C++ standard libs over old-school C codes. And I've read somewhere that sometimes C++ features are not so good for MCU development due to their limited memory. More investigation is needed here.

## VS Code and CircuitPython

I've been using Thonny for CircuitPython development for the Raspberry Pi Pico too, and plan to try out VS Code CircuitPython. Installing the extension is straightforward. Will log my experiences here in the future.
