# How to Install

## Official Web Flasher

The easiest way to install Bruce is using the official [Web Flasher](https://bruce.computer/flasher).


## Launcher

Use [Launcher](https://bmorcelli.github.io/Launcher/), once this is flashed to your device you can do over-the-air updates of Bruce and many other firmwares.


## M5Stack M5Burner

Flash it directly using the [M5Burner tool](https://docs.m5stack.com/en/download), just search for 'Bruce' within the device category you want and click `Burn`.

> The official builds will be uploaded by "owner" and have photos


## Manual Flashing

### Command Line

Download the latest binary from official [Web Flasher](https://bruce.computer/flasher) and flash locally using esptool.py

```sh
esptool.py --port /dev/ttyACM0 write_flash 0x00000 Bruce_device_version.bin
```

### Web Based Flashers

These are useful if the official flasher does not work for you, or you want to flash a .bin you have created yourself.

[ESPWebTool](https://esptool.spacehuhn.com/)

[ESPConnect](https://thelastoutpostworkshop.github.io/ESPConnect/)
