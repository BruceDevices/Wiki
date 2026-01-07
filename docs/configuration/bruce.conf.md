# bruce.conf

This file is used to persist settings between boots and when swapping firmware on your device when using [Launcher](https://bmorcelli.github.io/Launcher/) when you finish running other firmware.

TODO: Don't store pwd in cleartext :P

If there is no `bruce.conf` file on the LittleFS or SD card (if available) then one will be created.

## Default bruce.conf

```json
{
  "priColor": "a80f",
  "secColor": "880f",
  "bgColor": "0",
  "themeFile": "",
  "themeOnSd": 0,
  "dimmerSet": 10,
  "bright": 100,
  "tmz": 0,
  "soundEnabled": 1,
  "soundVolume": 100,
  "wifiAtStartup": 0,
  "instantBoot": 0,
  "ledBright": 50,
  "ledColor": "960064",
  "ledBlinkEnabled": 1,
  "ledEffect": 0,
  "ledEffectSpeed": 5,
  "ledEffectDirection": 1,
  "webUI": {
    "user": "admin",
    "pwd": "bruce"
  },
  "webUISessions": {},
  "wifiAp": {
    "ssid": "BruceNet",
    "pwd": "brucenet"
  },
  "wifiMAC": "",
  "evilWifiNames": [],
  "evilWifiEndpoints": {
    "getCredsEndpoint": "/creds",
    "setSsidEndpoint": "/ssid",
    "showEndpoints": true,
    "allowSetSsid": true,
    "allowGetCreds": true
  },
  "evilWifiPasswordMode": 0,
  "wifi": {},
  "mifareKeys": [],
  "startupApp": "",
  "wigleBasicToken": "",
  "devMode": 0,
  "colorInverted": 1,
  "badUSBBLEKeyboardLayout": 0,
  "badUSBBLEKeyDelay": 50,
  "disabledMenus": [],
  "qrCodes": [
    {
      "menuName": "Bruce AP",
      "content": "WIFI:T:WPA;S:BruceNet;P:brucenet;;"
    },
    {
      "menuName": "Bruce Wiki",
      "content": "https://github.com/pr3y/Bruce/wiki"
    },
    {
      "menuName": "Bruce Site",
      "content": "https://bruce.computer"
    },
    {
      "menuName": "Rickroll",
      "content": "https://youtu.be/dQw4w9WgXcQ"
    }
  ]
}
```
