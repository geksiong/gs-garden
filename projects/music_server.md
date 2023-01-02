---
title: Music Swerver
description: Project notes on building a music server
date: 2022-12-31
---

## Concept

- Board: Raspberry Pi 2
- OS: piCorePlayer
- Hifi DAC Hat from Taobao: https://item.taobao.com/item.htm?spm=a1z09.2.0.0.3c052e8dYwoG66&id=564203353411&_u=v1qg627kd223 (same item on AliExpress: https://www.aliexpress.com/item/1005004050985603.html)

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

- Realised only later that the location of the audio connectors are on the short side of the Pi, and there are no cases to accomodate them. Likely I'll have to place everything inside a DIY enclosure.

## LCD Screen via Jivelite

I used the same 3.5 inch LCD I used for RetroPie, and simply followed the instructions at https://docs.picoreplayer.org/projects/add-a-display/. Interestingly, I don't have to use the LCD-show repo - pCP seems to have support out of the box already. Framerate is probably not an issue here.

### Configuration

- You need to select the language the first time. The UI is a bit unintuitive. After selecting English language, just tap on the Back button to see the main screen.
- I accidentally set another language instead of English, but couldn't find a way to change the language.
  - The UI actually has an 'Advanced Settings' section where you can change the language. But then again, I wouldn't know how to get there in a language I don't know...
  - The nuke option is to go into the pCP web config and 'Reset' Jivelite which will clear all its configuration, then reboot. However, you will lose other settings such as the background, screensaver, etc.
- Other things to configure:
  - Wallpaper
  - Screensavers: Many things don't work. I chose "Analog Clock".
  - "Now Playing" views: The VU Meters don't work under 3.5-inch LCD screen, so disable them.
- Jivelite doesn't save settings automatically. You need to go into "Settings" > "piCorePlayer" > "Save Settings to SD Card".

### Inverting the Display

- I decided to invert the display, and then the touchscreen threw me off as it wasn't inverted together with the display. To fix, change Jivelite to not autostart, recalibrate the touchscreen, and set Jivelite back to autostart again.


### Unicode Fonts

- Jivelite's fonts do not support CJK glyphs. To support CJK, follow the instructions at https://forums.slimdevices.com/showthread.php?103330-Jivelite-for-piCorePlayer/page20 to replace the fonts.

To summarise the steps:

- Ensure you have ssh access to piCorePlayer
- Get a unicode font e.g. Google Noto Sans, or Huawei's HarmonyOS Sans. I chose HarmonyOS Sans just to try it out, and I think it looks good so I'm keeping it.
- Create a directory `/mnt/mmcblk0p2/tce/custom` (you could also just use `/mnt/mmcblk0p2/tce/optional` but I prefer to keep my custom hacks separate from the packages in there)
- scp the fonts to the above directory
- In the same directory, create `replaceJiveliteFonts.sh` with the following contents (replace font names accordingly):

```sh
#!/bin/sh
cd /opt/jivelite/share/jive/fonts
sudo rm FreeSans.ttf
sudo rm FreeSansBold.ttf
sudo ln -s /mnt/mmcblk0p2/tce/custom/HarmonyOS_Sans_SC_Regular.ttf FreeSans.ttf
sudo ln -s /mnt/mmcblk0p2/tce/custom/HarmonyOS_Sans_SC_Bold.ttf FreeSansBold.ttf
```

- Make sure you `chmod 755 replaceJiveliteFonts.sh`
- From the web gui, under "Tweaks" > "User Commands", specify the path to this file `/mnt/mmcblk0p2/tce/custom/replaceJiveliteFonts.sh` so that pCP will run these commands on boot
- Reboot and ssh in again to verify the fonts are soft-linked correctly. Test with your choice of music file with the Unicode metadata.


### Others

- The spectrum analyzer view seems unstable, however the music playback is not affected and LMS web page is still working fine. But it could be due to the VU Meter view which is not supported by the skin - after I disabled the VU Meter view, the spectrum analyzer seems to work fine.
