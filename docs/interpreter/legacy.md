---
tags:
 -  _TODO
todo: Formatting
      Needs updating with all recent JS changes by Matt (@emericklaw)
      Possible changes with the upcomming migration away from Duktape
      Go through each function and check documentation
---
## Manual for Coding in JavaScript to Bruce

This section was ported from [justinknight93/Doolittle](https://github.com/justinknight93/Doolittle), who made a very nice job with its JavaScript interpreter.

Some changes were made and some new functions were added, and more are to be add.

Some [example scripts are here](https://github.com/BruceDevices/firmware/tree/main/sd_files/interpreter).

You can copy your favourite scripts in the `/scripts` folder and have them listed in the ["Scripts" menu](https://github.com/BruceDevices/firmware/issues/258).

For easier development, you can use [this bash script to upload code directly via serial](https://github.com/eadmaster/Bruce/blob/53090dabfdfb1ed19314b99abb441961c29469b2/bin/bruceserialrun).

API Reference for Native Functions Accessible from JS:

* [General](#general)
* [Board info](#get-some-board-information)
* [WiFi](#wifi-functions)
* [TFT Display](#tft-display-functions)
* [Keyboard](#keyboard-inputs)
* [Storage](#storage-functions)
* [Serial](#serial-functions)
* [Audio](#audio-functions)
* [IR](#ir-functions)
* [Subghz](#subghzrf-functions)
* [Badusb](#badusb-functions)

[New alternative Flipper-like API](https://github.com/Tawank/bruce-js-tooling/)

## General

### `load(script: string)`

**Description**: Sets the script to be executed next when the current script ends.

**Parameters**:
* `script` (string): The JavaScript code as a string to be loaded.

**Returns**: `void`

---

### `now()`

**Description**: Returns the current time in milliseconds since the epoch.

**Returns**: `number`: The current timestamp in milliseconds.

---

### `delay(ms: number)`

**Description**: Pauses execution for the specified number of milliseconds.

**Parameters**:
* `ms` (number): The number of milliseconds to delay.

**Returns**: `void`

---

### `digitalWrite(pin: number, value: boolean)`

**Description**: Sets the digital value (HIGH or LOW) for a specified pin.

**Parameters**:
* `pin` (number): The pin number.
* `value` (boolean): The value to write (true for HIGH, false for LOW).

**Returns**: `void`

---

### `analogWrite(pin: number, value: int)`

[same as arduino's function](https://docs.arduino.cc/language-reference/en/functions/analog-io/analogWrite/)

---

### `digitalRead(pin: number)`

[same as arduino's function](https://docs.arduino.cc/language-reference/en/functions/digital-io/digitalread/)

---

### `analogRead(pin: number)`

[same as arduino's function](https://docs.arduino.cc/language-reference/en/functions/analog-io/analogRead/)

---

### `pinMode(pin: number, mode: number)`

**Description**: Configures the specified pin to behave as an input or an output.

**Parameters**:
* `pin` (number): The pin number.
* `mode` (number): The mode to set (INPUT, OUTPUT, etc.).

**Returns**: `void`

---

## Get some board information

### `getBattery`
**Description**: Get and integer from 0 to 100 relative to the battery level.

**Returns**: `number`: 0-100 battery level.

---
### `getBoard`
**Description**: Get and integer from 0 to 100 relative to the battery level.

**Returns**: `string[]`: "StickCPlus", "StickCPlus2", "Cardputer", "Core2", "Core", "CoreS3/SE".

---
## WiFi functions


### `wifiConnectDialog()`

**Description**: Open Bruce WiFi connection menu.

---

### `wifiConnect(ssid : string)`
### `wifiConnect(ssid : string, timeout_in_seconds : number)`
### `wifiConnect(ssid : string, timeout_in_seconds : number, pwd : string)`

**Description**: Connects to the specified WiFi network (without opening any menu).

**Parameters**:
* `ssid` (string): Network name.
* `timeout_in_seconds` (number): retry connecting. Defaults to 10 seconds if unspecified.
* `pwd` (string): Network password (optional for open networks)

**Returns**: boolean. Returns `true` if connection was successful, `false` otherwise.

---
### `wifiDisconnect()`

**Description**: Disconnect WiFi.

---

### `wifiScan()`

**Description**: scan for available WiFi networks. Also disconnect from current WiFi network to perform the scan.

**Returns**: `object`: An object array with 3 properties:
    * `SSID` (string): The network name.
    * `encryptionType` (string): The network encryptionType. Possible values: `"UNKNOWN", "TKIP/WPA", "WEP","CCMP/WPA","NONE","AUTO"`
    * `MAC` (string): The AP MAC address.

---

### `httpGet(url: string)`
### `httpGet(url: string, headers: string[])`

**Description**: Performs an HTTP GET request to the specified URL. Optionally includes headers.

**Parameters**:
* `url` (string): The URL to send the GET request to.
* `headers` (string[]): An array of headers to include in the request. Headers should be provided as key-value pairs in the array. Example: `[
    "Content-Type", "application/json",
    "Authorization", "Bearer your_token_here",
    "Accept", "application/json"
]`

**Returns**: `object`: An object with two properties:
    * `response` (number): The HTTP response code.
    * `body` (string): The response body.

---

## TFT Display Functions

### `color(r: number, g: number, b: number)`

**Description**: Creates a color from the given RGB values.

**Parameters**:
* `r` (number): The red component (0-255).
* `g` (number): The green component (0-255).
* `b` (number): The blue component (0-255).

**Returns**: `number`: The color value in 16-bit RGB format.

---

### `setTextColor(color: number)`

**Description**: Sets the text color for drawing operations.

**Parameters**:
* `color` (number): The color value in 16-bit RGB format.

**Returns**: `void`

---

### `setTextSize(size: number)`

**Description**: Sets the text size for drawing operations.

**Parameters**:
* `size` (number): The text size multiplier.

**Returns**: `void`

---

### `drawRect(x: number, y: number, width: number, height: number, color: number)`

**Description**: Draws a rectangle with the specified parameters.

**Parameters**:
* `x` (number): The x-coordinate of the top-left corner.
* `y` (number): The y-coordinate of the top-left corner.
* `width` (number): The width of the rectangle.
* `height` (number): The height of the rectangle.
* `color` (number): The color value in 16-bit RGB format.

**Returns**: `void`

---

### `drawFillRect(x: number, y: number, width: number, height: number, color: number)`

**Description**: Draws a filled rectangle with the specified parameters.

**Parameters**:
* `x` (number): The x-coordinate of the top-left corner.
* `y` (number): The y-coordinate of the top-left corner.
* `width` (number): The width of the rectangle.
* `height` (number): The height of the rectangle.
* `color` (number): The color value in 16-bit RGB format.

**Returns**: `void`

---

### `drawString(text: string, x: number, y: number)`

**Description**: Draws the specified text at the given coordinates.

**Parameters**:
* `text` (string): The text to draw.
* `x` (number): The x-coordinate where the text starts.
* `y` (number): The y-coordinate where the text starts.

**Returns**: `void`

---

### `width()`

**Description**: Returns the width of the display.

**Returns**: `number`: The width of the display in pixels.

---

### `height()`

**Description**: Returns the height of the display.

**Returns**: `number`: The height of the display in pixels.

---

### `drawLine(x: number, y: number, x2: number, y2: number, color: number)`

**Description**: Draws a line with the specified parameters.

**Parameters**:

* `x` (number): The x-coordinate of the starting point.
* `y` (number): The y-coordinate of the starting point.
* `x2` (number): The x-coordinate of the final point.
* `y2` (number): The y-coordinate of the final point.
* `color` (number): The color value in 16-bit RGB format.

**Returns**: `void`

---

### `drawPixel(x: number, y: number, color: number)`

**Description**: Draws a single pixel on screen.

**Parameters**:

* `x` (number): The x-coordinate of the pixel.
* `y` (number): The y-coordinate of the pixel.
* `color` (number): The color value in 16-bit RGB format.

**Returns**: `void`


### `fillScreen(color: number)`

**Description**: Fill the screen with the specified color.

**Parameters**:

* `color` (number): The color value in 16-bit RGB format.

**Returns**: `void`

---

### `dialogMessage(msg : string)`

**Description**: Show a message dialog.

**Parameters**:

* `msg` (string): The message text.

**Returns**: `void`

---

### `dialogError(msg : string)`

**Description**: Show an error message dialog.

**Parameters**:

* `msg` (string): The message text.

**Returns**: `void`

---

### `dialogPickFile(path : string)`

**Description**: Show a file picker dialog.

**Parameters**:

* `path` (string): Root path (optional, defaults to SDcard root if unspecified)

**Returns**: `string`. Selected file full path, or empty string if cancelled.

---

### `dialogChoice(choices : string[])`

**Description**: Show a dialog with multiple choices.

**Parameters**:

* `choices`: an all the choices to show.  Choices should be provided as key-value pairs in the array. Example: `[ "choice1", "return_val1", "choice2", "return_val2", ...]`

**Returns**: `string`. Selected choice value, or empty string if cancelled.


### `dialogViewFile(path : string)`

**Description**: Show a file viewer dialog.

**Parameters**:

* `path` (string): file pathname to view.

**Returns**: `void`

---

### `keyboard()`
### `keyboard(initval : string)`
### `keyboard(initval : string, maxlen : number)`
### `keyboard(initval : string, maxlen : number,  title : string)`

**Description**: Show a text input dialog.

**Parameters**:

* `initval` (string): initial text field value (optional).
* `maxlen` (number): max input string lenght (optional).
* `title` (string): dialog title (optional).

**Returns**: `string`. Typed string, or empty string if cancelled.

---

## Keyboard Inputs

### `getAnyPress()`

**Description**: check if any key/button is pressed;

**Returns**: `boolean`: Returns `true` if any key/button is pressed, or `false` otherwise.

---

### `getPrevPress()`

**Description**: Return the current state of 'Previous' button;

**Returns**: `boolean`: Returns `true` if pressed and `false` if not pressed

---
### `getSelPress()`

**Description**: Return the current state of 'Select' button;

**Returns**: `boolean`: Returns `true` if pressed and `false` if not pressed

---

### `getNextPress()`

**Description**: Return the current state of 'Next' button;

**Returns**: `boolean`: Returns `true` if pressed and `false` if not pressed

---
### `getKeysPressed()`

**Restriction**: Works only in Cardputer

**Description**: Returns the current state of the keys pressed on the M5Cardputer.

**Returns**: `string[]`: An array of strings representing the pressed keys. Possible values include "Delete", "Enter", "Alt", "Tab", "Function", "Option", or the actual key character.

---


## Storage functions

### `storageRead(filename : string) : string`

**Description**: Read a small text file with a single call (<4kb).

**Parameters**:

* `filename` (string): file pathname to read.

**Returns**: `string`. File contents, or empty string if not found or any other error.

---

### `storageWrite(filename : string, data : string)`

**Description**: Write a small text file. Append if file exist.


**Parameters**:

* `filename` (string): path of the file to write.
* `data` (string): string to write.

**Returns**: `boolean`: Returns `true` if successfull or `false` if failed

---

## Serial functions

### `print(message: string)`

**Description**: Prints the given message to the Serial monitor.

**Parameters**:
* `message` (string): The message to print.

**Returns**: `void`


### `serialReadln(timeout_in_ms : number)`

**Description**: Read a single line from the serial terminal;

**Parameters**:

* `timeout_in_ms` (number): timeout in milli-seconds. Defaults to 10s if unspecified.

**Returns**: `string`: Returns the line received

---

### `serialCmd(cmd : string)`

**Description**: Execute a supported serial command (see the [list here](../controlling-device/serial.md));

**Parameters**:

* `cmd` (string): serial command to execute.

**Returns**: `boolean`: Returns `true` if successfull or `false` if command failed

---

## Audio functions

### `playAudioFile(filename : string)`

### `tone(frequency : number)`
### `tone(frequency : number, duration : number)`

---

## IR functions

### `irTransmitFile(filename : string)`

### `irTransmit(data: string, protocol : string = "NEC", bits: int = 32)`

### `irRead(timeout_in_seconds : number) : string`

### `irReadRaw(timeout_in_seconds : number) : string`

---

## SubGhz/RF functions

### `subghzTransmitFile(filename : string)`

### `subghzTransmit(subghzTransmit(data : string, frequency : int = 433920000, te : int = 174, count : int = 10))`

### `subghzRead(timeout_in_seconds : number) : string`

### `subghzReadRaw(timeout_in_seconds : number) : string`

### `subghzSetFrequency(freq : float)`

---

## Badusb functions

### `badusbSetup()`

### `badusbRunFile(filename : string)`

### `badusbPrint(msg : string)`

### `badusbPrintln(msg : string)`

### `badusbPress(keycode : number)`

[keycodes list](https://github.com/espressif/arduino-esp32/blob/master/libraries/USB/src/USBHIDKeyboard.h)

### `badusbHold(keycode : number)`

### `badusbRelease(keycode : number)`

### `badusbReleaseAll()`

### `badusbPressRaw(hid_key : number)`

[TinyUSB's HID_KEY_* macros](https://github.com/hathach/tinyusb/blob/master/src/class/hid/hid.h)

---
