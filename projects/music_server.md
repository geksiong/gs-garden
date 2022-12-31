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

## LCD Screen via Jivelite

I used the same 3.5 inch LCD I used for RetroPie, and simply followed the instructions at https://docs.picoreplayer.org/projects/add-a-display/. Interestingly, I don't have to use the LCD-show repo - pCP seems to have support out of the box already. Framerate is probably not an issue here.

- The UI is a bit intuitive. After selecting English language, I had to go back to see the main screen.
- I accidentally set another language instead of English, but couldn't find a way to change the language.
  - The UI actually has an 'Advanced Settings' section where you can change the language. But then again, I wouldn't know how to get there in a language I don't know...
  - The nuke option is to go into the pCP web config and 'Reset' Jivelite which will clear all its configuration, then reboot. However, you will lose other settings such as the background, screensaver, etc.
- I decided to invert the display, and then the touchscreen threw me off as it wasn't inverted together with the display. To fix, change Jivelite to not autostart, recalibrate the touchscreen, and set Jivelite back to autostart again.
- The spectrum analyzer and VU meter seem to have a tendency to freeze Jivelite. Could be due to my using an RPi 2. The music playback is not affected and LMS web page is still working fine.
- To suppoort CJK fonts, need to install 'perl_font_freetype.tcz', then follow the instructions at https://forums.slimdevices.com/showthread.php?103330-Jivelite-for-piCorePlayer/page20 to replace the fonts.
