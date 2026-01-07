# Headless Mode

Headless mode can be used to use a device without a screen and buttons.


## Setup Headless Mode

* Connect over serial to the device more information [here](serial.md#how-to-send-commands)
* Connect to WiFi by sending command `wifi add NetworkName Password`
* Launch the WebUI by sending command `webui`

If you type `info` then it will show you the IP address the device has been assigned. This IP address can be used in a web browser to connect to the [WebUI](webui.md).

Other serial commands are documented [here](serial.md#reference)

You might then want to use the [WebUI Navigator](webui.md#navigator) to enable:

* **WiFi at Startup** - Config -> Startup WiFi
* **WebUI at Startup** - Config -> Startup App -> WebUI
