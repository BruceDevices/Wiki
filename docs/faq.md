# FAQ

## How to transfer files to/from the SD/LittleFS storage?

Use the [WebUI](controlling-device/serial.md).


## Default passwords?

For AP mode the WiFi network password is `brucenet`.

For the WebUI username is `admin` and password is `bruce` - these can be changed from the [WebUI Settings](controlling-device/webui.md#settings).


## IR/RF transmitter/receiver not working?

* [IR requires a line-of-sight between the transmitter and the receiver](https://www.quora.com/Does-an-IR-remote-need-a-line-of-sight), which can be easily blocked by solid objects. Light can bounce off some surfaces and still reach the receiver, but don't rely on that too much. [Other IR light sources like the sun can also cause interferences](https://www.garagedoordoctorllc.com/can-sunlight-exposure-affect-garage-door-sensors/).
* RF signals propagate in all directions and does not require a line of sight, but can [still be blocked by solid objects](https://jemengineering.com/blog-5-ways-physical-objects-impact-rf-signals/), especially if made of metal.
* Try putting the transmitter and receiver closer (<10cm), many cheap modules have poor ranges, or may be too noisy.
* If using unofficially supported modules, double-check that the used pins are correct. These can be [changed in the Settings](https://www.youtube.com/watch?v=i4wRNeGQJfw).
* Check if the module is active and working during transmission:
    * For IR, you can [use a smartphone camera to check if the IR LED is blinking](https://www.youtube.com/watch?v=i4wRNeGQJfw).
    * Use a multimeter or an oscilloscope to check activity on the data pin/LED/antenna.
    * Use another device to check and compare the signals. e.g. [rtl_433](https://github.com/merbanan/rtl_433) for RF, [Tasmota](https://tasmota.github.io/docs/Tasmota-IR/) or a [Broadlink RM4 Pro](https://www.ibroadlink.com/productinfo/762672.html) for both IR and RF.
* Most RF modules only supports the 433MHz frequency with ASK/OOK modulation. Check if your `.sub` file or source signal is compatible.
* Check the [serial log](controlling-device/serial.md) for errors, some signals may [fail to decode](https://github.com/BruceDevices/firmware/issues/216).
* Even if the signal decodes properly, some devices may use [rolling codes](https://en.m.wikipedia.org/wiki/Rolling_code) or other security mechanism to prevent replay attacks.


## Feature X is missing in my device?

Check the Compatible Device table at the bottom of the [Bruce Homepage](https://bruce.computer).

e.g. BadUSB is only available on ESP32-S3-based devices.


## My board is not supported?

For unsupported boards you have to port Bruce to support your device.

More info in the [Porting to Devices](development/porting-to-devices.md) documentation.


## What kind of files can I put on the SD card?

There is some file examples [here](https://github.com/BruceDevices/firmware/tree/main/sd_files).

Including:

* [Evil Portals](https://github.com/BruceDevices/firmware/tree/main/sd_files/portals)
* [NFCs](https://github.com/BruceDevices/firmware/tree/main/sd_files/nfc)
* [Infrared](https://github.com/BruceDevices/firmware/tree/main/sd_files/infrared)

There are two very useful external sources of:

* [RF .sub files](https://github.com/Zero-Sploit/FlipperZero-Subghz-DB/tree/main/subghz)
* [Infrared .ir files](https://github.com/Lucaslhm/Flipper-IRDB)


## How do I customize Bruce?

You can choose a startup image other than Bruce's default by adding your image as 'boot.jpg' or 'boot.gif' on the root of the filesystems, you can also choose a startup sound if you have a file named 'boot.wav' for it.
Also its possible to render images on the Bruce itself via the LittleFS or SD Card manager.
