---
tags:
  - _TODO
todo: Formatting
      Go through each menu item and add documentation
---
# BLE

![BLE Main Menu](ble-main-menu.png)

Features relating to BLE.

![BLE](ble.png)


## Disconnect BLE

If BLE is currently active, use this option to disconnect. No further explanation needed.


## Media Commands

Control your smartphone’s media functions(works with mostly all Android devices):

* Take screenshots
* Play/pause music
* Stop playback, and more!


## BLE Scan

Scan for nearby Bluetooth Low Energy (BLE) devices effortlessly.


## BadBLE

Simulate a keyboard to deploy DuckyScripts to paired devices.

!!! note

    * The device **must be previously paired** for this feature to work.
    * After disconnecting BLE, you need to restart the device to enable this functionality again.

## BLE Spam Features

iOS Spams
- AirPods Pro and Apple TV popup spam
- Legacy SourApple (iOS crash exploit)
- Legacy AppleJuice (iOS pairing spam)

Windows Spam
- SwiftPair pairing notifications with custom name support

Android Spam
- Google FastPair pairing notifications

Samsung Spam
- Galaxy Watch/Galaxy Buds pairing notifications

Spam All
- Cycles through all protocols sequentially (Android → Samsung → Windows → AppleTV → AirPods → SourApple → AppleJuice)

Custom Spam
- Advertise any device name with HID service

iBeacon
- Simulate an Apple iBeacon/AirTag. Can be tracked with an app like Beacon Locator, which also provides an estimation of the distance

# BLE Suite

BLE Suite is a comprehensive Bluetooth Low Energy security testing platform.
It provides reconnaissance, protocol exploitation, and post-exploitation capabilities.

Success varies by target device firmware and patch level. Modern/patched devices resist most attacks.


What You Can Do

Reconnaissance:
- **Quick Vulnerability Scan** - Tests HFP (CVE-2025-36911) and FastPair vulnerabilities
- **Deep Device Profiling** - Full service enumeration with characteristic analysis

Attack Suites:
- **FastPair Suite** - Memory corruption, state confusion, crypto overflow attacks, popup spam
- **HFP Suite** - CVE-2025-36911 testing, connection establishment, full attack chain, HID pivoting
- **Audio Suite** - AVRCP hijacking, audio stack crashing, telephony injection
- **HID Suite** - Keystroke injection, DuckyScript support, OS-specific exploits

Advanced Attacks:
- **Memory Corruption** - Multiple FastPair exploitation techniques
- **DoS Attacks** - Connection flooding, advertising spam, protocol fuzzing
- **Payload Delivery** - DuckyScript injection, PIN brute force, auth bypass

Chain Attacks:
- **Universal Attack Chain** - Automatically attempts HFP → HID → FastPair based on detected services

Smart Features:
- Auto-detects HFP/FastPair services during scan
- Context-aware attack suggestions
- Seamless pivot chains (HFP → HID)
- RSSI-based device sorting


Usage

1. Navigate to BLE Suite from main menu
2. Select attack type
3. Scan for nearby BLE devices
4. Select target
5. Execute attack

Results are displayed on screen and logged to SD if available.
