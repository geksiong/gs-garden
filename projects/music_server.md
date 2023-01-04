---
title: Music Swerver
description: Project notes on building a music server
date: 2022-12-31
updated: 2023-01-04
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
  - "Now Playing" views
- Jivelite doesn't save settings automatically. You need to go into "Settings" > "piCorePlayer" > "Save Settings to SD Card".

### Inverting the Display

- I decided to invert the display, and then the touchscreen threw me off as it wasn't inverted together with the display. To fix, change Jivelite to not autostart, recalibrate the touchscreen, and set Jivelite back to autostart again.


### Unicode Fonts

#### The Hard Way (My Way)

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

#### The Easy Way (not tested)

Under the "Advanced" menu there is an "Applet Installer". One of the applets is a Font Replacer which is supposed to replace the font with a merged FreeSans + Droid Sans font. 

### Others

- The spectrum analyzer view seems unstable, however the music playback is not affected and LMS web page is still working fine. But it could be due to the VU Meter view which is not supported by the skin - after I disabled the VU Meter view, the spectrum analyzer seems to work fine.
- "Power off" only powers off the LMS(?) service, not shutdown the Raspberry Pi! I don't think it's safe to pull the plug with this? 

## Now Playing

### VU Meters

The VU Meters will not work out of the box. Need to install the VU Meter packages that end with 'wav35skin'. Even then, I found that only 'Logitech Black-wav35skin' works. Fortunately that one looks pretty good.


## Buttons and Rotary Encoders

I decided to add some physical controls to the setup. Someone has already written a guide here: https://docs.picoreplayer.org/projects/control-jivelite-by-rotary-encoders-and-buttons/. However, the guide has a very critical error which caused me to spend hours to troubleshoot. Turns out the sbpd command should be run in daemon mode with the `-d` parameter and that's missing from the guide. Also, those comments in the sbpd command shouldn't be in the actual script.

### Wiring

As I have both a DAC and LCD, this means most of the pins are used up. My DAC has pass through pins, and the LCD uses a 26-pin connector, I simply connected the LCD on top of the DAC. Following the guide above, this leaves only pins 29-34 free, just enough to wire up one push button and one rotary encoder (with push button).

The rotary encoder must be one without power. Basically the one without a breakout board, with 3 pins on one side and 2 pins on the other side.

My encoder pins are too soft to plug into a breadboard so I attach them with female-male jumper wires (not very secure).

My current GPIO pins layout are:

- pin 29 (GPIO 5): Rotary Up
- pin 31 (GPIO 6): Rotary Down
- pin 32 (GPIO 12): Rotary Button
- pin 33 (GPIO 13): Push Button

#### Resources

- https://www.hackster.io/hardikrathod/push-button-with-raspberry-pi-6b6928
- https://learn.adafruit.com/rotary-encoder/hardware

> **TODO**: I can probably squeeze in another rotary encoder (or more buttons), if I wire up the LCD using individual wires instead of directly using the LCD's onboard connector.

### Packages & Script

Following the guide:

- Install the `pcp-sbpd` package (this is the only one needed, it will pull in the `pigpio` package)
- Create the file `/home/tc/sbpd-script.sh`. Replace the actions with what you need:

```sh
#!/bin/sh

# start pigpiod daemon
pigpiod -t 0 -f -l -s 10

# wait for pigpiod to initialize - indicated by 'pigs t' exit code of zero

count=10 # approx time limit in seconds
while ! pigs t >/dev/null 2>&1 ; do
        if [ $((count--)) -le 0 ]; then
                printf "\npigpiod failed to initialize within time limit\n"
                exit 1
        fi
#       printf "\nWaiting for pigpiod to initialize\n"
        sleep 1
done
printf "\npigpiod is running\n"

# load uinput module - required to be able to send keystrokes
# then set the permission to group writable, so you don't need to run sbpd with root permissions
sudo modprobe uinput
sudo chmod g+w /dev/uinput

# The full list of Jivelite key commands can be found here:
# https://github.com/ralph-irving/tcz-lirc/blob/master/jivekeys.csv

# Button 1                                              # button-section, defines the GPIO and key-commands
SW1=13                                                  # GPIO (BCM, not Board)
SH1=KEY:KEY_LEFTBRACE                                   # key-command for SHORT press (Now Playing)
LO1=KEY:KEY_POWER                                       # key-command for LONG press (Power)
LMS1=250                                                # milliseconds for long press

# Button rotary 1
SW4=12
SH4=KEY:KEY_ENTER                               # key-command for SHORT press (Enter)
LO4=KEY:KEY_BACK                                # key-command for LONG press (Back)
LMS4=250

# Encoder Rotary 1
EN1_UP=5
EN1_DN=6
EN1_CMD=KEY:KEY_UP-KEY_DOWN

CMD="sbpd -d -f /home/tc/sbpd_commands.cfg \
b,$SW1,$SH1,2,0,$LO1,$LMS1 \
b,$SW4,$SH4,2,0,$LO4,$LMS4 \
e,$EN1_UP,$EN1_DN,$EN1_CMD,4"

echo $CMD
$CMD > /dev/null 2>&1 &
```

  - Use the key names documented here: https://github.com/ralph-irving/tcz-lirc/blob/master/jivekeys.csv (I think they work because LIRC is provided by default).
  - You can also supply a `sbpd_commands.cfg` file to contain aliases for the keys. This is not really needed.
  - For more information, checkout sbpd's GitHub repository: https://github.com/coolio107/SqueezeButtonPi-Daemon
- Make the script executable: `sudo chmod +x /home/tc/sbpd-script.sh`
- In the pCP web GUI, go to "Main" > "Tweaks" and set "User Commands" to run the script on boot.

### Notes

- The encoder doesn't always register. This is actually documented at sbpd's GitHub repo.
