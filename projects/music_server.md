---
title: Music Swerver
description: Project notes on building a music server
date: 2022-12-31
---

## Concept

- Board: Raspberry Pi 2
- OS: piCorePlayer
- Hifi DAC Hat from Taobao: https://item.taobao.com/item.htm?spm=a1z09.2.0.0.3c052e8dYwoG66&id=564203353411&_u=v1qg627kd223

Future ideas:

- LCD screen, via Jivelite
- Rotary encoder and buttons for navigating the LCD display

## piCorePlayer

piCorePlayer consists of a server and player:

- Player: Squeezelite
- Server: Logitech Media Server
- Installed the web interface. Default port is 9000.
- I mounted the music library from my Synology NAS, via CIFS interface.

piCorePlayer does not have the ability to stream music to the web browser. It is designed to output to loudspeakers.

## DAC

It's a HiFiBerry DAC clone. Configure in Squeezelite as HiFiBerry DAC Zero/MiniAMP. Sound is good.

- Realised only later that the location of the connectors are on the short side of the Pi, and there are no cases to accomodate it. Likely I'll have to place everything inside a DIY enclosure.
