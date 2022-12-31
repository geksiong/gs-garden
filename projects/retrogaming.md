---
title: Retro Gaming
description: Project notes on building a retro gaming station
date: 2022-12-31
---

## Concept

- Board: Raspberry Pi 3
- OS: RetroPie
- MHS35 3.5-inch LCD screen, via SPI connectors
- Bluetooth game controller
- USB game controller via a USB Game Control Board

## RetroPie

- Setup networking and SSH first.
- Don't meddle with the default GPU mem split, it could be counterproductive.
  - https://www.raspberrypi.com/documentation/computers/config_txt.html#memory-options
- Best themes for small LCD screens: GBZ35, GBZ35-dark, TFT
- Set the slide transition to 'Slide'. The default 'Fade' looks horrible on the LCD screen.
- Kiosk mode is good for visiting kids. Default way to get out of Kiosk mode is the Konami code.

## LCD screen

The MHS35 LCD works out of the box.

**Instructions**: http://www.lcdwiki.com/MHS-3.5inch_RPi_Display

- I used LCD35-show instead of MHS35-show and that was horrible.
- I installed the retropie version, not sure if it's any different.
- The quality is not perfect, but don't really need to bother with trying to tweak it.
- Screen has resistive touch capabilities, but I don't really need this.
- Looks like I cannot toggle between LCD and HDMI without rebooting?
- I can't find a way to programmtically power off the screen. Powering off the RPi ends up with a white screen.

## Bluetooth Game Controller

> To Do

## USB Game Control Board

Got this board many years ago. I intend to wire this up to an old cheapo 'arcade style' PS2 controller, or maybe an old gameport gamepad (can't fit the board inside though).

- I believe these is the correct guide for this board: https://www.s-config.com/zero-delay-usb-joystick-encoder/
- Looks like I can get both a D-Pad and Analog joystick. I only have wires for the analog joystick though.
- I might have an additional 4 buttons for Auto/Clear/Turbo/Mode.
