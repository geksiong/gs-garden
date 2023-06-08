---
title: ESPHome Remote Control and Sensors
description: Building an ESPHome Remote Control and Sensor node
date: 2023-06-08
updated: 2023-06-08
---

## Concept

I wanted to make a WiFi-controllable IR and RF remote control, with the ability to learn the codes from my remote controller. Then by accessing it from HomeAssistant I can try out some smart home automation. The board would be an ESP8266, cannibalised from my previous not-so-successful Web radio project (which will be converting to an ESP32 but more on that later). Additionally, I would like to use the same node to do some temperature and humidity monitoring (a rather "hot" topic nowadays).

## ESPHome

ESPHome is a low-code approach to building sensors and controllers, and it also integrates with HomeAssistant since it is by the same company that runs HomeAssistant Cloud. Instead of writing code, you just write a YAML file, and the tool will generate all the codes, build and upload it for you. Pretty impressive.

### Installation

Installing on a Mac was straightforward - there's already a Homebrew formula for it. Just run `brew install esphome`.

## IR Receiver

The first order of the day is to learn my remote control codes, otherwise I'll have nothing to transmit with. It'll be far cheaper to buy a bunch of IR receiver diodes, but I only need one and I want it quick and easy so I bought a breakout board.

### Gotchas

Writing the YAML files was easy, and everything built and uploaded fine. But I couldn't see any logs output from the remote receiver (other logs were fine). After some googling I suddenly remember that the ESP8266 has some limitations when it comes to which pins are usable for what. After refreshing my memory, I connected the data pin to D7 and I finally got logs.

I then saw that the readings were inconsistent, so back to Google again. Turns out a common "fix" is to invert the pin reading and specify that the pin is in INPUT_PULLUP mode.

### Codes

Below is what I'm using for that section of the codes:

```yaml
remote_receiver:
  pin:
    number: D7
    inverted: True
    mode: INPUT_PULLUP
  dump: all
```

Interestingly, testing with my *Mitsubishi* floor fan remote gives me *three* types of remote codes: Pronto, Panasonic and AEHA.

...to be continued
