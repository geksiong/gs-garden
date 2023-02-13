---
title: Learning Raspberry Pi Pico
description: My journey with the Raspberry Pi Pico
date: 2023-02-05
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

### Documentation

CircuitPython Core Libraries: https://circuitpython.readthedocs.io/en/latest/shared-bindings/index.html#modules


## Pico Pinout

![Pico Pinout](https://cdn-learn.adafruit.com/assets/assets/000/099/339/original/raspberry_pi_Pico-R3-Pinout-narrow.png)

## Learning Roadmap

I plan to learn the following:

- [x] Pushbutton
- [x] Rotary encoder (both the knob and its pushbutton)
- [ ] Potentiometer (analog input)
- LED
  - [ ] basic LED
  - [ ] RGB LED
  - [ ] pulse
  - [ ] WS2812 LED strip
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
  - [x] mp3
  - [ ] wav
- [x] timing
- [ ] USB HID
- [ ] USB Midi




