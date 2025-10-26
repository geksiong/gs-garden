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

- Clock speed: 80 MHz (can increase to 160 MHz)
- SRAM: 64 kB
- Flash: 4 MB
- EEPROM: 4 Kb
- 11 x Digital Pins (see Limitations below), 1 x Analog Pin
- 1 x SPI, 1 x I2C, 1 x UART
- Note: the board only provides 3.3V to components, even though it take in 5V input.

### Programming Language

The board by default comes with the NodeMCU firmware which uses Lua. I've switched to the Arduino IDE using C instead.

### Limitations

You get much fewer pins than appears on the specifications. Depending on what you want to do, they can be quite a challenge to use.

- GPIOs 6 to 11 are unavailable for use, as they are connected to the Flash chip
- GPIO 0 is tricky to use, it is connected to the Flash button, and can cause the board to go into Flash mode
- GPIO 1 and 2 are tricky to use, they can cause the board to fail to boot
- A few other pins are pulled High during boot, which may be a problem for your components depending on what you want to do
- Some pins are fixed for specific uses:
  - I2C
    - GPIO 4: SDA
    - GPIO 5: SCL
  - SPI
    - GPIO 12: MISO
    - GPIO 13: MOSI
    - GPIO 14: SCLK
    - GPIO 15: CS
  - I2S
    - GPIO 2: LRC
    - GPIO 3: DIN
    - GPIO 15: BCLK
  - It's not clear to me whether any of them are re-assignable to other pins (e.g. SPI and I2S)
- Analog pin is input only


## Web Radio

Web Radio is probably one of the common use cases for this microcontroller. There are several tutorials out there, and it can get quite confusing.

### Audio Library

The library to use is https://github.com/earlephilhower/ESP8266Audio

It also comes with a Web Radio example, which is very helpful. In fact, you are better off following the documentation here than to try and follow some random tutorial out there.

The library supports MP3 and AAC decoding, connecting to HTTP streams (HTTPS seems to work).

### Sound Output

The library assumes an I2S interface. However, it conveniently provides a "NoDAC" option so that it can emulate a virtual I2S device and output to a speaker.

Note that the NodeMCU is not able to drive a speaker directly. You need to use an amplifier.

#### 1-Transistor Amplifier

If you don't have an amplifier module, you can also use a quick-and-dirty 1-transistor amplifier described, using a 2N2222 transistor, in place of the 2N3904 mentioned in the docs (they are supposed to be quite identical for the purpose) and a 1k ohm resistor.

I supplied 3.3V to the speaker instead of a 5V source. The sound is output to GPIO 3 (RX) pin only. You have to disconnect the pin during uploading, and reconnect it after it's done.

This setup works but the sound is pretty bad.

### I2S DAC

I have a MAX98357A board so I tried that next. It's actually a DAC plus amplifier.

Specific pins are required to make this work.

- GPIO  2 --> LRC
- GPIO 15 --> BCLK
- GPIO  3 --> DIN (notice it's the same pin as used in the 1-transistor amp)

I can upload fine with these pins are connected.

The sound is much better. I used the 3.3V output instead of a separate 5V source, but it seems ok. Sound is quite loud on the 8 ohm 0.4W speaker I used.

I suppose using the I2S DAC also reduces some processing load, compared to using the "NoDAC" approach.

## OLED Display

I'm using a 128x32 ssd1306 OLED display. The library to use is https://github.com/ThingPulse/esp8266-oled-ssd1306

The main thing to note is that by default SDA is GPIO 4 and SCL/SCK is GPIO 5.

The library comes with many example. Remember to call `display.display()` to actually display the contents.

## Reading the Analog Pin

The goal is to use a potentiometer to set the volume.

The common potentiometer is just a variable resistor and current can go both ways, so you can swap the ground and power lines depending on the desired knob rotation direction. I read there are some kinds of pots that must be wired a certain way but that's not what I have.

### Gotcha for ESP8266

I keep getting reboots when I added the pot, which is extremely puzzling because the code is so direct and simple. Until I found this: https://github.com/esp8266/Arduino/issues/1634

Apparently the reason is that the analog pin is also used to decide WiFi strength? And apparently adding a 3ms delay will work? That's weird...

Update: using `millis()` to decide when to read the pot seems unstable. What works reliably for me in the end is to count the loops, as suggested here: 

https://arduino.stackexchange.com/questions/19787/esp8266-analog-read-interferes-with-wifi

```c
if (serialCount == 10000) {
  ldrState = map(analogRead(ldrPin), ldrMin, ldrMax, 0, 100);
  Serial.println(ldrState);
  serialCount = 0;
} else {
  serialCount ++;
}
```

Update #2: Still getting reboots after some time. It's possible that the analog pin cannot be used together with WiFi, so I might have to switch to a rotary encoder. Another possibility is that I learnt that the ESP8266 "timer" resets after about 72 mins (versus the Arduino which resets in 49 days) (update: there's some contention regarding the 72 min claim). Of course, it could also just be a dropped WiFi connection. I need to conduct further testing.

## Other Resources

- https://github.com/smurf0969/ESP32_ESP8266_WiFi_Speaker_OLED
- https://randomnerdtutorials.com/esp8266-pinout-reference-gpios/
- https://riktronics.wordpress.com/2017/10/02/esp8266-error-messages-and-exceptions-explained/

## Thoughts so far

- The board seems to be rather slow. The instructions say to build for 160MHz CPU, but I don't know whether that made any difference. It take some time to connect to the WiFi network and start the stream. Initially I had a lot of instability, then I got a period of long stable output. Changing radio streams can be quite unstable.
- The volume seems to work initially, but after a period of playback the volume became load again. Might be a bug with the library.
- I might try a PAM8403 amplifier board (not I2S) with onboard volume control, but that might mean more load on the CPU which might affect the playback.
- There are tutorials out there using the VS1053 MP3 decoding board. I believe that will make the playback much better (although limited to MP3). But this board is not that cheap so I'm hesitant to get one.
- The doc mentions an SPI RAM board for better buffering. I looked around and couldn't find a source for the mentoned board, although there seems to be some similar ones but I don't know enough. Also, they are not that cheap.
- Perhaps the ESP8266 is no longer a good enough device for this use case. Maybe I should upgrade to an ESP32 instead?

## Update 2023-03-12: Further Tinkering

I've been tinkering with the board and trying to add more functionality to the Web Radio, but so far it's been pretty unstable overall.

### Using GPIO16 as button

I wanted a button to start/stop the audio.

GPIO16 (D0) is not the best pin to place a button on, but I read that it can be used for input. Instead of internal pull-ups, this pin has a pull-down resistor. However, I couldn't figure out how it's supposed to work. Whether I use the internal pull-down resistor, or just configure as input and wire my own pull-down circuit, I keep getting ghost button presses at random places.

### Chinese characters on SSD1306 OLED display

This is an interesting sidetrack. Since the radio station I want to listen to is in Chinese, it would be nice to be able to display Chinese characters on the OLED display. Due to the size of the Chinese character set and complexity of the font, it is not easy to squeeze everything into the confines of the ESP8266.

I finally found a working solution, using the U8g2 lib, but it splits the Chinese characters into 3 which doesn't seem sufficient for my needs. Then I found a lib called 'Arduino_GFX_Library' where the author created another Chinese U8g2 font based on the most common characters. I tried this combo and it seems to perform quite well.

To fit the Chinese font into the buiild I needed to reduce the filesystem to 1 Mb, and the adjust MMU to give 16 Kb cache + 48 Kb IRAM.

### Fetching song titles

The radio stream I listen to streams from StreamTheWorld, but it does not carry song title information. Some searching brought me to OnlineRadioBox which appears to redirect from StreamTheWorld, but also provides a separate API to fetch the song title. I discovered the API is a long-polling type, which may respond only after about 3 minutes (input param is the timestamp of the last request), so I used the AsyncHTTPRequest_Generic library together with the ArduinoJson library to parse the JSON response.

This increased the memory pressure on the NodeMCU. I didn't have much left to begin with, and with this I started getting memory access exceptions and OOM.

Looks like I might have to get an ESP32 board after all, if I still want to make this thing.

