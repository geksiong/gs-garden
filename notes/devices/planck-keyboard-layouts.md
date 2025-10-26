---
title: Planck Keyboard Layout
date: 2021-01-17
---
# Planck Keyboard Layout

Some notes about my preferences:

- My dominant hand is the left hand, although I'm right handed for writing tasks. This is probably a consequence of me being born a lefty and trained since young to become a righty.
- I don't use the RCtrl/RCmd or RAlt/ROpt, but I do use RShift

## Some Principles

- Left- and right-most columns, and bottom row are the modifier and layer keys.
- Several mod keys also perform double duty using QMK's mod tap feature (since the base layer can't contain all keys on a typical keyboard)

## Software

- tmux
  - I use Alt to trigger tmux functions
    - To switch windows, Alt+n
- vim
  - leader key is backslash, which is not on the base layer. Many users use comma as the leader
- dwm
- karabiner+hammerspoon
  - hyper key - capslock → f19 (**this is current unmapped on the planck**)
  - super key - RAlt → f18
- Some conventions maybe need to change
  - Meta is usually AltGr (RAlt), although in KDE if refers to the Cmd key
  - Super is the Cmd key in Ubuntu
  - Hyper - not really on keyboards

## Features I want

- **Combined symbol & number layer**
  - to avoid switching between layers (this is important for coding)
  - trigger with left hand so that I can enter numbers with right hand
  - **logical solution: lower key**
- **Left-hand arrow cluster**
  - something I do via software with the caps lock key on normal keyboards
  - **solution: Tab ley**
- **Right-hand arrow cluster**
  - If feasible, a mouse overlay on top of the right-hand cluster
  - **solution: Raise key**
    - overlay by pressing some key on the left (Tab?)
    - accelerate mouse with left hand keys
- **Adjust layer**
  - For RGB and other keyboard adjustments
  - **Trigger: Raise + Lower**
- **Function key layer**
  - Ctrl, Alt, Shift, Cmd untouched for combos
  - do I really need beyond F12?
  - Ideally triggered on the left, so that I can use F2 and F4 on Windows with left hand, together with the mouse on the right
  - **solution: hold down ESC** **DOESN'T WORK**
    - better than the 'FN' key, since most often used F keys are F1-F4
- **Window / Macro layer**
  - Mainly is activate RAlt and RGui (which are not on the base layer) for combos via software
    - RAlt → F19
    - RGui → F20
  - Ideally most of the base layer should be accessible here.
    - lock this to QWERTY?
  - **solution: hold down FN key**
- **Multimedia layer**
  - Vol, brightness adjust
  - PgUp/PgDn/Home/End with bottom right arrows (use thumb)
    - trigger with right hand key for one handed operation
  - **trigger: BkSp**
- **QMK layer**
  - Reset
    - **trigger: BkSpc + Esc**

## Design

### L0: Base Layer

- Esc and Tab keys are swapped, in order to cater to my muscle memory.
  - Esc key is Esc normally, but tilde when shift or GUI is pressed.
  - Tab key is Ctrl when held, Tab when pressed.
- Enter and quote keys are swapped, also for my muscle memory.
  - Enter key is RCtrl when held, Enter when pressed
  - Quote key is right-shift when held, quote when pressed.
    - _Note: to get double quotes, you need to use the left shift key_
- LShift is LShift when held, dash when pressed
- Bottom row: App (Menu), GUI (Cmd on Mac), Left-Alt, L8 (func), Tab/L4 (math), Space/L6 (nav), Enter/L5 (sym), left, down, up/right-alt, down/right-gui
  - Retained the original arrow key layout

### L1: Alternative Layout

Here I chose the Workman layout. I get around to learning it some day...

### L2, L3: Unused, reserved for other layouts?

### L4: Numbers

Invoked with Lower key
need to have comma maybe

### L5: Symbols

Invoked with Raise key

### L6: Numbers

Invoked with Lower key

### L7: Navigation

### L8: Function & Macro

Tap multiple times to lock/unlock

F1-12 on left cluster, F13-24 on right cluster (intend to map them to macros or other functionality via software)

### L9-13: Unused

### L14: Media

### L15: QMK

Invoked by holding down top left and top right keys

Reset, Debug, EEPROM reset

## Future Improvements?

- Still feels like the thumb is dancing around too much, especially when I need to enter something non-alpha after a space. Maybe should use the pinky to switch those layers instead?
- The 'App' key is pretty useless, what is used for on Windows?
  - Still want a left hand navigation cluster, so that I can simultaneously use right hand for mouse. Maybe switch bottom corner back to ctrl, and use my tap key to invoke hyper key (for karabiner/hammerspoon make capslock emit real hyper key uinstead of f18)?

---

## Notes on Planck Keyboard Layouts

### Tap actions

Generally, there are 3 types of keys on a keyboard:

1. Keys that modify the behaviour of other keys while pressed, but don't do anything on their own (e.g. Ctrl, Shift) - these are the _modifier keys_
2. Keys that you press & release, but don't want to repeat (e.g. Esc, Insert)
3. Keys that you may want to repeat (e.g. spacebar, backspace)

The 1st and 2nd groups can be merged into a single key

Tap once → a character is output
Press in conjunction with another key → modifier

#### Auto-Shift

Hold a key to output its shifted character. Tradeoff is you lose auto-repeat.

#### Tap & Hold

There can be some UX issues with this approach. QMK defaults to 200ms, but it can vary depending on your preference and the key switches used.

#### Tap Dance

Rapidly tap 2 times or 3 times to enter a different character.

### Remapping

#### CapsLock → Esc / Ctrl

CapsLock is an odd key in that it doesn't belong in the 3 groups above, and it is generally quite useless.

Many people opt to remap the Caps Lock key to Ctrl (especially Vim users)

One interesting approach is to combine this with Esc key.

### Layers

#### How many layers can you get?

The 4 keys on the bottom left generally gets you Ctrl, Alt/Option, Meta/Command/OS, Hyper/Super. If you move Ctrl to CapsLock

Left/right of the spacebar you get Lower & Raise, or some people prefer to refer to them as Fn1 & Fn2.

#### Numeric Layer

Keys:

- Numbers: 0-9
- Operators: +-/*^%=
- Brackets: ()
- Function keys?

Approaches:

- **Number row on home row**
  - the principle is that you don't stray from the home row.
  - F1-F10 keys can be placed in the upper row
- **Number row on the upper row**
  - numeric keypad on the right hand (7,8,9 are the common keys)
- **Number row on 3rd row**
  - numeric keyboard on 7,8,9 (inverted from above)

#### Symbol Layer

Objective: facilitate regex entry

Keys:

- `!@#$%^&*()`
- backtick, tilde
- foward / back slashes
- brackets: {} [] ()
- dash, plus, pipe. colon, single/double quotes, question mark

_appears to be quite a squeeze_

Approaches:

- Symbol row (Shift 0-9) on the home row
  - F11-F20 are on the upper row. Other symbols are on the bottom row.
- Symbol cluster on left hand

#### Function Layer

Approaches:

- F1-10 in a row
- F1-F12 in a 4x3 cluster on the left/right hand

#### Navigation Layer

Keys: U/D/L/R, Home, PgUp, PgDn, End

Approaches:

- Navigation cluster on right hand
  - Either Vim style or inverted T
  - For ease of use, semicolon key can be a Tap-Mod key for single handed operation

#### GUI/Media/Mouse layer

## Resources

- https://noahfrederick.com/log/the-planck-keyboard#:~:text=The%20Planck%20is%20a%2040,a%20total%20of%2047%20keys.
- http://thedarnedestthing.com/planck%20refactored
- http://thedarnedestthing.com/ergo%20planck
- https://www.smittey.co.uk/the-planck-key-theory
