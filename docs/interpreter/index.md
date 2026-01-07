---
tags:
 -  _TODO
todo: Formatting
      Needs updating with all recent JS changes by Matt (@emericklaw)
      Possible changes with the upcomming migration away from Duktape
      Go through each function and check documentation
---
# Interpreter

## Guide for Coding with JavaScript in Bruce

The Bruce JS Interpreter allows you to create scripts or even games using JavaScript.

For legacy version of this page go here: [Interpreter Legacy](legacy.md)

Some [example scripts are here](https://github.com/BruceDevices/firmware/tree/main/sd_files/interpreter).

You can copy your favourite scripts in the `/scripts` folder and have them listed in the ["Scripts" menu](https://github.com/BruceDevices/firmware/issues/258).

For easier development, you can use [this bash script to upload code directly via serial](https://github.com/eadmaster/Bruce/blob/53090dabfdfb1ed19314b99abb441961c29469b2/bin/bruceserialrun). (linux only)
Or this [TypeScript developement SDK](typescript.md) works in linux and windows.

### Important: JavaScript Compatibility

* ❌ **No modern JavaScript features** – Use only ES5 syntax:
    * ❌ **No** `let` (use `var` instead).
    * ❌ **No** `for...of` loops (use traditional `for` loops with indexes).
    * ❌ **No** Arrow functions `() => {}` (use `function` instead).
    * ❌ **No** `import ir from 'ir'` import syntax use `const ir = require('ir');` instead.
    * ❌ **No** External npm module imports.
    * ❌ **No** `Object.entries()`, `Promise`, `async/await`, `setInterval`, `setTimeout` (for now).

If you want modern JS features as well as TypeScript support you can go [here](typescript.md)

### Uploading Your Scripts to Bruce

When uploading scripts to Bruce, keep in mind:

* **Only JavaScript (`.js`) files can be uploaded.**
* If you have **TypeScript (`.ts`)** files, you must first [**compile it to JavaScript** (`.js`)](typescript.md).

## Documentation

## Modules

| Module | Description |
| ------------------------------- | ----------------------------------------------------- |
| [globals](#globals) | Global variables and functions available in bruce js. |
| [audio](#audiomd) | Controls the audio. |
| [badusb](#badusbmd) | Simulates USB keyboard input. |
| [device](#devicemd) | Show device information. |
| [dialog](#dialogmd) | Displays dialog messages and user interactions. |
| [display](#displaymd) | Controls the TFT Display |
| [gpio](#gpiomd) | GPIO (General Purpose Input/Output) ports |
| [ir](#irmd) | Interacting with infrared (IR) signals. |
| [keyboard](#keyboardmd) | Access the keyboard input. |
| [notification](#notificationmd) | Controls the notification LED. |
| [serial](#serialmd) | Serial communication. |
| [storage](#storagemd) | File storage operations. |
| [subghz](#subghzmd) | Sub-GHz communication. |
| [wifi](#wifimd) | Wi-Fi connection and HTTP requests. |

## globals

Global variables and functions available in bruce js.

### Example

```js
console.log(now()); // console.log prints to serial monitor
delay(1000); // Pause for 1000ms (1 second)
var myNumber = parse_int("2133"); // myNumber will be number 2133
var myString = to_string(24); // myString will be string "24"
var myHexStr = to_hex_string(1337); // myHexStr will be string "539" (because 1337 decimal is 0x539).
var str = "Hello World";
println(to_lower_case(str)); // prints: hello world
println(to_upper_case(str)); // prints: HELLO WORLD
println(random(1, 20)); // prints random value between 1 and 20
println(__dirname); // prints current dirname
```

<!-- index-start -->
## globals functions

* [Path](#path)
* [FileSystem](#filesystem)
* [BRUCE_VERSION](#bruce_version)
* [BRUCE_PRICOLOR](#bruce_pricolor)
* [BRUCE_SECCOLOR](#bruce_seccolor)
* [BRUCE_BGCOLOR](#bruce_bgcolor)
* [now()](#now)
* [delay()](#delay)
* [parse_int()](#parse_int)
* [to_string()](#to_string)
* [to_hex_string()](#to_hex_string)
* [to_lower_case()](#to_lower_case)
* [to_upper_case()](#to_upper_case)
* [random()](#random)
* [println()](#println)
* [require()](#require)
* [assert()](#assert)
<!-- index-end -->

## Path

Represents a file path along with its storage location.

### Example

```js
const dialog = require("dialog");
dialog.pickFile({ fs: "user", path: "/" });
```

### Properties

| Property | Type | Description |
| -------------------------- | --------------------------- | ------------------------------------------------ |
| <a id="fs"></a> `fs` | [`FileSystem`](#filesystem) | The storage medium where the file is located |
| <a id="path-1"></a> `path` | `string` | The file path within the selected storage medium |

---

## FileSystem

```ts
type FileSystem = "sd" | "littlefs" | null;
```

Represents the storage medium where a file is located.

* `'sd'` - File stored on SD card.
* `'littlefs'` - File stored on LittleFS.
* `null` - Automatically choose between SD card (if available) and LittleFS as a fallback.

---

## \_\_dirname

```ts
const __dirname: string;
```

Path to the directory containing the current script

---

## \_\_filename

```ts
const __filename: string;
```

Path to the current script file

---

## BRUCE_VERSION

```ts
const BRUCE_VERSION: string;
```

Version of the bruce firmware

---

## BRUCE_PRICOLOR

```ts
const BRUCE_PRICOLOR: number;
```

Primary color from the config

---

## BRUCE_SECCOLOR

```ts
const BRUCE_SECCOLOR: number;
```

Secondary color from the config

---

## BRUCE_BGCOLOR

```ts
const BRUCE_BGCOLOR: number;
```

Background color from the config

---

## now()

```ts
now(): number;
```

Returns the current time in milliseconds since the epoch

### Returns

`number`

The current timestamp in milliseconds

---

## delay()

```ts
delay(ms: number): void;
```

Pauses execution for the specified number of milliseconds

### Parameters

| Parameter | Type | Description |
| --------- | -------- | ----------------------------------- |
| `ms` | `number` | The number of milliseconds to delay |

### Returns

`void`

---

## parse_int()

```ts
parse_int(text: string): number;
```

Converts a string to a number

### Parameters

| Parameter | Type | Description |
| --------- | -------- | --------------------------------- |
| `text` | `string` | The string to convert to a number |

### Returns

`number`

---

## to_string()

```ts
to_string(value: any): string;
```

Converts a value to a string

### Parameters

| Parameter | Type | Description |
| --------- | ----- | -------------------------------- |
| `value` | `any` | The value to convert to a string |

### Returns

`string`

---

## to_hex_string()

```ts
to_hex_string(text: string): string;
```

Converts a hex string to a number

### Parameters

| Parameter | Type | Description |
| --------- | -------- | ------------------------------------- |
| `text` | `string` | The hex string to convert to a number |

### Returns

`string`

---

## to_lower_case()

```ts
to_lower_case(text: string): string;
```

Converts a string to a number

### Parameters

| Parameter | Type | Description |
| --------- | -------- | --------------------------------- |
| `text` | `string` | The string to convert to a number |

### Returns

`string`

---

## to_upper_case()

```ts
to_upper_case(text: string): string;
```

Converts a string to a number

### Parameters

| Parameter | Type | Description |
| --------- | -------- | --------------------------------- |
| `text` | `string` | The string to convert to a number |

### Returns

`string`

---

## random()

### Call Signature

```ts
random(min: number, max: number): number;
```

Returns a pseudo-random number

#### Parameters

| Parameter | Type | Description |
| --------- | -------- | ----------------------------------------------------- |
| `min` | `number` | Lower bound of the random value, inclusive (optional) |
| `max` | `number` | Upper bound of the random value, exclusive |

#### Returns

`number`

### Call Signature

```ts
random(max: number): number;
```

Returns a pseudo-random number

#### Parameters

| Parameter | Type | Description |
| --------- | -------- | ------------------------------------------ |
| `max` | `number` | Upper bound of the random value, exclusive |

#### Returns

`number`

---

## println()

```ts
println(...args: any[]): void;
```

Prints text to serial monitor and display. Alias to [display.println](#displayprintln)().

### Parameters

| Parameter | Type | Description |
| --------- | ------- | ------------- |
| ...`args` | `any`[] | Text content. |

### Returns

`void`

---

## require()

```ts
require(module: string): any;
```

Loads a module. List of modules: [modules](#modules).

### Example

```js
const dialog = require("dialog");
dialog.message("Test.");
```

### Parameters

| Parameter | Type | Description |
| --------- | -------- | ------------------------------ |
| `module` | `string` | The name of the module to load |

### Returns

`any`

---

## assert()

```ts
assert(assertion: boolean, message?: string): boolean;
```

Throws an error if the assertion is false.

### Example

```js
assert(2 + 2 === 5); // Throws an error
```

### Parameters

| Parameter | Type | Description |
| ----------- | --------- | --------------------------------------- |
| `assertion` | `boolean` | Any boolean expression |
| `message`? | `string` | The Error message if assertion is false |

### Returns

`boolean`


<a name="audiomd"></a>

# audio

Controls the audio.

### Example

```js
const audio = require("audio");

audio.tone(988, 2000); // Beeps buzzer for 2 seconds
```

<!-- index-start -->
## audio functions

* [audio.playFile()](#audioplayfile)
* [audio.tone()](#audiotone)
<!-- index-end -->

## audio.playFile()

```ts
audio.playFile(filename: string): void;
```

Plays an audio file from storage.

### Parameters

| Parameter | Type | Description |
| ---------- | -------- | ------------------------------------------------ |
| `filename` | `string` | The path to the audio file (e.g., "/sound.wav"). |

### Returns

`void`

---

## audio.tone()

```ts
audio.tone(
  frequency: number,
  durationMs: number,
  nonBlocking?: boolean,
): void;
```

Plays a tone at the specified frequency for a given duration.

### Parameters

| Parameter | Type | Description |
| -------------- | --------- | ------------------------------------------------------------------------- |
| `frequency` | `number` | Frequency in Hz. |
| `durationMs` | `number` | Duration in milliseconds. |
| `nonBlocking`? | `boolean` | If true, the function will not play the tone if it would block execution. |

### Returns

`void`


<a name="badusbmd"></a>

# badusb

Simulates USB keyboard input.

### Example

```js
const badusb = require("badusb");

badusb.setup();
badusb.press(0x04); // Presses the 'A' key
badusb.release(0x04); // Releases the 'A' key
badusb.println("Hello, world!"); // Types and presses Enter
```

<!-- index-start -->
## badusb functions

* [badusb.setup()](#badusbsetup)
* [badusb.press()](#badusbpress)
* [badusb.pressRaw()](#badusbpressraw)
* [badusb.hold()](#badusbhold)
* [badusb.release()](#badusbrelease)
* [badusb.releaseAll()](#badusbreleaseall)
* [badusb.print()](#badusbprint)
* [badusb.println()](#badusbprintln)
* [badusb.runFile()](#badusbrunfile)
<!-- index-end -->

## badusb.setup()

```ts
badusb.setup(): void;
```

Initializes the BadUSB module.

### Returns

`void`

---

## badusb.press()

```ts
badusb.press(keycode: number): void;
```

Simulates pressing a key using a keycode.

### Parameters

| Parameter | Type | Description |
| --------- | -------- | -------------------------------- |
| `keycode` | `number` | HID keycode of the key to press. |

### Returns

`void`

---

## badusb.pressRaw()

```ts
badusb.pressRaw(hid_key: number): void;
```

Simulates pressing a key using a raw HID key value.

### Parameters

| Parameter | Type | Description |
| --------- | -------- | ------------------ |
| `hid_key` | `number` | Raw HID key value. |

### Returns

`void`

---

## badusb.hold()

```ts
badusb.hold(keycode: number): void;
```

Simulates holding down a key.

### Parameters

| Parameter | Type | Description |
| --------- | -------- | ------------------------------- |
| `keycode` | `number` | HID keycode of the key to hold. |

### Returns

`void`

---

## badusb.release()

```ts
badusb.release(keycode: number): void;
```

Releases a previously pressed key.

### Parameters

| Parameter | Type | Description |
| --------- | -------- | ---------------------------------- |
| `keycode` | `number` | HID keycode of the key to release. |

### Returns

`void`

---

## badusb.releaseAll()

```ts
badusb.releaseAll(): void;
```

Releases all currently held keys.

### Returns

`void`

---

## badusb.print()

```ts
badusb.print(message: string): void;
```

Types a string as keyboard input.

### Parameters

| Parameter | Type | Description |
| --------- | -------- | ----------------- |
| `message` | `string` | The text to type. |

### Returns

`void`

---

## badusb.println()

```ts
badusb.println(message: string): void;
```

Types a string followed by an "Enter" key press.

### Parameters

| Parameter | Type | Description |
| --------- | -------- | ----------------- |
| `message` | `string` | The text to type. |

### Returns

`void`

---

## badusb.runFile()

```ts
badusb.runFile(filename: string): void;
```

Executes a script file from storage.

### Parameters

| Parameter | Type | Description |
| ---------- | -------- | ---------------------------- |
| `filename` | `string` | The path to the script file. |

### Returns

`void`


<a name="devicemd"></a>

# device

Show device information.

### Example

```js
const device = require("device");

console.log("Device Name:", device.getName());
console.log("Board:", device.getBoard());
console.log("Model:", device.getModel());
console.log("Battery Charge:", device.getBatteryCharge(), "%");

const memoryStats = device.getFreeHeapSize();
console.log("RAM Free:", memoryStats.ram_free);
console.log("PSRAM Free:", memoryStats.psram_free);
```

<!-- index-start -->
## device functions

* [device.getName()](#devicegetname)
* [device.getBoard()](#devicegetboard)
* [device.getModel()](#devicegetmodel)
* [device.getBatteryCharge()](#devicegetbatterycharge)
* [device.getFreeHeapSize()](#devicegetfreeheapsize)
<!-- index-end -->

## device.getName()

```ts
device.getName(): string;
```

Retrieves the device's name.

### Returns

`string`

The name of the device.

---

## device.getBoard()

```ts
device.getBoard(): string;
```

Retrieves the board type.

### Returns

`string`

The board identifier.

---

## device.getModel()

```ts
device.getModel(): string;
```

Retrieves the model name.

### Returns

`string`

The model of the device.

---

## device.getBatteryCharge()

```ts
device.getBatteryCharge(): number;
```

Retrieves the current battery charge level.

### Returns

`number`

The battery charge percentage (0-100).

---

## device.getFreeHeapSize()

```ts
device.getFreeHeapSize(): {
  ram_free: number;
  ram_min_free: number;
  ram_largest_free_block: number;
  ram_size: number;
  psram_free: number;
  psram_size: number;
};
```

Retrieves information about the device's memory usage.

### Returns

```ts
{
  ram_free: number;
  ram_min_free: number;
  ram_largest_free_block: number;
  ram_size: number;
  psram_free: number;
  psram_size: number;
}
```

An object containing RAM and PSRAM statistics:

* `ram_free`: Free RAM available (in bytes).
* `ram_min_free`: Minimum free RAM recorded (in bytes).
* `ram_largest_free_block`: Largest contiguous free RAM block (in bytes).
* `ram_size`: Total RAM size (in bytes).
* `psram_free`: Free PSRAM available (in bytes).
* `psram_size`: Total PSRAM size (in bytes).

| Name | Type |
| ------------------------ | -------- |
| `ram_free` | `number` |
| `ram_min_free` | `number` |
| `ram_largest_free_block` | `number` |
| `ram_size` | `number` |
| `psram_free` | `number` |
| `psram_size` | `number` |


<a name="dialogmd"></a>

# dialog

Displays dialog messages and user interactions.

### Example

```js
const dialog = require("dialog");

dialog.success("Operation completed successfully.");
dialog.error("An error occurred!", true);

const options = ["Yes", "No", "Cancel"];
const selected = dialog.choice(options);

const filePath = dialog.pickFile("/documents", "txt");
dialog.viewFile(filePath);
```

<!-- index-start -->
## dialog functions

* [dialog.drawStatusBar()](#dialogdrawstatusbar)
* [dialog.message()](#dialogmessage)
* [dialog.info()](#dialoginfo)
* [dialog.success()](#dialogsuccess)
* [dialog.warning()](#dialogwarning)
* [dialog.error()](#dialogerror)
* [dialog.choice()](#dialogchoice)
* [dialog.pickFile()](#dialogpickfile)
* [dialog.prompt()](#dialogprompt)
* [dialog.viewFile()](#dialogviewfile)
* [dialog.viewText()](#dialogviewtext)
* [dialog.createTextViewer()](#dialogcreatetextviewer)
<!-- index-end -->

## dialog.drawStatusBar()

```ts
dialog.drawStatusBar(): void;
```

Draws Bruce's status bar

### Returns

`void`

---

## dialog.message()

```ts
dialog.message(
  message: string,
  buttons?: {
    left: string;
    center: string;
    right: string;
  },
): void | "left" | "center" | "right";
```

Displays a message dialog with up to three choices.

### Example

```js
const dialog = require("dialog");
dialog.message("Hello!"); // Just displays the message
dialog.message("Press any key...", true); // Blocks until a key is pressed
const choice = dialog.message("Choose:", {
  left: "No",
  center: "Maybe",
  right: "Yes",
});
if (choice === "right") console.log("User chose Yes!");
```

### Parameters

| Parameter | Type | Description |
| ----------------- | -------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------- |
| `message` | `string` | The message to display. |
| `buttons`? | \{ `left`: `string`; `center`: `string`; `right`: `string`; \} | If `true`, waits for a key press before closing (default: `false`). If an object, displays up to three buttons with custom labels. |
| `buttons.left`? | `string` | - |
| `buttons.center`? | `string` | - |
| `buttons.right`? | `string` | - |

### Returns

`void` \| `"left"` \| `"center"` \| `"right"`

The button pressed (`"left"`, `"center"`, or `"right"`), or `void` if no buttons are used.

---

## dialog.info()

```ts
dialog.info(message: string, waitForKeyPress?: boolean): void;
```

Displays an info notification (Blue background).

### Example

```js
const dialog = require("dialog");
dialog.info("Operation completed successfully.");
```

### Parameters

| Parameter | Type | Description |
| ------------------ | --------- | ------------------------------------------------------------------- |
| `message` | `string` | The info message to display. |
| `waitForKeyPress`? | `boolean` | If `true`, waits for a key press before closing (default: `false`). |

### Returns

`void`

---

## dialog.success()

```ts
dialog.success(message: string, waitForKeyPress?: boolean): void;
```

Displays an success notification (Green background).

### Example

```js
const dialog = require("dialog");
dialog.success("Operation completed successfully.");
```

### Parameters

| Parameter | Type | Description |
| ------------------ | --------- | ------------------------------------------------------------------- |
| `message` | `string` | The success message to display. |
| `waitForKeyPress`? | `boolean` | If `true`, waits for a key press before closing (default: `false`). |

### Returns

`void`

---

## dialog.warning()

```ts
dialog.warning(message: string, waitForKeyPress?: boolean): void;
```

Displays an warning notification (Yellow background).

### Example

```js
const dialog = require("dialog");
dialog.warning("Warning!");
```

### Parameters

| Parameter | Type | Description |
| ------------------ | --------- | ------------------------------------------------------------------- |
| `message` | `string` | The warning message to display. |
| `waitForKeyPress`? | `boolean` | If `true`, waits for a key press before closing (default: `false`). |

### Returns

`void`

---

## dialog.error()

```ts
dialog.error(message: string, waitForKeyPress?: boolean): void;
```

Displays an error notification (Red background).

### Example

```js
const dialog = require("dialog");
dialog.error("An error occurred!", true);
```

### Parameters

| Parameter | Type | Description |
| ------------------ | --------- | ------------------------------------------------------------------- |
| `message` | `string` | The error message to display. |
| `waitForKeyPress`? | `boolean` | If `true`, waits for a key press before closing (default: `false`). |

### Returns

`void`

---

## dialog.choice()

```ts
dialog.choice(
  values: string[] | [string, string][] | {} | string[][],
): string;
```

Displays a choice dialog and returns the selected option.

### Example

```js
const dialog = require("dialog");
const options = ["Yes", "No", "Cancel"];
const selected = dialog.choice(options);
console.log("Selected:", selected); // it should print "Yes", "No" or "Cancel"

const optionsNestedArray = [
  ["Go Back", "go_back"],
  ["Cancel", "cancel"],
  ["Quit", "quit"],
];
const selectedNestedArray = dialog.choice(optionsNestedArray);
console.log("selectedNestedArray:", selectedNestedArray); // it should print "go_back", "cancel" or "quit"

const optionsObject = { "Go Back": "go_back", Cancel: "cancel", Quit: "quit" };
const selectedObject = dialog.choice(optionsObject);
console.log("selectedObject:", selectedObject); // it should print "go_back", "cancel" or "quit"
```

### Parameters

| Parameter | Type | Description |
| --------- | -------------------------------------------------------------- | ----------------------------------------------------------------------- |
| `values` | `string`[] \| \[`string`, `string`\][] \| \{\} \| `string`[][] | An array of options to choose from. Can also be nested array or object. |

### Returns

`string`

The selected option as a string.

---

## dialog.pickFile()

```ts
dialog.pickFile(path?: string, extension?: string): string;
```

Opens a file picker dialog and returns the selected file path.

### Example

```js
const dialog = require("dialog");
const filePath = dialog.pickFile("/documents", "txt");
dialog.viewFile(filePath);
```

### Parameters

| Parameter | Type | Description |
| ------------ | -------- | -------------------------------------- |
| `path`? | `string` | The initial directory path (optional). |
| `extension`? | `string` | The file extension filter (optional). |

### Returns

`string`

The selected file path or `null` if no file is chosen.

---

## dialog.prompt()

```ts
dialog.prompt(title?: string, valueLength?: number, value?: string): string;
```

Opens an on-screen keyboard for user input.

### Parameters

| Parameter | Type | Description |
| -------------- | -------- | ---------------------------------- |
| `title`? | `string` | Title of the keyboard prompt. |
| `valueLength`? | `number` | Maximum length of the input value. |
| `value`? | `string` | Initial value to display. |

### Returns

`string`

User input.

---

## dialog.viewFile()

```ts
dialog.viewFile(path: string): void;
```

Opens and displays a file in a viewer.
Displays a window where the user can scroll and exit.
Blocks execution until the user exits.

### Example

```js
const dialog = require("dialog");
const filePath = dialog.pickFile("/documents", "txt");
dialog.viewFile(filePath);
```

### Parameters

| Parameter | Type | Description |
| --------- | -------- | ---------------------- |
| `path` | `string` | The file path to view. |

### Returns

`void`

---

## dialog.viewText()

```ts
dialog.viewText(text: string, title?: string): void;
```

Opens and displays text in a viewer.
Displays a window where the user can scroll and exit.
Blocks execution until the user exits.

### Example

```js
const dialog = require("dialog");
dialog.viewText("text to display");
```

### Parameters

| Parameter | Type | Description |
| --------- | -------- | ---------------------------------------- |
| `text` | `string` | The text to view. |
| `title`? | `string` | The optional title of the viewer window. |

### Returns

`void`

---

## dialog.createTextViewer()

```ts
dialog.createTextViewer(
  text: string,
  options?: {
    fontSize: number;
    startX: number;
    startY: number;
    width: number;
    height: number;
    indentWrappedLines: boolean;
  },
): TextViewer;
```

Creates a `TextViewer` instance, allowing manual control.
Unlike `viewText`, this does **not** block execution.
You must handle scrolling and closing yourself.
Thanks to this you can implement: scrolling to section,
follow links in text, and much more.
You can find example of the implementation here:
https://github.com/Tawank/bruce-js-tooling/tree/master/examples/browser-bruce

### Example

```js
const dialog = require("dialog");
const textViewer = dialog.createTextViewer("long text");
while (true) {
  if (keyboard.getPrevPress()) {
    textViewer.scrollUp();
  }
  if (keyboard.getNextPress()) {
    textViewer.scrollDown();
  }
  textViewer.draw();
  delay(100);
}
```

### Parameters

| Parameter | Type | Description |
| ----------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------ |
| `text` | `string` | The text to view. |
| `options`? | \{ `fontSize`: `number`; `startX`: `number`; `startY`: `number`; `width`: `number`; `height`: `number`; `indentWrappedLines`: `boolean`; \} | The text viewer options. |
| `options.fontSize`? | `number` | - |
| `options.startX`? | `number` | - |
| `options.startY`? | `number` | - |
| `options.width`? | `number` | - |
| `options.height`? | `number` | - |
| `options.indentWrappedLines`? | `boolean` | - |

### Returns

`TextViewer`

A `TextViewer` instance with manual controls.


<a name="displaymd"></a>

# display

Controls the TFT Display

### Example

```js
const display = require("display");

const black = display.color(0, 0, 0);
const white = display.color(255, 255, 255);

display.fill(black);
display.drawFillRect(20, 20, 50, 50, white);

delay(2000);
```


<!-- index-start -->
## display functions

* [display.color()](#displaycolor)
* [display.fill()](#displayfill)
* [display.setCursor()](#displaysetcursor)
* [display.print()](#displayprint)
* [display.println()](#displayprintln)
* [display.setTextColor()](#displaysettextcolor)
* [display.setTextAlign()](#displaysettextalign)
* [display.setTextSize()](#displaysettextsize)
* [display.drawText()](#displaydrawtext)
* [display.drawString()](#displaydrawstring)
* [display.drawPixel()](#displaydrawpixel)
* [display.drawLine()](#displaydrawline)
* [display.drawRect()](#displaydrawrect)
* [display.drawFillRect()](#displaydrawfillrect)
* [display.drawFillRectGradient()](#displaydrawfillrectgradient)
* [display.drawRoundRect()](#displaydrawroundrect)
* [display.drawFillRoundRect()](#displaydrawfillroundrect)
* [display.drawCircle()](#displaydrawcircle)
* [display.drawFillCircle()](#displaydrawfillcircle)
* [display.drawXBitmap()](#displaydrawxbitmap)
* [display.drawBitmap()](#displaydrawbitmap)
* [display.drawJpg()](#displaydrawjpg)
* [display.drawGif()](#displaydrawgif)
* [display.gifOpen()](#displaygifopen)
* [display.width()](#displaywidth)
* [display.height()](#displayheight)
<!-- index-end -->

## display.color()

```ts
display.color(r: number, g: number, b: number): number;
```

Converts RGB values to a display-compatible color format.

### Parameters

| Parameter | Type | Description |
| --------- | -------- | ------------- |
| `r` | `number` | Red (0-255) |
| `g` | `number` | Green (0-255) |
| `b` | `number` | Blue (0-255) |

### Returns

`number`

A numeric representation of the color.

---

## display.fill()

```ts
display.fill(color: number): void;
```

Fills the entire screen with the specified color.

### Parameters

| Parameter | Type | Description |
| --------- | -------- | ----------------------------------------------------------- |
| `color` | `number` | Color value (use `display.color(r, g, b)` to generate one). |

### Returns

`void`

---

## display.setCursor()

```ts
display.setCursor(x: number, y: number): void;
```

Sets the cursor position for text rendering.

### Parameters

| Parameter | Type | Description |
| --------- | -------- | ------------- |
| `x` | `number` | X-coordinate. |
| `y` | `number` | Y-coordinate. |

### Returns

`void`

---

## display.print()

```ts
display.print(...args: any[]): void;
```

Prints text at the current cursor position.
It also prints text to the Serial Monitor for devices without screen.

### Parameters

| Parameter | Type | Description |
| --------- | ------- | ------------- |
| ...`args` | `any`[] | Text content. |

### Returns

`void`

---

## display.println()

```ts
display.println(...args: any[]): void;
```

Prints text followed by a newline at the current cursor position.
It also prints text to the Serial Monitor for devices without screen.

### Parameters

| Parameter | Type | Description |
| --------- | ------- | ------------- |
| ...`args` | `any`[] | Text content. |

### Returns

`void`

---

## display.setTextColor()

```ts
display.setTextColor(color: number): void;
```

Sets the text color.

### Parameters

| Parameter | Type | Description |
| --------- | -------- | ---------------------------------------------------------- |
| `color` | `number` | Text color (use `display.color(r, g, b)` to generate one). |

### Returns

`void`

---

## display.setTextAlign()

```ts
display.setTextAlign(
  align: number | "left" | "center" | "right",
  baseline?: number | "top" | "middle" | "bottom" | "alphabetic",
): void;
```

Sets the text alignment and baseline for rendering text.

### Example

```js
display.setTextAlign(1, 2); // Center align, bottom baseline
display.drawText(50, 50, "Hello!");
```

### Parameters

| Parameter | Type | Description |
| ----------- | ----------------------------------------------------------------- | --------------------------------- |
| `align` | `number` \| `"left"` \| `"center"` \| `"right"` | Horizontal alignment of the text. |
| `baseline`? | `number` \| `"top"` \| `"middle"` \| `"bottom"` \| `"alphabetic"` | Vertical alignment of the text. |

### Returns

`void`

---

## display.setTextSize()

```ts
display.setTextSize(size: number): void;
```

Sets the text size.

### Parameters

| Parameter | Type | Description |
| --------- | -------- | ----------- |
| `size` | `number` | Text size. |

### Returns

`void`

---

## display.drawText()

```ts
display.drawText(text: string | number | boolean, x: number, y: number): void;
```

Draws text at the specified position.

### Parameters

| Parameter | Type | Description |
| --------- | --------------------------------- | -------------------- |
| `text` | `string` \| `number` \| `boolean` | The text to display. |
| `x` | `number` | X-coordinate. |
| `y` | `number` | Y-coordinate. |

### Returns

`void`

---

## display.drawString()

```ts
display.drawString(
  text: string | number | boolean,
  x: number,
  y: number,
): void;
```

Draws a string at the specified position (alias for `drawText`).

### Parameters

| Parameter | Type |
| --------- | --------------------------------- |
| `text` | `string` \| `number` \| `boolean` |
| `x` | `number` |
| `y` | `number` |

### Returns

`void`

---

## display.drawPixel()

```ts
display.drawPixel(x: number, y: number, color: number): void;
```

Draws a single pixel on the display.

### Parameters

| Parameter | Type | Description |
| --------- | -------- | ----------------------------------------------------------- |
| `x` | `number` | X-coordinate. |
| `y` | `number` | Y-coordinate. |
| `color` | `number` | Pixel color (use `display.color(r, g, b)` to generate one). |

### Returns

`void`

---

## display.drawLine()

```ts
display.drawLine(
  x: number,
  y: number,
  x2: number,
  y2: number,
  color: number,
): void;
```

Draws a line between two points.

### Parameters

| Parameter | Type | Description |
| --------- | -------- | ---------------------------------------------------------- |
| `x` | `number` | Start X-coordinate. |
| `y` | `number` | Start Y-coordinate. |
| `x2` | `number` | End X-coordinate. |
| `y2` | `number` | End Y-coordinate. |
| `color` | `number` | Line color (use `display.color(r, g, b)` to generate one). |

### Returns

`void`

---

## display.drawRect()

```ts
display.drawRect(
  x: number,
  y: number,
  width: number,
  height: number,
  color: number,
): void;
```

Draws a rectangle outline.

### Parameters

| Parameter | Type | Description |
| --------- | -------- | ------------------------------------------------------------- |
| `x` | `number` | X-coordinate. |
| `y` | `number` | Y-coordinate. |
| `width` | `number` | Rectangle width. |
| `height` | `number` | Rectangle height. |
| `color` | `number` | Outline color (use `display.color(r, g, b)` to generate one). |

### Returns

`void`

---

## display.drawFillRect()

```ts
display.drawFillRect(
  x: number,
  y: number,
  width: number,
  height: number,
  color: number,
): void;
```

Draws a filled rectangle.

### Parameters

| Parameter | Type | Description |
| --------- | -------- | ----------------------------------------------------- |
| `x` | `number` | X-coordinate. |
| `y` | `number` | Y-coordinate. |
| `width` | `number` | Rectangle width. |
| `height` | `number` | Rectangle height. |
| `color` | `number` | Color (use `display.color(r, g, b)` to generate one). |

### Returns

`void`

---

## display.drawFillRectGradient()

```ts
display.drawFillRectGradient(
  x: number,
  y: number,
  width: number,
  height: number,
  color1: number,
  color2: number,
  direction: "horizontal" | "vertical",
): void;
```

Draws a filled gradient rectangle.

### Parameters

| Parameter | Type | Description |
| ----------- | ------------------------------ | ------------------------------------------------------- |
| `x` | `number` | X-coordinate. |
| `y` | `number` | Y-coordinate. |
| `width` | `number` | Rectangle width. |
| `height` | `number` | Rectangle height. |
| `color1` | `number` | Color 1 (use `display.color(r, g, b)` to generate one). |
| `color2` | `number` | Color 2 (use `display.color(r, g, b)` to generate one). |
| `direction` | `"horizontal"` \| `"vertical"` | `'horizontal'` or `'vertical'`. |

### Returns

`void`

---

## display.drawRoundRect()

```ts
display.drawRoundRect(
  x: number,
  y: number,
  width: number,
  height: number,
  radius: number,
  color: number,
): void;
```

Draws a round rectangle.

### Parameters

| Parameter | Type | Description |
| --------- | -------- | ------------------------------------------------------------- |
| `x` | `number` | X-coordinate. |
| `y` | `number` | Y-coordinate. |
| `width` | `number` | Rectangle width. |
| `height` | `number` | Rectangle height. |
| `radius` | `number` | Rectangle radius. |
| `color` | `number` | Outline color (use `display.color(r, g, b)` to generate one). |

### Returns

`void`

---

## display.drawFillRoundRect()

```ts
display.drawFillRoundRect(
  x: number,
  y: number,
  width: number,
  height: number,
  radius: number,
  color: number,
): void;
```

Draws a filled round rectangle.

### Parameters

| Parameter | Type | Description |
| --------- | -------- | ----------------------------------------------------- |
| `x` | `number` | X-coordinate. |
| `y` | `number` | Y-coordinate. |
| `width` | `number` | Rectangle width. |
| `height` | `number` | Rectangle height. |
| `radius` | `number` | Rectangle radius. |
| `color` | `number` | Color (use `display.color(r, g, b)` to generate one). |

### Returns

`void`

---

## display.drawCircle()

```ts
display.drawCircle(x: number, y: number, r: number, color: number): void;
```

Draws a circle.

### Parameters

| Parameter | Type | Description |
| --------- | -------- | ------------------------------------------------------------- |
| `x` | `number` | X-coordinate. |
| `y` | `number` | Y-coordinate. |
| `r` | `number` | Circle radius. |
| `color` | `number` | Outline color (use `display.color(r, g, b)` to generate one). |

### Returns

`void`

---

## display.drawFillCircle()

```ts
display.drawFillCircle(x: number, y: number, r: number, color: number): void;
```

Draws a filled circle.

### Parameters

| Parameter | Type | Description |
| --------- | -------- | ----------------------------------------------------- |
| `x` | `number` | X-coordinate. |
| `y` | `number` | Y-coordinate. |
| `r` | `number` | Circle radius. |
| `color` | `number` | Color (use `display.color(r, g, b)` to generate one). |

### Returns

`void`

---

## display.drawXBitmap()

```ts
display.drawXBitmap(
  x: number,
  y: number,
  bitmap: ArrayBuffer,
  width: number,
  height: number,
  fgColor: number,
  bgColor?: number,
): void;
```

Draws a monochrome bitmap (XBM Bitmap) at the specified position on the screen.
You can convert images to this format using this online converter:
https://www.online-utility.org/image/convert/to/XBM

### Parameters

| Parameter | Type | Description |
| ---------- | ------------- | ---------------------------------------------------------------------------------------------------------------------- |
| `x` | `number` | X-coordinate for the bitmap. |
| `y` | `number` | Y-coordinate for the bitmap. |
| `bitmap` | `ArrayBuffer` | The bitmap data stored in an ArrayBuffer (1-bit per pixel). |
| `width` | `number` | The width of the bitmap in pixels. |
| `height` | `number` | The height of the bitmap in pixels. |
| `fgColor` | `number` | The foreground color (used for `1` bits in the bitmap). |
| `bgColor`? | `number` | (Optional) The background color (used for `0` bits in the bitmap). If not provided then the background is transparent. |

### Returns

`void`

---

## display.drawBitmap()

```ts
display.drawBitmap(
  x: number,
  y: number,
  bitmap: ArrayBuffer,
  width: number,
  height: number,
  bpp: 1 | 4 | 8 | 16,
  palette?: ArrayBuffer,
): void;
```

Draws a bitmap at the specified position on the screen.

### Parameters

| Parameter | Type | Description |
| ---------- | ------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `x` | `number` | X-coordinate for the bitmap. |
| `y` | `number` | Y-coordinate for the bitmap. |
| `bitmap` | `ArrayBuffer` | The bitmap data stored in an `ArrayBuffer`. The format depends on `bpp`: - `16 bpp`: Each pixel is a 16-bit color value (RGB565). - `8 bpp`: Each pixel is an 8-bit color value (RGB332). - `4 bpp`: Each pixel is a 4-bit index into `palette` (2 pixels per byte). - `1 bpp`: Each pixel is a 1-bit index into `palette` (8 pixels per byte). |
| `width` | `number` | The width of the bitmap in pixels. |
| `height` | `number` | The height of the bitmap in pixels. |
| `bpp` | `1` \| `4` \| `8` \| `16` | Bits per pixel (16, 8, 4, or 1). |
| `palette`? | `ArrayBuffer` | A color palette used **only** when `bpp` is 4 or 1. Each entry is a 16-bit color (RGB565). |

### Returns

`void`

---

## display.drawJpg()

```ts
display.drawJpg(
  path: string | Path,
  x?: number,
  y?: number,
  center?: boolean,
): void;
```

Draws a JPG image on the display.

### Parameters

| Parameter | Type | Description |
| --------- | ------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `path` | `string` \| [`Path`](#path) | The path to the JPG file. Supports: - A string path (e.g., "/images/photo.jpg"). - A `Path` object specifying storage \{ fs: "sd", path: "/images/photo.jpg" \}. |
| `x`? | `number` | X-coordinate. |
| `y`? | `number` | Y-coordinate. |
| `center`? | `boolean` | - |

### Returns

`void`

---

## display.drawGif()

```ts
display.drawGif(
  path: string | Path,
  x?: number,
  y?: number,
  center?: boolean,
  playDurationMs?: number,
): void;
```

Draws a GIF image on the display.

### Parameters

| Parameter | Type | Description |
| ----------------- | ------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `path` | `string` \| [`Path`](#path) | The path to the GIF file. Supports: - A string path (e.g., "/images/anim.gif"). - A `Path` object specifying storage \{ fs: "sd", path: "/images/anim.gif" \}. |
| `x`? | `number` | X-coordinate. |
| `y`? | `number` | Y-coordinate. |
| `center`? | `boolean` | Whether to center the image. |
| `playDurationMs`? | `number` | Duration to play the GIF. |

### Returns

`void`

---

## display.gifOpen()

```ts
display.gifOpen(path: string | Path): Gif;
```

Opens a GIF for manual frame playback.

### Parameters

| Parameter | Type | Description |
| --------- | ------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `path` | `string` \| [`Path`](#path) | The path to the GIF file. Supports: - A string path (e.g., "/images/anim.gif"). - A `Path` object specifying storage \{ fs: "sd", path: "/images/anim.gif" \}. |

### Returns

`Gif`

A `Gif` object for controlling playback.

---

## display.width()

```ts
display.width(): number;
```

Gets the display width.

### Returns

`number`

Display width in pixels.

---

## display.height()

```ts
display.height(): number;
```

Gets the display height.

### Returns

`number`

Display height in pixels.


<a name="gpiomd"></a>

# gpio

GPIO (General Purpose Input/Output) ports

### Example

```js
const gpio = require("gpio");

const led = 26;
gpio.init(led, OUTPUT);

gpio.write(led, HIGH);
delay(1000);
gpio.write(led, LOW);
delay(1000);
```

<!-- index-start -->
## gpio functions

* [gpio.pinMode()](#gpiopinmode)
* [gpio.digitalWrite()](#gpiodigitalwrite)
* [gpio.digitalRead()](#gpiodigitalread)
* [gpio.analogWrite()](#gpioanalogwrite)
* [gpio.analogRead()](#gpioanalogread)
* [gpio.touchRead()](#gpiotouchread)
* [gpio.dacWrite()](#gpiodacwrite)
* [gpio.ledcSetup()](#gpioledcsetup)
* [gpio.ledcAttachPin()](#gpioledcattachpin)
* [gpio.ledcWrite()](#gpioledcwrite)
<!-- index-end -->

## gpio.pinMode()

```ts
gpio.pinMode(pin: string | number, mode: Mode): void;
```

Configures the specified pin mode.

### Parameters

| Parameter | Type | Description |
| --------- | -------------------- | ---------------------------------------------- |
| `pin` | `string` \| `number` | GPIO pin number or name (e.g., `26` or "G26"). |
| `mode` | `Mode` | Pin mode (`INPUT`, `OUTPUT`, `PULLUP`, etc.). |

### Returns

`void`

---

## gpio.digitalWrite()

```ts
gpio.digitalWrite(pin: string | number, value: boolean | PinValue): void;
```

Writes a digital value to a pin.

### Parameters

| Parameter | Type | Description |
| --------- | ----------------------- | ------------------------------- |
| `pin` | `string` \| `number` | GPIO pin number or name. |
| `value` | `boolean` \| `PinValue` | `HIGH`/`LOW` or `true`/`false`. |

### Returns

`void`

---

## gpio.digitalRead()

```ts
gpio.digitalRead(pin: string | number): number;
```

Reads the digital state of a pin.

### Parameters

| Parameter | Type | Description |
| --------- | -------------------- | ------------------------ |
| `pin` | `string` \| `number` | GPIO pin number or name. |

### Returns

`number`

`1` for HIGH, `0` for LOW.

---

## gpio.analogWrite()

```ts
gpio.analogWrite(pin: string | number, value: number): void;
```

Writes an analog value (PWM) to a pin.

### Parameters

| Parameter | Type | Description |
| --------- | -------------------- | ------------------------ |
| `pin` | `string` \| `number` | GPIO pin number or name. |
| `value` | `number` | PWM duty cycle (0-255). |

### Returns

`void`

---

## gpio.analogRead()

```ts
gpio.analogRead(pin: string | number): number;
```

Reads the analog value from a pin.

### Parameters

| Parameter | Type | Description |
| --------- | -------------------- | ------------------------ |
| `pin` | `string` \| `number` | GPIO pin number or name. |

### Returns

`number`

Analog value (0-4095 for ADC1, varies for ADC2).

---

## gpio.touchRead()

```ts
gpio.touchRead(pin: string | number): number;
```

Reads the capacitive touch sensor value from a pin.

### Parameters

| Parameter | Type | Description |
| --------- | -------------------- | ------------------------ |
| `pin` | `string` \| `number` | GPIO pin number or name. |

### Returns

`number`

Touch sensor value.

---

## gpio.dacWrite()

```ts
gpio.dacWrite(pin: string | number, value: number): void;
```

Writes a DAC (Digital-to-Analog) value to a pin.

### Parameters

| Parameter | Type | Description |
| --------- | -------------------- | ------------------------ |
| `pin` | `string` \| `number` | GPIO pin number or name. |
| `value` | `number` | DAC value (0-255). |

### Returns

`void`

---

## gpio.ledcSetup()

```ts
gpio.ledcSetup(
  channel: number,
  freq: number,
  resolution_bits: number,
): number;
```

Configures an LEDC (PWM) channel with a specific frequency and resolution.

### Parameters

| Parameter | Type | Description |
| ----------------- | -------- | -------------------------------------------------- |
| `channel` | `number` | LEDC channel number (0–15). |
| `freq` | `number` | PWM frequency in Hz. |
| `resolution_bits` | `number` | Resolution (1–16 bits, defining duty cycle range). |

### Returns

`number`

The actual frequency set.

---

## gpio.ledcAttachPin()

```ts
gpio.ledcAttachPin(pin: number, channel: number): number;
```

Attaches a GPIO pin to an LEDC (PWM) channel.

### Parameters

| Parameter | Type | Description |
| --------- | -------- | --------------------------- |
| `pin` | `number` | GPIO pin number. |
| `channel` | `number` | LEDC channel number (0–15). |

### Returns

`number`

`0` on success, non-zero on failure.

---

## gpio.ledcWrite()

```ts
gpio.ledcWrite(channel: number, duty: number): number;
```

Sets the PWM duty cycle for a specific LEDC (PWM) channel.

### Parameters

| Parameter | Type | Description |
| --------- | -------- | ------------------------------------------ |
| `channel` | `number` | LEDC channel number (0–15). |
| `duty` | `number` | Duty cycle (0 to `2^resolution_bits - 1`). |

### Returns

`number`

`0` on success, non-zero on failure.


<a name="irmd"></a>

# ir

Interacting with infrared (IR) signals.

### Example

```js
const ir = require("ir");

const signal = ir.read(5); // Waits up to 5 seconds for an IR signal
console.log("Received IR signal:", signal);

const rawSignal = ir.readRaw(5);
console.log("Received raw IR signal:", rawSignal);

const success = ir.transmitFile("/signals/power_on.ir");
console.log("Transmission successful:", success);
```

<!-- index-start -->
## ir functions

* [ir.read()](#irread)
* [ir.readRaw()](#irreadraw)
* [ir.transmitFile()](#irtransmitfile)
<!-- index-end -->

## ir.read()

```ts
ir.read(timeoutSeconds: number): string;
```

Reads an infrared signal within a given timeout.

### Parameters

| Parameter | Type | Description |
| ---------------- | -------- | ---------------------------- |
| `timeoutSeconds` | `number` | Timeout duration in seconds. |

### Returns

`string`

The received IR signal as a string.

---

## ir.readRaw()

```ts
ir.readRaw(timeoutSeconds: number): string;
```

Reads an infrared signal in raw format within a given timeout.

### Parameters

| Parameter | Type | Description |
| ---------------- | -------- | ---------------------------- |
| `timeoutSeconds` | `number` | Timeout duration in seconds. |

### Returns

`string`

The raw received IR signal as a string.

---

## ir.transmitFile()

```ts
ir.transmitFile(path: string): boolean;
```

Transmits an infrared signal stored in a file.

### Parameters

| Parameter | Type | Description |
| --------- | -------- | --------------------------- |
| `path` | `string` | Path to the IR signal file. |

### Returns

`boolean`

`true` if the transmission was successful, otherwise `false`.


<a name="keyboardmd"></a>

# keyboard

Access the keyboard input.

### Example

```js
const keyboard = require("keyboard");

while (true) {
  if (keyboard.getAnyPress()) {
    break; // Exits the loop when a button is pressed.
  }
}
```

<!-- index-start -->
## keyboard functions

* [keyboard.getKeysPressed()](#keyboardgetkeyspressed)
* [keyboard.getPrevPress()](#keyboardgetprevpress)
* [keyboard.getSelPress()](#keyboardgetselpress)
* [keyboard.getNextPress()](#keyboardgetnextpress)
* [keyboard.getEscPress()](#keyboardgetescpress)
* [keyboard.getAnyPress()](#keyboardgetanypress)
* [keyboard.keyboard()](#keyboardkeyboard)
<!-- index-end -->

## keyboard.getKeysPressed()

```ts
keyboard.getKeysPressed(): string[];
```

Gets a list of currently pressed keys.

### Returns

`string`[]

An array of key names.

---

## keyboard.getPrevPress()

```ts
keyboard.getPrevPress(hold?: boolean): boolean;
```

Checks if the "previous" button was pressed.

### Parameters

| Parameter | Type | Description |
| --------- | --------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `hold`? | `boolean` | If `true`, returns `true` as long as any button is held down. If `false` or omitted, returns `true` only on press and repeats every X ms. Default: `false`. |

### Returns

`boolean`

`true` if the button was pressed, otherwise `false`.

---

## keyboard.getSelPress()

```ts
keyboard.getSelPress(hold?: boolean): boolean;
```

Checks if the "select" button was pressed.

### Parameters

| Parameter | Type | Description |
| --------- | --------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `hold`? | `boolean` | If `true`, returns `true` as long as any button is held down. If `false` or omitted, returns `true` only on press and repeats every X ms. Default: `false`. |

### Returns

`boolean`

`true` if the button was pressed, otherwise `false`.

---

## keyboard.getNextPress()

```ts
keyboard.getNextPress(hold?: boolean): boolean;
```

Checks if the "next" button was pressed.

### Parameters

| Parameter | Type | Description |
| --------- | --------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `hold`? | `boolean` | If `true`, returns `true` as long as any button is held down. If `false` or omitted, returns `true` only on press and repeats every X ms. Default: `false`. |

### Returns

`boolean`

`true` if the button was pressed, otherwise `false`.

---

## keyboard.getEscPress()

```ts
keyboard.getEscPress(hold?: boolean): boolean;
```

Checks if the "esc" button was pressed.

### Parameters

| Parameter | Type | Description |
| --------- | --------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `hold`? | `boolean` | If `true`, returns `true` as long as any button is held down. If `false` or omitted, returns `true` only on press and repeats every X ms. Default: `false`. |

### Returns

`boolean`

`true` if the button was pressed, otherwise `false`.

---

## keyboard.getAnyPress()

```ts
keyboard.getAnyPress(hold?: boolean): boolean;
```

Checks if any button was pressed.

### Parameters

| Parameter | Type | Description |
| --------- | --------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `hold`? | `boolean` | If `true`, returns `true` as long as any button is held down. If `false` or omitted, returns `true` only on press and repeats every X ms. Default: `false`. |

### Returns

`boolean`

`true` if any button was pressed, otherwise `false`.

---

## keyboard.keyboard()

```ts
keyboard.keyboard(title: string, valueLength: number, value: string): string;
```

Opens an on-screen keyboard for user input.

### Parameters

| Parameter | Type | Description |
| ------------- | -------- | ---------------------------------- |
| `title` | `string` | Title of the keyboard prompt. |
| `valueLength` | `number` | Maximum length of the input value. |
| `value` | `string` | Initial value to display. |

### Returns

`string`

User input.


<a name="notificationmd"></a>

# notification

Controls the notification LED.

### Example

```js
const notification = require("notification");

notification.blink(200); // Blinks the LED for 200ms
delay(1000);
notification.blink("short"); // Blinks using a predefined short duration
```

<!-- index-start -->
## notification functions

* [notification.blink()](#notificationblink)
<!-- index-end -->

## notification.blink()

```ts
notification.blink(durationMs: number | "short" | "long"): void;
```

Blinks the notification LED for a specified duration.

This function turns the LED on, waits for the given duration, and then turns it off.
Equivalent to:

```js
digitalWrite(19, HIGH);
delay(ms);
digitalWrite(19, LOW);
```

### Parameters

| Parameter | Type | Description |
| ------------ | --------------------------------- | ----------------------------------------------------------- |
| `durationMs` | `number` \| `"short"` \| `"long"` | Duration in milliseconds for how long the LED should blink. |

### Returns

`void`


<a name="serialmd"></a>

# serial

Serial communication.

### Example

```js
const serial = require("serial");

serial.println("Hello, Serial Port!");
const input = serial.readln();
console.log("Received:", input);
```

<!-- index-start -->
## serial functions

* [serial.print()](#serialprint)
* [serial.println()](#serialprintln)
* [serial.readln()](#serialreadln)
* [serial.cmd()](#serialcmd)
<!-- index-end -->

## serial.print()

```ts
serial.print(...args: any[]): void;
```

Sends a message over the serial connection without a newline.

### Parameters

| Parameter | Type | Description |
| --------- | ------- | -------------------- |
| ...`args` | `any`[] | The values to print. |

### Returns

`void`

---

## serial.println()

```ts
serial.println(...args: any[]): void;
```

Sends a message over the serial connection with a newline at the end.

### Parameters

| Parameter | Type | Description |
| --------- | ------- | -------------------- |
| ...`args` | `any`[] | The values to print. |

### Returns

`void`

---

## serial.readln()

```ts
serial.readln(timeoutInMiliseconds?: number): string;
```

Reads a line of input from the serial connection.

### Parameters

| Parameter | Type | Description |
| ----------------------- | -------- | ------------------------------------------------------------------ |
| `timeoutInMiliseconds`? | `number` | The time (in miliseconds) to wait for a connection before failing. |

### Returns

`string`

The received string.

---

## serial.cmd()

```ts
serial.cmd(command: string): boolean;
```

Executes a serial command list: controlling-device/serial.md.

### Parameters

| Parameter | Type |
| --------- | -------- |
| `command` | `string` |

### Returns

`boolean`

`true` if the command was successful, otherwise `false`.


<a name="storagemd"></a>

# storage

File storage operations.

This module provides functions for reading, writing, listing, renaming, and deleting files on different storage types.

### Example

```js
const storage = require("storage");

// Write data to a file
const success = storage.write("/data/log.txt", "Hello, world!");
if (success) {
  console.log("File written successfully!");
}

// Read data from a file
const content = storage.read("/data/log.txt");
console.log("File content:", content);

// Rename a file
const renamed = storage.rename("/data/log.txt", "/data/log_old.txt");
console.log("Rename successful:", renamed);

// Delete a file
const deleted = storage.delete("/data/log_old.txt");
console.log("Delete successful:", deleted);

// List directory contents
const files = storage.readdir("/data", { withFileTypes: true });
console.log("Files:\n", JSON.stringify(files, null, 2));

// Create a new directory
storage.mkdir("/data/newdir");

// Remove a directory
storage.rmdir("/data/newdir");
```

<!-- index-start -->
## storage functions

* [storage.Dirent](#storagedirent)
* [storage.read()](#storageread)
* [storage.write()](#storagewrite)
* [storage.readdir()](#storagereaddir)
* [storage.rename()](#storagerename)
* [storage.remove()](#storageremove)
* [storage.mkdir()](#storagemkdir)
* [storage.rmdir()](#storagermdir)
<!-- index-end -->

## storage.Dirent

Directory entry representing a file or directory.

### Properties

| Property | Type |
| -------------------------------------- | --------- |
| <a id="name"></a> `name` | `string` |
| <a id="size"></a> `size` | `number` |
| <a id="isdirectory"></a> `isDirectory` | `boolean` |

---

## storage.read()

### Call Signature

```ts
storage.read(path: string | Path, binary?: false): string;
```

Reads the content of a file.

#### Parameters

| Parameter | Type | Description |
| --------- | ------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------- |
| `path` | `string` \| [`Path`](#path) | The path to the file. Supports: - A string path (e.g., `"/file.txt"`). - A `Path` object specifying storage `{ fs: "sd", path: "/file.txt" }`. |
| `binary`? | `false` | If `true`, returns the file content as a `Uint8Array` (default: `false`). |

#### Returns

`string`

The file content as:

* A `string` if `binary` is `false` or omitted.
* A `Uint8Array` if `binary` is `true`.

### Call Signature

```ts
storage.read(path: string | Path, binary: true): Uint8Array;
```

Reads the content of a file.

#### Parameters

| Parameter | Type | Description |
| --------- | ------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------- |
| `path` | `string` \| [`Path`](#path) | The path to the file. Supports: - A string path (e.g., `"/file.txt"`). - A `Path` object specifying storage `{ fs: "sd", path: "/file.txt" }`. |
| `binary` | `true` | If `true`, returns the file content as a `Uint8Array` (default: `false`). |

#### Returns

`Uint8Array`

The file content as:

* A `string` if `binary` is `false` or omitted.
* A `Uint8Array` if `binary` is `true`.

---

## storage.write()

```ts
storage.write(
  path: string | Path,
  data: string | Uint8Array,
  mode?: "write" | "append",
  position?: string | number,
): boolean;
```

Writes data to a file, optionally inserting at a specific position.

### Parameters

| Parameter | Type | Description |
| ----------- | ------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `path` | `string` \| [`Path`](#path) | The file path. Supports: - A string like `"/file.txt"`. - A `Path` object like `{ fs: "sd", path: "/file.txt" }`. |
| `data` | `string` \| `Uint8Array` | The content to write. Can be a `string` or `Uint8Array`. |
| `mode`? | `"write"` \| `"append"` | How to write: - `"write"` (default): Replace the file content. - `"append"`: Add to the end of the file. |
| `position`? | `string` \| `number` | Where to insert the data: - A `number`: Insert at this byte position. - A `string`: Insert **before** the first time this text appears. - `"end"`: Add to the end (default for `"append"` mode). |

### Returns

`boolean`

`true` if writing was successful, otherwise `false`.

---

## storage.readdir()

### Call Signature

```ts
storage.readdir(
  path: string | Path,
  options?: {
    withFileTypes: false;
  },
): string[];
```

Lists the contents of a directory.

#### Parameters

| Parameter | Type | Description |
| ------------------------ | ------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------- |
| `path` | `string` \| [`Path`](#path) | The directory path. Supports: - A string path (e.g., `"/file.txt"`). - A `Path` object specifying storage `{ fs: "sd", path: "/file.txt" }`. |
| `options`? | \{ `withFileTypes`: `false`; \} | - |
| `options.withFileTypes`? | `false` | - |

#### Returns

`string`[]

An array of files and directories names in the directory.

### Call Signature

```ts
storage.readdir(
  path: string | Path,
  options: {
    withFileTypes: true;
  },
): Dirent[];
```

Lists the contents of a directory.

#### Parameters

| Parameter | Type | Description |
| ----------------------- | ------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------- |
| `path` | `string` \| [`Path`](#path) | The directory path. Supports: - A string path (e.g., `"/file.txt"`). - A `Path` object specifying storage `{ fs: "sd", path: "/file.txt" }`. |
| `options` | \{ `withFileTypes`: `true`; \} | If `{ withFileTypes: true }`, returns an array of `Dirent` objects with file type info. |
| `options.withFileTypes` | `true` | - |

#### Returns

[`Dirent`](#storagedirent)[]

An array of filenames or `Dirent` objects if `withFileTypes` is `true`.

---

## storage.rename()

```ts
storage.rename(oldPath: string | Path, newPath: string): boolean;
```

Renames a file or directory.

### Parameters

| Parameter | Type | Description |
| --------- | ------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `oldPath` | `string` \| [`Path`](#path) | The current path of the file or directory. Supports: - A string path (e.g., `"/file.txt"`). - A `Path` object specifying storage `{ fs: "sd", path: "/file.txt" }`. |
| `newPath` | `string` | The new path. |

### Returns

`boolean`

`true` if the rename operation was successful, otherwise `false`.

---

## storage.remove()

```ts
storage.remove(path: string | Path): boolean;
```

Deletes a file or directory.

### Parameters

| Parameter | Type | Description |
| --------- | ------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `path` | `string` \| [`Path`](#path) | The path of the file or directory to delete. Supports: - A string path (e.g., `"/file.txt"`). - A `Path` object specifying storage `{ fs: "sd", path: "/file.txt" }`. |

### Returns

`boolean`

`true` if the delete operation was successful, otherwise `false`.

---

## storage.mkdir()

```ts
storage.mkdir(path: string | Path): boolean;
```

Creates a directory, including parent directories if necessary.

### Parameters

| Parameter | Type | Description |
| --------- | ------------------------------------- | -------------------------- |
| `path` | `string` \| [`Path`](#path) | The path to the directory. |

### Returns

`boolean`

`true` if the directory was successfully created, otherwise `false`.

---

## storage.rmdir()

```ts
storage.rmdir(path: string | Path): boolean;
```

Removes an empty directory.

### Parameters

| Parameter | Type | Description |
| --------- | ------------------------------------- | -------------------------- |
| `path` | `string` \| [`Path`](#path) | The path to the directory. |

### Returns

`boolean`

`true` if the directory was successfully removed, otherwise `false`.


<a name="subghzmd"></a>

# subghz

Sub-GHz communication.

This module allows interaction with Sub-GHz frequencies for reading and transmitting data.

### Example

```js
const subghz = require("subghz");

// Set frequency to 433 MHz
subghz.setFrequency(433);

// Read incoming signal for 5 seconds
const receivedData = subghz.read(5);
console.log("Received Data:", receivedData);

// Transmit a stored signal file
const success = subghz.transmitFile("/plug1_on.sub");
if (success) {
  console.log("Transmission successful!");
}
```

<!-- index-start -->
## subghz functions

* [subghz.setFrequency()](#subghzsetfrequency)
* [subghz.read()](#subghzread)
* [subghz.readRaw()](#subghzreadraw)
* [subghz.transmitFile()](#subghztransmitfile)
<!-- index-end -->

## subghz.setFrequency()

```ts
subghz.setFrequency(freq: number): void;
```

Sets the frequency for Sub-GHz communication.

### Parameters

| Parameter | Type | Description |
| --------- | -------- | ----------------------------------------- |
| `freq` | `number` | Frequency in MHz (e.g., 433 for 433 MHz). |

### Returns

`void`

---

## subghz.read()

```ts
subghz.read(timeoutSeconds: number): string;
```

Reads an incoming signal.

### Parameters

| Parameter | Type | Description |
| ---------------- | -------- | --------------------------------------------- |
| `timeoutSeconds` | `number` | Maximum time in seconds to wait for a signal. |

### Returns

`string`

The received signal as a string.

---

## subghz.readRaw()

```ts
subghz.readRaw(timeoutSeconds: number): string;
```

Reads raw incoming signal data.

### Parameters

| Parameter | Type | Description |
| ---------------- | -------- | --------------------------------------------- |
| `timeoutSeconds` | `number` | Maximum time in seconds to wait for a signal. |

### Returns

`string`

The raw received signal as a string.

---

## subghz.transmitFile()

```ts
subghz.transmitFile(filename: string): boolean;
```

Transmits a saved signal from a file.

### Parameters

| Parameter | Type | Description |
| ---------- | -------- | ------------------------------------------------------- |
| `filename` | `string` | The path to the file containing the signal to transmit. |

### Returns

`boolean`

`true` if the transmission was successful, otherwise `false`.


<a name="wifimd"></a>

# wifi

Wi-Fi connection and HTTP requests.

### Example

```js
const wifi = require("wifi");

if (!wifi.connected()) {
  wifi.connect("MySSID", 10, "password123");
}

const networks = wifi.scan();
console.log("Available Networks:", networks);

const response = wifi.httpFetch("https://example.com/api", {
  method: "GET",
  headers: { "Content-Type": "application/json" },
});
console.log(response.body);
```

<!-- index-start -->
## WiFi functions

* [wifi.connected()](#wificonnected)
* [wifi.connect()](#wificonnect)
* [wifi.connectDialog()](#wificonnectdialog)
* [wifi.disconnect()](#wifidisconnect)
* [wifi.scan()](#wifiscan)
* [wifi.httpFetch()](#wifihttpfetch)
<!-- index-end -->

## wifi.connected()

```ts
wifi.connected(): boolean;
```

Checks if the device is connected to a Wi-Fi network.

### Returns

`boolean`

`true` if connected, otherwise `false`.

---

## wifi.connect()

```ts
wifi.connect(
  ssid: string,
  timeoutInSeconds: number,
  password: string,
): boolean;
```

Connects to a Wi-Fi network.

### Parameters

| Parameter | Type | Description |
| ------------------ | -------- | -------------------------------------------------------------- |
| `ssid` | `string` | The name of the Wi-Fi network (SSID). |
| `timeoutInSeconds` | `number` | The time (in seconds) to wait for a connection before failing. |
| `password` | `string` | The Wi-Fi password. |

### Returns

`boolean`

`true` if successfully connected, otherwise `false`.

---

## wifi.connectDialog()

```ts
wifi.connectDialog(): void;
```

Opens a dialog to select and connect to a Wi-Fi network.

### Returns

`void`

---

## wifi.disconnect()

```ts
wifi.disconnect(): void;
```

Disconnects from the current Wi-Fi network.

### Returns

`void`

---

## wifi.scan()

```ts
wifi.scan(): {
  encryptionType:
 | "OPEN"
 | "WEP"
 | "WPA_PSK"
 | "WPA2_PSK"
 | "WPA_WPA2_PSK"
 | "ENTERPRISE"
 | "WPA2_ENTERPRISE"
 | "WPA3_PSK"
 | "WPA2_WPA3_PSK"
 | "WAPI_PSK"
 | "WPA3_ENT_192"
 | "MAX";
  SSID: string;
  MAC: string;
}[];
```

Scans for available Wi-Fi networks.

### Returns

\{
`encryptionType`: \| `"OPEN"`
\| `"WEP"`
\| `"WPA_PSK"`
\| `"WPA2_PSK"`
\| `"WPA_WPA2_PSK"`
\| `"ENTERPRISE"`
\| `"WPA2_ENTERPRISE"`
\| `"WPA3_PSK"`
\| `"WPA2_WPA3_PSK"`
\| `"WAPI_PSK"`
\| `"WPA3_ENT_192"`
\| `"MAX"`;
`SSID`: `string`;
`MAC`: `string`;
\}[]

An array of available networks, each containing:

* `encryptionType`: The type of encryption used (e.g., WPA2, WEP).
* `SSID`: The name of the network.
* `MAC`: The MAC address of the access point.

---

## wifi.httpFetch()

### Call Signature

```ts
wifi.httpFetch(
  url: string,
  options?: {
    method:
 | "GET"
 | "POST"
 | "DELETE"
 | "PATCH"
 | "PUT"
 | "HEAD"
 | "OPTIONS"
 | "TRACE"
 | "CONNECT";
    body: string;
    binaryResponse: false;
    headers: string[] | [string, string][] | Record<string, string>;
  },
): {
  status: number;
  ok: boolean;
  body: string;
};
```

Performs an HTTP request.

#### Parameters

| Parameter | Type | Description |
| ------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------- |
| `url` | `string` | The URL to fetch. |
| `options`? | \{ `method`: \| `"GET"` \| `"POST"` \| `"DELETE"` \| `"PATCH"` \| `"PUT"` \| `"HEAD"` \| `"OPTIONS"` \| `"TRACE"` \| `"CONNECT"`; `body`: `string`; `binaryResponse`: `false`; `headers`: `string`[] \| \[`string`, `string`\][] \| `Record`&lt;`string`, `string`&gt;; \} | Request options including method, body, and headers. |
| `options.method`? | \| `"GET"` \| `"POST"` \| `"DELETE"` \| `"PATCH"` \| `"PUT"` \| `"HEAD"` \| `"OPTIONS"` \| `"TRACE"` \| `"CONNECT"` | - |
| `options.body`? | `string` | - |
| `options.binaryResponse`? | `false` | - |
| `options.headers`? | `string`[] \| \[`string`, `string`\][] \| `Record`&lt;`string`, `string`&gt; | - |

#### Returns

```ts
{
  status: number;
  ok: boolean;
  body: string;
}
```

An object containing:

* `status`: The HTTP response status code (e.g., `200`, `404`).
* `ok`: `true` if the response status is 200-299, otherwise `false`.
* `body`: The response body as a string.

| Name | Type |
| -------- | --------- |
| `status` | `number` |
| `ok` | `boolean` |
| `body` | `string` |

### Call Signature

```ts
wifi.httpFetch(
  url: string,
  options?: {
    method:
 | "GET"
 | "POST"
 | "DELETE"
 | "PATCH"
 | "PUT"
 | "HEAD"
 | "OPTIONS"
 | "TRACE"
 | "CONNECT";
    body: string;
    responseType: "string";
    headers: string[] | [string, string][] | Record<string, string>;
  },
): {
  status: number;
  ok: boolean;
  body: string;
};
```

Performs an HTTP request.

#### Parameters

| Parameter | Type | Description |
| ----------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------- |
| `url` | `string` | The URL to fetch. |
| `options`? | \{ `method`: \| `"GET"` \| `"POST"` \| `"DELETE"` \| `"PATCH"` \| `"PUT"` \| `"HEAD"` \| `"OPTIONS"` \| `"TRACE"` \| `"CONNECT"`; `body`: `string`; `responseType`: `"string"`; `headers`: `string`[] \| \[`string`, `string`\][] \| `Record`&lt;`string`, `string`&gt;; \} | Request options including method, body, and headers. |
| `options.method`? | \| `"GET"` \| `"POST"` \| `"DELETE"` \| `"PATCH"` \| `"PUT"` \| `"HEAD"` \| `"OPTIONS"` \| `"TRACE"` \| `"CONNECT"` | - |
| `options.body`? | `string` | - |
| `options.responseType`? | `"string"` | - |
| `options.headers`? | `string`[] \| \[`string`, `string`\][] \| `Record`&lt;`string`, `string`&gt; | - |

#### Returns

```ts
{
  status: number;
  ok: boolean;
  body: string;
}
```

An object containing:

* `status`: The HTTP response status code (e.g., `200`, `404`).
* `ok`: `true` if the response status is 200-299, otherwise `false`.
* `body`: The response body as a string.

| Name | Type |
| -------- | --------- |
| `status` | `number` |
| `ok` | `boolean` |
| `body` | `string` |

### Call Signature

```ts
wifi.httpFetch(
  url: string,
  options?: {
    method:
 | "GET"
 | "POST"
 | "DELETE"
 | "PATCH"
 | "PUT"
 | "HEAD"
 | "OPTIONS"
 | "TRACE"
 | "CONNECT";
    body: string;
    responseType: "binary";
    headers: string[] | [string, string][] | Record<string, string>;
  },
): {
  status: number;
  ok: boolean;
  body: Uint8Array;
};
```

Performs an HTTP request.

#### Parameters

| Parameter | Type | Description |
| ----------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------- |
| `url` | `string` | The URL to fetch. |
| `options`? | \{ `method`: \| `"GET"` \| `"POST"` \| `"DELETE"` \| `"PATCH"` \| `"PUT"` \| `"HEAD"` \| `"OPTIONS"` \| `"TRACE"` \| `"CONNECT"`; `body`: `string`; `responseType`: `"binary"`; `headers`: `string`[] \| \[`string`, `string`\][] \| `Record`&lt;`string`, `string`&gt;; \} | Request options including method, body, and headers. |
| `options.method`? | \| `"GET"` \| `"POST"` \| `"DELETE"` \| `"PATCH"` \| `"PUT"` \| `"HEAD"` \| `"OPTIONS"` \| `"TRACE"` \| `"CONNECT"` | - |
| `options.body`? | `string` | - |
| `options.responseType`? | `"binary"` | - |
| `options.headers`? | `string`[] \| \[`string`, `string`\][] \| `Record`&lt;`string`, `string`&gt; | - |

#### Returns

```ts
{
  status: number;
  ok: boolean;
  body: Uint8Array;
}
```

An object containing:

* `status`: The HTTP response status code (e.g., `200`, `404`).
* `ok`: `true` if the response status is 200-299, otherwise `false`.
* `body`: The response body as a string.

| Name | Type |
| -------- | ------------ |
| `status` | `number` |
| `ok` | `boolean` |
| `body` | `Uint8Array` |

### Call Signature

```ts
wifi.httpFetch(
  url: string,
  options?: {
    method:
 | "GET"
 | "POST"
 | "DELETE"
 | "PATCH"
 | "PUT"
 | "HEAD"
 | "OPTIONS"
 | "TRACE"
 | "CONNECT";
    body: string;
    responseType: "string" | "binary";
    headers: string[] | [string, string][] | Record<string, string>;
  },
): {
  status: number;
  ok: boolean;
  body: string | Uint8Array;
};
```

Performs an HTTP request.

#### Parameters

| Parameter | Type | Description |
| ----------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------- |
| `url` | `string` | The URL to fetch. |
| `options`? | \{ `method`: \| `"GET"` \| `"POST"` \| `"DELETE"` \| `"PATCH"` \| `"PUT"` \| `"HEAD"` \| `"OPTIONS"` \| `"TRACE"` \| `"CONNECT"`; `body`: `string`; `responseType`: `"string"` \| `"binary"`; `headers`: `string`[] \| \[`string`, `string`\][] \| `Record`&lt;`string`, `string`&gt;; \} | Request options including method, body, and headers. |
| `options.method`? | \| `"GET"` \| `"POST"` \| `"DELETE"` \| `"PATCH"` \| `"PUT"` \| `"HEAD"` \| `"OPTIONS"` \| `"TRACE"` \| `"CONNECT"` | - |
| `options.body`? | `string` | - |
| `options.responseType`? | `"string"` \| `"binary"` | - |
| `options.headers`? | `string`[] \| \[`string`, `string`\][] \| `Record`&lt;`string`, `string`&gt; | - |

#### Returns

```ts
{
  status: number;
  ok: boolean;
  body: string | Uint8Array;
}
```

An object containing:

* `status`: The HTTP response status code (e.g., `200`, `404`).
* `ok`: `true` if the response status is 200-299, otherwise `false`.
* `body`: The response body as a string.

| Name | Type |
| -------- | ------------------------ |
| `status` | `number` |
| `ok` | `boolean` |
| `body` | `string` \| `Uint8Array` |
