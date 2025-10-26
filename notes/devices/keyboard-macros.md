---
title: Software Keyboard Macros
description: Some keyboard macros I used before I got a Planck keyboard
date: 2021-07-17
---
# Software Keyboard Macros

Mac - via Karabiner-Elements + Hammerspoon
Win - via AutoHotKey

Some of these functions have been built into my planck keyboard too_

## Windows Management & Navigation

use keybindings similar to i3
although for Mac/Win we can emulate some things

- $mod + Enter: new terminal
- $mod + hjkl: switch window focus
- $mod + f: toggle fullscreen
- $mod + num: switch to workspace
- $mod + shift + num: move window to workspace
- (move window)
- (resize window)
- (move + resize to particular layout position)
- (switch monitor?)
  - e.g. mod + wer (from xmonad)

layouts

2x1: 2 halves
2x2: 4 corners
3x3: 6 combinations, does it make sense?

another approach is to use modes:

- toggle to window management mode
- display a grid with numbers maybe
- tap 2 numbers to specify the 2 corners of the window

what can i use for mod key?

- Win: App key?
- Mac: RAlt for builtin keyboard, App key for external keyboards
- for planck, since I don't have an App key, probably a chord of two keys. or i programmatically code a double tap action.
  - switching workspaces will require too many keys. **a better approach is needed**
  - idea: use Space + Fn to toggle layer with other mod combos (since Space is less likely to require combo?)

comparison with dynamic tiling wm e.g. xmonad

- concept of master window in a given layout. you can swiotch between current and master window, and you can resize the master area

### Recommended bindings from DistroTube

| Keybinding                | Action                                                                     |
|---------------------------|----------------------------------------------------------------------------|
| `MODKEY + RETURN`         | opens terminal                                                             |
| `MODKEY + SHIFT + RETURN` | opens run launcher (such as dmenu)                                         |
| `MODKEY + TAB`            | rotates through the available layouts                                      |
| `MODKEY + SHIFT + c`      | closes window with focus                                                   |
| `MODKEY + SHIFT + r`      | restarts the window manager                                                |
| `MODKEY + SHIFT + q`      | quits the window manager                                                   |
| `MODKEY + 1-9`            | switch focus to workspace (1-9)                                            |
| `MODKEY + SHIFT + 1-9`    | send focused window to workspace (1-9)                                     |
| `MODKEY + j`              | switches focus between windows in the stack, the opposite direction of ‘k’ |
| `MODKEY + k`              | switches focus between windows in the stack, the opposite direction of ‘j’ |
| `MODKEY + SHIFT + j`      | rotates the windows in the stack, the opposite direction of ‘k’            |
| `MODKEY + SHIFT + k`      | rotates the windows in the stack, the opposite direction of ‘j’            |
| `MODKEY + h`              | expand size of window                                                      |
| `MODKEY + l`              | shrink size of window                                                      |
| `MODKEY + w`              | switch focus to monitor 1                                                  |
| `MODKEY + e`              | switch focus to monitor 2                                                  |
| `MODKEY + r`              | switch focus to monitor 3                                                  |
| `MODKEY + period`         | switch focus to next monitor                                               |
| `MODKEY + comma`          | switch focus to prev monitor                                               |

## Display key bindings

There's a KSheet spoon for hammerspoon that does this

## Key bindings in Pop OS

- MOD + arrows: switch focus
- MOD + Enter: window adjustment mode
  - arrows: move window
  - shift + arrows: resize windows
  - Ctrl + arrows: Swap windows
  - Enter: apply and exit adjustment mode
  - Esc: exit without applying
- MOD + S: toggle stacking
- MOD + O: change window orientation
- MOD + G: float/unfloat window
- MOD + M: maximise/unmaximise window
- MOD + Ctrl + left/right: snap to left/right size of display
- MOD + Q: close window
- MOD + Ctrl + up/down: navigate workspaces
- MOD + home/end: first/last workspace
- MOD + Shift + arrows: move window between workspaces/displays
- MOD + Esc: Lock screen
