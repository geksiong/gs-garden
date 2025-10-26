---
date: 2025-10-24
updated: 2025-10-25
---

# DIY Layout Creator

This is a very nice app for guitar and pedal wiring.

GitHub: https://github.com/bancika/diy-layout-creator

What makes it extremely useful for guitar wiring is the **Guitar Wiring Analyzer** feature. It can do the following:

* identifies all the switches in the diagram and analyzes how the circuit behaves in **each of the combination of switch positions**. For example, if we have a 3-way Les Paul switch and one push-pull potentiometer, the diagram will show six different switch combinations and would analyze all of them in detail
* in each switch position, it checks which of the pickup coils are activated,
* checks whether they are in **split-coil mode** or **full humbucking** (in case of humbucking pickups),
* checks if the coils are wired in **series** or **parallel** mode,
* checks the phase of each coil and in case there is more than one active coil, checks if the coils are **in-phase** or **out-of-phase**,
* checks if the current pickup combination is **noise-cancelling** or not,
* checks if any of the potentiometers are wired as **volume** or **tone** controls
* checks if there is a **treble bleed network** installed on a volume control

## Tutorials

This is a useful tutorial: https://www.youtube.com/watch?v=dlYYSDWnYIY

## Drawing Tips

Some things that might not be immediately obvious.

Note: Guitar wiring diagrams are usually drawn _as viewed from the back_, i.e. what you should see when soldering.

### Add flexible leads

Some components can automatically extend flexible leads. It doesn’t work for all components though.

### Neck Humbuckers

The default humbucker component is for bridge (north pointing up). The neck humbucker is simply the bridge humbucker rotated 180 degrees.

### Push-pull pots

To get a push-pull pot, attach a mini toggle switch to a potentiometer. Default type should be “DPDT (On/On)” - you should not need to change it.

The gotcha is that the mini toggle switch does not rotate 180 degrees, and neither does it mirror vertically. If you turn on markers, you can see which sides are ON1 and ON2. ON1 is above and ON2 is below even when you do these transforms. So you cannot stick the toggle on a pot and rotate it as a group. Best is to just treat them as separate components...

Since potentiometers are by default drawn with the lugs pointing down, we can adopt the convention of ON1 = Push and ON2 = Pull (similar to looking at the push-pull pot with shaft pointing down). However, when we rotate the pot by 180 degrees, the toggle is still ON1 = Push and ON2 = Pull, so need to bear that in mind when soldering.

### Blend pots

According to this [closed issue](https://github.com/bancika/diy-layout-creator/issues/797), you can get a blend pot by stacking 2 pots.

### Acoustic pickups

The app does not have acoustic piezo style pickups, or DIY piezo elements. It’s cosmetic anyway, just have to make do with any of the standard pickups.

### Title Block

You can add a “Title Block” component to your diagram to provide some documentation.

### Bill of Materials

You can add a “Bill of Materials” component which automatically lists the various components.
