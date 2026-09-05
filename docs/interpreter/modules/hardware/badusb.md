# BadUSB

The BadUSB module provides USB HID keyboard emulation functionality for security testing and automation.

## Functions

### `badusb.setup()`

Initializes BadUSB keyboard mode.

**Parameters:** None

**Returns:** `boolean` - True if USB HID is enabled, false otherwise

**Example:**

```javascript
var badusb = require("badusb");

if (badusb.setup()) {
    console.log("BadUSB mode enabled");
} else {
    console.log("BadUSB not available");
}
```

### `badusb.print(text)`

Types text without a newline (like typing on a keyboard).

**Parameters:**

| Parameter | Type     | Description   |
| --------- | -------- | ------------- |
| `text`    | `string` | Text to type  |

**Returns:** `undefined`

**Example:**

```javascript
var badusb = require("badusb");

badusb.print("Hello World");
```

### `badusb.println(text)`

Types text followed by a newline (like typing and pressing Enter).

**Parameters:**

| Parameter | Type     | Description   |
| --------- | -------- | ------------- |
| `text`    | `string` | Text to type  |

**Returns:** `undefined`

**Example:**

```javascript
var badusb = require("badusb");

badusb.println("Hello World");
```

### `badusb.press(keycode)`

Presses and releases a key by keycode.

**Parameters:**

| Parameter | Type     | Description      |
| --------- | -------- | ---------------- |
| `keycode` | `number` | USB HID keycode  |

**Returns:** `undefined`

**Example:**

```javascript
var badusb = require("badusb");

badusb.press(0x28); // Enter key
badusb.press(0x29); // Escape key
```

### `badusb.hold(keycode)`

Holds down a key without releasing it.

**Parameters:**

| Parameter | Type     | Description             |
| --------- | -------- | ----------------------- |
| `keycode` | `number` | USB HID keycode to hold |

**Returns:** `undefined`

**Example:**

```javascript
var badusb = require("badusb");

badusb.hold(0xE0); // Hold Left Control
badusb.press(0x06); // Press 'c' (Ctrl+C)
badusb.release(0xE0); // Release Left Control
```

### `badusb.release(keycode)`

Releases a previously held key.

**Parameters:**

| Parameter | Type     | Description                |
| --------- | -------- | -------------------------- |
| `keycode` | `number` | USB HID keycode to release |

**Returns:** `undefined`

### `badusb.releaseAll()`

Releases all currently held keys (safety function).

**Parameters:** None

**Returns:** `undefined`

**Example:**

```javascript
var badusb = require("badusb");

// In case of stuck keys
badusb.releaseAll();
```

### `badusb.pressRaw(keycode)`

Presses and releases a raw keycode (lower-level access).

**Parameters:**

| Parameter | Type     | Description |
| --------- | -------- | ----------- |
| `keycode` | `number` | Raw keycode |

**Returns:** `undefined`

### `badusb.runFile(filename)`

Executes a BadUSB script file.

**Parameters:**

| Parameter  | Type     | Description         |
| ---------- | -------- | ------------------- |
| `filename` | `string` | Path to script file |

**Returns:** `boolean` - Success status

**Example:**

```javascript
var badusb = require("badusb");

if (badusb.runFile("/badusb/script.txt")) {
    console.log("Script executed successfully");
}
```

## Common USB HID Keycodes

### Letters (A-Z)

- `0x04` to `0x1D`: A through Z

### Numbers

- `0x1E` to `0x27`: 1 through 9, 0

### Special Keys

- `0x28`: Enter
- `0x29`: Escape
- `0x2A`: Backspace
- `0x2B`: Tab
- `0x2C`: Space
- `0x39`: Caps Lock

### Function Keys

- `0x3A` to `0x45`: F1 through F12

### Arrow Keys

- `0x4F`: Right Arrow
- `0x50`: Left Arrow
- `0x51`: Down Arrow
- `0x52`: Up Arrow

### Modifier Keys

- `0xE0`: Left Control
- `0xE1`: Left Shift
- `0xE2`: Left Alt
- `0xE3`: Left GUI (Windows/Cmd key)
- `0xE4`: Right Control
- `0xE5`: Right Shift
- `0xE6`: Right Alt
- `0xE7`: Right GUI

## Usage Examples

```javascript
var badusb = require("badusb");

// Basic text typing
function typeText() {
    if (!badusb.setup()) {
        console.log("BadUSB not available");
        return;
    }

    delay(2000); // Give user time to position cursor
    badusb.println("Hello from Bruce!");
}

// Keyboard shortcuts
function openTaskManager() {
    badusb.hold(0xE0); // Hold Ctrl
    badusb.hold(0xE1); // Hold Shift
    badusb.press(0x29); // Press Escape
    badusb.releaseAll(); // Release all keys
}

// Windows Run dialog
function openRunDialog() {
    badusb.hold(0xE3); // Hold Windows key
    badusb.press(0x15); // Press 'r'
    badusb.release(0xE3); // Release Windows key
    delay(500);
    badusb.println("notepad");
}

// Advanced script execution
function runComplexScript() {
    // Open command prompt
    badusb.hold(0xE3); // Windows key
    badusb.press(0x15); // 'r'
    badusb.release(0xE3);
    delay(300);

    badusb.println("cmd");
    delay(1000);

    // Execute commands
    badusb.println("echo Hello World");
    badusb.println("dir");
    badusb.println("exit");
}

// USB Rubber Ducky style payload
function rubberDuckyPayload() {
    if (!badusb.setup()) return;

    delay(3000); // DELAY 3000

    // GUI r (Windows + R)
    badusb.hold(0xE3);
    badusb.press(0x15);
    badusb.release(0xE3);
    delay(500);

    // STRING powershell
    badusb.print("powershell");
    delay(100);

    // ENTER
    badusb.press(0x28);
    delay(2000);

    // STRING Get-ComputerInfo
    badusb.print("Get-ComputerInfo");
    badusb.press(0x28);
    delay(1000);

    // STRING exit
    badusb.print("exit");
    badusb.press(0x28);
}

// File execution from storage
function executeStoredScript() {
    const scriptPath = "/badusb/payload.txt";

    // Check if file exists
    try {
        const content = storage.read(scriptPath);
        console.log(`Script size: ${content.length} bytes`);
    } catch (error) {
        console.log("Script file not found");
        return;
    }

    // Execute the script
    if (badusb.runFile(scriptPath)) {
        console.log("Script executed successfully");
    } else {
        console.log("Script execution failed");
    }
}

// Secure typing with error handling
function secureType(text, delayBetweenKeys = 50) {
    if (!badusb.setup()) {
        throw new Error("BadUSB not available");
    }

    try {
        for (let char of text) {
            badusb.print(char);
            delay(delayBetweenKeys);
        }
    } catch (error) {
        badusb.releaseAll(); // Ensure no stuck keys
        throw error;
    }
}

// Multi-platform shortcuts
function openTerminal() {
    if (!badusb.setup()) return;

    // Try Windows (Ctrl+Shift+Esc for Task Manager, then File > Run New Task)
    badusb.hold(0xE0); // Ctrl
    badusb.hold(0xE1); // Shift
    badusb.press(0x29); // Esc
    badusb.releaseAll();
    delay(1000);

    // Alt+F to open File menu
    badusb.hold(0xE2); // Alt
    badusb.press(0x09); // F
    badusb.release(0xE2);
    delay(200);

    badusb.press(0x15); // R for "Run new task"
    delay(300);

    badusb.println("cmd");
}
```
