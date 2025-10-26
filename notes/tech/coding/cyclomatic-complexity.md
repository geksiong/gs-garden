---
title: Cyclomatic Complexity
description: Some notes on keeping cyclomatic complexity low
date: 2023-04-08

tags:
  - complexity
  - software engineering

---
Cyclomatic Complexity is one of the indicators of code complexity. It basically measures the amount of branching in your function. If you have too many nested branches in your code, it probably means the code is difficult to reason about (has high cognitive load), and therefore hard to maintain. I've seen clients require that we do not exceed a certain level of cyclomatic complexity, as measured by code quality tools such as SonarQube.

But what exactly is Cyclomatic Complexity? I've seen teams struggle with keeping this metric below the designated threshold (SonarLint/Qube uses 15), and there are also times when they declare it is not possible. Is it really so hard to write codes to go below? How bad are codes that exceed this?

I was recently playing with getting a joystick to emulate a mouse, powered by CircuitPython on Raspberry Pi Pico board. I cobbled together some codes I found online, and that saw one of the functions had exceeded the Cognitive Complexity of 15 as set by SonarLint.

The offending function looks like this:

```python
direction_list = ["East","Southeast","South","Southwest","West","Northwest","North","Northeast","Centre"]

def get_direction(zero = (32767,32767)):
    x_axis = x_axis_pin.value - zero[0]
    y_axis = y_axis_pin.value - zero[1]
    if x_axis >= 10000 and -10000 < y_axis < 10000:
        return direction_list[0]
    elif x_axis >= 10000 and y_axis <= -10000:
        return direction_list[1]
    elif -10000 < x_axis < 10000 and y_axis <= -10000:
        return direction_list[2]
    elif x_axis <= -10000 and y_axis <= -10000:
        return direction_list[3]
    elif x_axis <= -10000 and -10000 < y_axis < 10000:
        return direction_list[4]
    elif x_axis <= -10000 and y_axis >= 10000:
        return direction_list[5]
    elif -10000 < x_axis < 10000 and y_axis >= 10000:
        return direction_list[6]
    elif x_axis >=10000 and y_axis >= 10000:
        return direction_list[7]
    else :
        return direction_list[8]
```

The Cyclomatic Complexity is 17, only 2 more than 15 so it's "not bad". The code is pretty straightforward, and frankly not too hard to understand, but yes the cascade of 'if-else' can be a pain to debug if you got one of the conditions wrong.

Why was it measured as 17 though? I counted 8 branch points, each of the if-else involves 2 variables instead of the last one, so I'm guessing it's 8 x 2 + 1 = 16. The plus one is the final 'else' block.

I then wrote a similar looking function while trying to implement mouse acceleration based on X-Y position of the joystick. The initial codes look like this:

```python
def get_mouse_move(x, y, zero):
    move_x = 0
    move_y = 0
    if (x >= 0):
        if (x > x_extremum[1] - zero[0]):
            move_x = 1
            if (x > 10000):
                move_x = 12
            elif (x > 5000):
                move_x = 8
            elif (x > 1000):
                move_x = 2
    else:
        if (x < x_extremum[0] - zero[0]):
            move_x = -1
            if (x < -10000):
                move_x = -12
            elif (x < -5000):
                move_x = -8
            elif (x < -1000):
                move_x = -2

    if (y >= 0):
        if (y > y_extremum[1] - zero[1]):
            move_y = 1
            if (y > 10000):
                move_y = 12
            elif (y > 5000):
                move_y = 8
            elif (x > 1000):
                move_y = 2
    else:
        if (y < y_extremum[0] - zero[1]):
            move_y = -1
            if (y < -10000):
                move_y = -12
            elif (y < -5000):
                move_y = -8
            elif (y < -1000):
                move_y = -2

    return (move_x, move_y)
```

_Don't mind what 'extremum' and 'zero' means, they are not important for the discussion._

Immediately I saw that SonarLint warned the Cyclomatc Complexity is 32. If you count the number of 'if-else' I think you get 16 x 2. So yeah, 32.

My optimised version of the same function resulted in no warnings:

```python
def accel_function(input, dead_min, dead_max):
    if (input >= dead_min and input <= dead_max):
        return 0

    if (input < -10000):
        return -12
    if (input < -5000):
        return -8
    if (input < -1000):
        return -2

    if (input > 10000):
        return 12
    if (input > 5000):
        return 8
    if (input > 1000):
        return 2

    return 1

def get_mouse_move(x, y, zero):
    move_x = accel_function(x, x_extremum[0] - zero[0], x_extremum[1] - zero[0])
    move_y = accel_function(y, y_extremum[0] - zero[1], y_extremum[1] - zero[1])
    return (move_x, move_y)
```

I broke up the function into 2. The smaller function implements repeated logic that was previously expanded out in the 'if-else' statements. Being able to return immediately from the function also allowed me to get rid of the 'elif' parts. Overall there's less code to read and reason about, so it's not a wasted trip.

Structurally it doesn't look like there's much difference in the logic. The logic may have been abstracted away into another function, but overall after you assemble everything back together it's essentially the same logic.

One might even argue that functional calls are less performant. Yes, but if you follow this argument you might as well just write the entire program as a single function (functional programming FTW?)...

Another argument could be that it's easier to change some numbers if the requirements change later, and creating more abstractions is 'over-engineering' which we are not supposed to do. This is an interesting point of view, and personally I think that's not what the term 'over-engineering' is meant to guard against, so I don't buy it. Software engineering is all about balancig tradeoffs though, so end of the day it is about which problem they'd rather handle later. I would be careful though if I keep hearing such arguments  this may indicate a desire to avoid additional work, or deeper underlying causes resulting in an overworked team.

Anyway, back to topic. The point about Cyclomatic Complexity is not the performance of the code, but the ability to reason about it.

Sadly SonarLint doesn't tell me what's the Cyclomatic Complexity of the revised implementation. One way is to configure it to a ridicously low threshold so it'll issue warnings for everything. It would be nice if SonarLint can report for everyone so that developers can learn as they code, instead of scratching their heads when something becomes too big to handle.
