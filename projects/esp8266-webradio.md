---
title: ESP8266 Web Radio
description: Using ESP8266 to make a Web Radio player
date: 2023-02-26
---
## ESP8266

I bought a NodeMCU (v0.9) microcontroller dev board years ago, not really knowing what to do with it, and it has been lying around unused. It uses the ESP8266 chip. Recently I've been wondering whether I could make an Web Radio with it, maybe something simple that my mum could use.

First to sort out some common confusion with the terminology:

- ESP8266 refers to the System-on-Chip
- The ESP8266 is used in the ESP12 module
- NodeMCU is the development board that has the ESP12
- NodeMCU was actually the name of the firmware running Lua, but it has since been commonly used to refer to the development board
- Nowadays there is a much better module called the ESP32, which has a few variants and also used in a variety development boards as well.

### NodeMCU v0.9 specifications

![NodeMCU v0.9 Pinout](https://mechatronicsblog.com/wp-content/uploads/2019/01/NodeMCUV1-pinout.jpg)

- Clock speed: 80 MHz
- SRAM: 64 kB
- Flash: 4 MB
- 11 x Digital Pins, 1 x Analog Pin
- 1 x SPI, 1 x I2C, 1 x UART
- Note: the board only provides 3.3V to components, even though it take in 5V input.

> Note that I'm using Arduino IDE (C language) instead of NodeMCU (Lua language).

## Web Radio

Web Radio is probably one of the common use cases for this microcontroller. There are several tutorials out there, and it can get quite confusing.

### Audio Library

The library to use is https://github.com/earlephilhower/ESP8266Audio

It also comes with a Web Radio example, which is very helpful. In fact, you are better off following the documentation here than to try and follow some random tutorial out there.

The library supports MP3 and AAC decoding, connecting to HTTP streams (HTTPS seems to work).

### Sound Output

The library assumes an I2S interface. However, it conveniently provides a "NoDAC" option so that it can emulate a virtual I2S device and output to a speaker.

Note that the NodeMCU is not able to drive a speaker directly. You need to use an amplifier.

#### 1-bit transistor amplifier

If you don't have an amplifier module, you can also use a quick-and-dirty 1-bit transistor amplifier described, using a 2N2222 transistor, in place of the 2N3904 mentioned in the docs (they are supposed to be quite identical for the purpose) and a 1k ohm resistor.

This setup works but the sound is pretty bad.

### I2S DAC

I have a MAX98357A board so I tried that next.

The sound is much better. I used the 3.3V output instead of a separate 5V source, but it seems ok. Sound is quite loud on the 8 ohm 0.4W speaker I used.

I suppose using the I2S DAC also reduces some processing load, compared to using the "NoDAC" approach.

## Thoughts so far

- The board seems to be rather slow. The instructions say to build for 160MHz CPU, but I don't know whether that made any difference. It take some time to connect to the WiFi network and start the stream. Initially I had a lot of instability, then I got a period of long stable output. Changing radio streams can be quite unstable.
- The volume seems to work initially, but after a period of playback the volume became load again. Might be a bug with the library.
- I might try a PAM8403 amplifier board (not I2S) with onboard volume control, but that might mean more load on the CPU which might affect the playback.
- There are tutorials out there using the VS1053 MP3 decoding board. I believe that will make the playback much better (although limited to MP3). But this board is not that cheap so I'm hesitant to get one.
- Perhaps the ESP8266 is no longer a good enough device for this use case. Maybe I should upgrade to an ESP32 instead?
