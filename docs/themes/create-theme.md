# Create Theme

## Images

Bruce accepts **.bmp** **.jpg** **.gif** and **.png** (not available on LITE_VERSION) to be used in the Main menu, but the bigger they are, greater will be the time to draw them on screen, **please, try to keep them small**.

The recommended height of the images is:

| Device | Display Size | Height |
| --- | --- | --- |
| Cardputer and StickCPlus | 240x135 | 105px |
| Lilygo T-Embed | 320x170 | 140px |
| Core / CYD | 320x240 | 180px |
| Lilygo T-LoRa Pager | 480×222 | 192px |
| Custom | Any | 30px less than screen height |


## Theme File

Theme settings are stored in a `.json` file, following this structure:

```
{
  "wifi":"wifi.png",
  "ble":"ble.png",
  "rf":"rf.png",
  "rfid":"rfid.png",
  "fm":"fm.png",
  "ir":"ir.png",
  "files":"files.png",
  "gps":"gps.png",
  "nrf":"nrf.png",
  "interpreter":"interpreter.png",
  "clock":"clock.png",
  "lora":"lora.png",
  "others":"others.png",
  "connect":"connect.png",
  "config":"config.png",
  "priColor":"ffff",
  "secColor":"aaaa",
  "bgColor":"0",
  "border":0,
  "label":0,
  "boot_img":"boot.gif",
  "boot_sound":"boot.wav",
  "ledBright": 100,
  "ledColor": "960064",
  "ledEffect": 0,
  "ledEffectSpeed": 3,
  "ledEffectDirection": 1
}
```

* **Colors (excluding LED):** Codes are in **RGB565**, 16bit pattern, so you need to convert it using [this tool](https://rgbcolorpicker.com/565).
* **LED Colors:** Codes are in HEX.
* **border:** 0 or 1, 1 to keep UI Borders
* **label:** 0 or 1, 1 to use UI Labels (Use 50px smaller images in this case)


## Theme Builder

You can use [Bruce Theme Builder](https://bruce.computer/build_theme.html) to setup and prepare your images, settings and .json file, it will give you a .zip file that you **need to unzip** somewhere on your device, LittleFS or SD Card.
