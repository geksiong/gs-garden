---
title: Learning Raspberry Pi Pico
description: My journey with the Raspberry Pi Pico
date: 2023-02-05
updated: 2023-04-07
---
## CircuitPython

The official framework supported by the Pico is MicroPython, but I quickly discovered Adafruit's CircuitPython, which seems rather popular among makers. After some initial confusion with following tutorials and understanding how things work, I decided to go with CircuitPython.

Some of my notes concerning CircuitPython:

- Installing CircuitPython is a simple matter of putting the Pico into bootsel mode by holding the button while plugging it to USB.
- You can use Thonny (recommended by MicroPython) instead of the Mu editor (recommended by CircuitPython). I found that Mu editor is not much different from a general purpose Python IDE (might as well use VS Code). Thonny has some support for updating firmwares and uploading files to the device so I prefer Thonny. Otherwise, I would prefer the IDEs to provide more features such as Intellisense.
- Do not search for CircuitPython libraries via Thonny's package search. I did that and found myself running out of storage.
  - CircuitPython's libraries are to be directly copied into the lib folder on the Pico. The CircuitPython documentation actually mentions installing via pip, don't do that either.
  - Adafruit distributes an official bundle at their GitHub. I think most of what I'll need can be found here.
  - There's also a community bundle.
  - You need to download the correct versions depending on your CircuitPython version, as the library formats has changed.
  - The lib files are distributed as .mpy files which are very compact.
- CircuitPython will mount the device as a storage device everytime you plug it in. You can disable it by modifying the `boot.py` file as follows. Thonny can still talk to the device and manage files. I'm not so sure about Mu though as it seems to work directly with the mounted folders.

```python
import storage
storage.disable_usb_drive()
```

tip: I saw a fancier script where it checks for a button press, so that you can selectively mount the folder by holding down the button during boot.

### Documentation

CircuitPython Core Libraries: https://circuitpython.readthedocs.io/en/latest/shared-bindings/index.html#modules


## Pico Pinout

![Pico Pinout](https://cdn-learn.adafruit.com/assets/assets/000/099/339/original/raspberry_pi_Pico-R3-Pinout-narrow.png)

## Learning Roadmap

I plan to learn the following:

- [x] Pushbutton
- [x] Rotary encoder (both the knob and its pushbutton)
- [ ] Potentiometer (analog input)
- [ ] analog joystick
- LED
  - [ ] basic LED
  - [ ] RGB LED
  - [ ] pulse
  - [ ] WS2812 LED strip / ring
- OLED display (SSD1306 driver)
  - [x] text
  - [x] draw lines
  - [ ] display image file
  - animation
    - [ ] scrolling text
    - [ ] sprites
- Play simple tone and tune
  - [x] simpleio
  - [ ] musical notes
- Play audio file
  - [x] mp3 via PWM
  - [ ] wav via PWM
  - [x] mp3 via I2S audio output
    - my audio board (MAX9857A) seems to have unstable volume and crackling. Not sure if I screwed up the soldering of the header pins.
- [x] timing
- [?] microphone input
  - this is more frustrating than I expected. I bought an I2S mic, only to find out that CircuitPython has yet to support I2S input. Adafruit now sells a "PDM mic breakout board" with CircuitPython support which I cannot find anywhere else. They claim that it is easier to implement the PDM input API than a full I2S input.
      - looks like the only alternative is to get an analog mic and sample the analog pin directly. Not sure if this will work well for my purposes.
      - https://www.hackster.io/AlexWulff/adc-sampling-and-fft-on-raspberry-pi-pico-f883dd
- [ ] USB HID
- [ ] USB Midi
- [ ] ulab (built-in math lib)
  - for vumeter display
  - for fft spectrum display

### I2S


### I2C

My hardware: 0.91-in OLED SSD1603 128x32 I2C White

- SCK (SCL): GP1
- SDA: GP0

### SPI

My hardware: 0.96-in OLED SSD1306 128x64 SPI Yellow-Blue

SPI has very different layout than I2C, so check which interface is your OLED display using first!

- SCK: GP2
- SDA (MOSI): GP3
- RST: GP4 
- DC: GP0
- CS: GP1

```python
import board
import displayio
import busio
import adafruit_displayio_ssd1306

SCK = board.GP2
SDA = board.GP3
RST = board.GP4
DC = board.GP0
CS = board.GP1

WIDTH = 128
HEIGHT = 64

displayio.release_displays()

spi = busio.SPI(clock=SCK, MOSI=SDA)
display_bus = displayio.FourWire(spi, command=DC, chip_select=CS, reset=RST, baudrate=1000000)
display = adafruit_displayio_ssd1306.SSD1306(display_bus, width=WIDTH, height=HEIGHT)
```
Note: Even though the OLED is "yellow-blue", the colors are actually fixed.  Some specs I've seen mentions 16 pixels height for the yellow part, but that doesn't match my testing. My display has 12 pixels of yellow at the top, with a small gap and then the rest is blue. Depending on your use case this may be desirable or not.

### Joystick

Connection is straightforward - the joystick has 2 analog outputs for X and Y axes, and 1 digital output for the button.

#### Some notes

- Analog input readings are 16-bit: 0 - 65535
- The readings in centre position do vary over time, so need to implement a dead zone to avoid jitter
- Connect the power to 3V3 only, otherwise you may destroy the analog inputs


**TODO**:
- Dead zone and calibration
- https://forum.banana-pi.org/t/bpi-picow-s3-use-a-dual-axis-joystick-circuitpython/14260


### Analog Microphone

Adafruit sells an I2S microphone, as well as a 'normal' analog microphone, but for some reason CircuitPython currently only supports an I2S MEMS PDM microphone.

Since there is no support currently for an I2S mic, I tried using an analog mic based on the MAX4466. It's supposed to be worse than the I2S one.

Th mic comes with an adjustable gain pot. I was able to get some samples from the mic via the `analogbufio` library, and the output does look like it varying with the frequencies. However, the output appears to be quite noisy no matter how I adjust the gain.

There's a catch with the CircuityPython API to note. Most pins you just grab and don't need to release, but with the `BufferedIn` API I found out I can't simply reuse it - I have to `deinit` the pin once I capture my samples, and call `BufferedIn` again the next time to setup the pin for recording.

### Multiplexer


### IO Expander


### Shift Register


