---
title: Xcode Notes
date: 2018-12-08

tags:
- xcode
---

### Cleaning Xcode

#### Look at these folders

* `~/Library/Developer/Xcode/DerivedData`
* `~/Library/Developer/Xcode/iOS\ DeviceSupport` (created when you connect a physical device)
* `~/Library/Developer/Xcode/Archives` (either delete in bulk, or manage them inside Xcode: `Xcode > Organizer > Archives`)
* `~/Library/Caches/com.apple.dt.Xcode`
* `~/Library/Logs/CoreSimulator` (logs that build up over time)
* `~/Library/Developer/Xcode/iOS\ Device\ Logs`

#### Remove unused simulators

```shell
xcrun simctl list devices
xcrun simctl delete unavailable
xcrun simctl delete <id>
```

or in Xcode: Go to `code > Window > Devices > SIMULATORS`

or in Finder: Look a `/Library/Developer/CoreSimulator/Devices/`
