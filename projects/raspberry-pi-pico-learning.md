---
title: Learning Raspberry Pi Pico
description: My journey with the Raspberry Pi Pico
date: 2023-02-05
---
## Switch to CircuitPython?

The official firmware for the Pico is MicroPython, and I had been trying out tutorials with it. Then I discovered others are using CircuitPython. CircuitPython is built by Adafruit, on top of MicroPython. I thought it would be good to have a try, since MicroPython is quite low-level for some the things I wanted to do with the Pico. CircuitPython is also supposed to be more portable across different boards.

So far my observations are:
- going by the examples that people posted, the APIs can be quite verbose
- I'm confused by how package management is supposed to work. Simply trying to install Adafruit's OLED SSD1306 package resulted in me running out of storage.
- There's a community library bundle on Adafruit's GitHub, but it is obviously too big for the Pico's storage (only 1 Mb left after installing CircuitPython). Manually copying libs over and discovering more dependencies to copy just doesn't sound right.

For now I'm back in MicroPython.


## Pico Pinout

![Pico Pinout](https://cdn-learn.adafruit.com/assets/assets/000/099/339/original/raspberry_pi_Pico-R3-Pinout-narrow.png)

## Learning Roadmap

### Pushbutton

Ok this is the quintessential "Hello World". No issues here.

### (TODO) Rotary encoder


### OLED display (SSD1306 driver)

Experience was pretty good with MicroPython. But when I tried switching to CircuitPython I ran out the storage just trying to install the libs.

### Play simple tone

Via PWM pin output direct to speaker.
Sound is not loud, probably because my speakers (ripped them out of old computer speakers) are 8 ohms. Not sure if I should get 4 ohm speakers instead. Definitely not a buzzer.

I'm also unsure which amplifier board I should get.

### (TODO) Play wav or mp3 file

Not much success yet. So far the tutorials I found are based on CircuiyPython.


