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
  - CircuitPython's libraries are to be directly copied into the lib folder on the device.
  - Adafruit distributes an official bundle at their GitHub. I think most of what I'll need can be found here.
  - There's also a community bundle.
  - You need to download the correct versions depending on your CircuitPython version, as the library formats has changed.
  - The lib files are distributed as .mpy files which are very compact.
- CircuitPython will mount the device as a storage device everytime you plug it in. You can disable it by modifying the `boot.txt` file.

### Documentation

CircuitPython Core Libraries: https://circuitpython.readthedocs.io/en/latest/shared-bindings/index.html#modules


## Pico Pinout

![Pico Pinout](https://cdn-learn.adafruit.com/assets/assets/000/099/339/original/raspberry_pi_Pico-R3-Pinout-narrow.png)

## Learning Roadmap

I plan to learn the following:

- Pushbutton
- Rotary encoder
- Potentiometer (analog input)
- OLED display (SSD1306 driver)
- Play simple tone and tune
- Play wav or mp3 file




