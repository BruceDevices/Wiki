# Porting to Devices

For unsupported boards/devices you have to manually add a new [configuration environment](https://docs.platformio.org/en/latest/projectconf/sections/env/index.html) in the [platformio.ini](https://github.com/BruceDevices/firmware/blob/main/platformio.ini) and `boards` folders, and define the pins for the modules to be connected, buttons and display.

Then, [compile the firmware for your new environment](building-from-source.md). Depending on the board more code changes may be needed.


## Headless Mode

For boards that do not have a display and buttons you can use the [Headless Mode](../controlling-device/headless-mode.md). Headless boards can be controlled via the [WebUI Navigator](../controlling-device/webui.md#navigator) and [Serial](../controlling-device/serial.md).


## Example Environment

A sample environment for the [esp32-s3-devkitc-1 board](https://docs.espressif.com/projects/esp-idf/en/stable/esp32s3/hw-reference/esp32s3/user-guide-devkitc-1.html) is [defined here](https://github.com/BruceDevices/firmware/blob/main/boards/ESP-General/ESP-General.ini), it should work with any headless ESP32-S3 boards.


## Files

```
.
├── platformio.ini
└── boards
    ├── _boards_json
    │   └── [board].json
    └── [board]
        ├── interface.cpp
        ├── pins_arduino.h
        └── [board].ini
...
```

Replace `[board]` with the board name


### platformio.ini

Add entry to `default_envs` section.


### boards/_boards_json/\[board].json

This is the board config. Look at other boards for whats needed.

Here is an offical example and what we are actually using [here](https://github.com/platformio/platform-espressif32/blob/master/boards/esp32-s3-devkitc-1.json)


### boards/pinouts/pins_arduino.h

This is where you put the flags and pinouts to the board. Look at other boards for whats needed.

Here is an official example and what we are actually using [here](https://github.com/espressif/arduino-esp32/blob/master/variants/esp32s3/pins_arduino.h)


### boards/\[board]/interface.cpp

This is where board specific setup code is added.


### boards/\[board].ini

This is the platformio config for the device. Look at other boards for whats needed.


### .github/workflows/buil_parallel.yml

This is the last file that should be changed to add a new device build, you should just add a new line with the environment you choose before and set the bootloader address.
