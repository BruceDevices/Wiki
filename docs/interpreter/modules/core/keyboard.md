# Keyboard

The Keyboard module provides on-screen keyboard input functionality for user interaction.

## Keyboard Types

### `keyboard.keyboard(prompt, initialText, minLength, maxLength)`

Shows a full QWERTY keyboard for text input.

**Parameters:**

| Parameter     | Type     | Description                       |
| ------------- | -------- | --------------------------------- |
| `prompt`      | `string` | Prompt message displayed to user  |
| `initialText` | `string` | Pre-filled text in input field    |
| `minLength`   | `number` | Minimum required text length      |
| `maxLength`   | `number` | Maximum allowed text length       |

**Returns:** `string` - User input text, or empty string if cancelled

**Example:**

```javascript
var keyboard = require("keyboard");

const username = keyboard.keyboard("Enter username:", "", 1, 20);
if (username) {
    console.log("Username entered:", username);
}
```

### `keyboard.numKeyboard(prompt, initialText, minLength, maxLength)`

Shows a numeric keyboard for number input.

**Parameters:**

| Parameter     | Type     | Description      |
| ------------- | -------- | ---------------- |
| `prompt`      | `string` | Prompt message   |
| `initialText` | `string` | Pre-filled text  |
| `minLength`   | `number` | Minimum length   |
| `maxLength`   | `number` | Maximum length   |

**Returns:** `string` - Numeric input string

**Example:**

```javascript
var keyboard = require("keyboard");

const age = keyboard.numKeyboard("Enter age:", "", 1, 3);
const ageNumber = parseInt(age);
```

### `keyboard.hexKeyboard(prompt, initialText, minLength, maxLength)`

Shows a hexadecimal keyboard for hex value input.

**Parameters:**

| Parameter     | Type     | Description      |
| ------------- | -------- | ---------------- |
| `prompt`      | `string` | Prompt message   |
| `initialText` | `string` | Pre-filled text  |
| `minLength`   | `number` | Minimum length   |
| `maxLength`   | `number` | Maximum length   |

**Returns:** `string` - Hexadecimal input string

**Example:**

```javascript
var keyboard = require("keyboard");

const hexValue = keyboard.hexKeyboard("Enter hex value:", "0x", 3, 8);
console.log("Hex input:", hexValue);
```

## Button State Functions

### `keyboard.getKeysPressed()`

Returns currently pressed keys state.

**Parameters:** None

**Returns:** `object` - Key state information

**Example:**

```javascript
var keyboard = require("keyboard");

const keys = keyboard.getKeysPressed();
console.log("Keys pressed:", keys);
```

### `keyboard.getPrevPress(timeout)`

Checks if the previous/up button was pressed.

**Parameters:**

| Parameter | Type               | Description                  |
| --------- | ------------------ | ---------------------------- |
| `timeout` | `number, optional` | Timeout in milliseconds      |

**Returns:** `boolean` - True if button was pressed

### `keyboard.getSelPress(timeout)`

Checks if the select/enter button was pressed.

**Parameters:**

| Parameter | Type               | Description                  |
| --------- | ------------------ | ---------------------------- |
| `timeout` | `number, optional` | Timeout in milliseconds      |

**Returns:** `boolean` - True if button was pressed

### `keyboard.getEscPress(timeout)`

Checks if the escape/back button was pressed.

**Parameters:**

| Parameter | Type               | Description                  |
| --------- | ------------------ | ---------------------------- |
| `timeout` | `number, optional` | Timeout in milliseconds      |

**Returns:** `boolean` - True if button was pressed

### `keyboard.getNextPress(timeout)`

Checks if the next/down button was pressed.

**Parameters:**

| Parameter | Type               | Description                  |
| --------- | ------------------ | ---------------------------- |
| `timeout` | `number, optional` | Timeout in milliseconds      |

**Returns:** `boolean` - True if button was pressed

### `keyboard.getAnyPress(timeout)`

Checks if any button was pressed.

**Parameters:**

| Parameter | Type               | Description                  |
| --------- | ------------------ | ---------------------------- |
| `timeout` | `number, optional` | Timeout in milliseconds      |

**Returns:** `boolean` - True if any button was pressed

## Configuration

### `keyboard.setLongPress(enabled)`

Enables or disables long press functionality.

**Parameters:**

| Parameter | Type      | Description                |
| --------- | --------- | -------------------------- |
| `enabled` | `boolean` | Enable long press if true  |

**Returns:** `undefined`

**Example:**

```javascript
keyboard.setLongPress(true); // Enable long press detection
```

## Usage Examples

```javascript
// User registration form
function userRegistration() {
    display.fill(display.color(0, 0, 0));
    display.setTextColor(display.color(255, 255, 255));
    display.drawString("User Registration", 10, 10);

    // Get username
    const username = keyboard.keyboard("Username:", "", 3, 15);
    if (!username) {
        dialog.info("Registration cancelled");
        return null;
    }

    // Get password (using regular keyboard, could add masking)
    const password = keyboard.keyboard("Password:", "", 6, 20);
    if (!password) {
        dialog.info("Registration cancelled");
        return null;
    }

    // Get age
    const ageStr = keyboard.numKeyboard("Age:", "", 1, 3);
    const age = parseInt(ageStr);

    if (isNaN(age) || age < 1 || age > 120) {
        dialog.error("Invalid age entered");
        return null;
    }

    return {
        username: username,
        password: password,
        age: age
    };
}

// Configuration editor
function editConfiguration() {
    const config = {
        deviceName: "Bruce",
        timeout: 30,
        frequency: 433.92
    };

    // Edit device name
    config.deviceName = keyboard.keyboard(
        "Device Name:",
        config.deviceName,
        1, 20
    );

    // Edit timeout (numeric)
    const timeoutStr = keyboard.numKeyboard(
        "Timeout (seconds):",
        config.timeout.toString(),
        1, 4
    );
    config.timeout = parseInt(timeoutStr) || 30;

    // Edit frequency (allow decimal)
    const freqStr = keyboard.keyboard(
        "Frequency (MHz):",
        config.frequency.toString(),
        1, 10
    );
    config.frequency = parseFloat(freqStr) || 433.92;

    return config;
}

// WiFi password input
function getWiFiPassword(ssid) {
    display.fill(display.color(0, 0, 100));
    display.setTextColor(display.color(255, 255, 255));
    display.setTextSize(1);
    display.drawString(`Connect to: ${ssid}`, 10, 10);

    const password = keyboard.keyboard("WiFi Password:", "", 0, 63);
    return password;
}

// Hex editor for binary data
function hexEditor(initialData = "") {
    let data = initialData;

    while (true) {
        display.fill(display.color(0, 0, 0));
        display.setTextColor(display.color(255, 255, 255));
        display.setTextSize(1);

        // Display current data
        display.drawString("Hex Editor", 10, 10);
        display.drawString(`Data: ${data}`, 10, 30);
        display.drawString("Press SEL to edit, ESC to exit", 10, 50);

        // Wait for input
        if (keyboard.getSelPress(5000)) {
            // Edit mode
            const newData = keyboard.hexKeyboard("Edit hex data:", data, 0, 32);
            if (newData !== null) {
                data = newData;
            }
        } else if (keyboard.getEscPress(100)) {
            // Exit
            break;
        }

        delay(100);
    }

    return data;
}

// Simple menu navigation
function menuNavigation(menuItems) {
    let selectedIndex = 0;

    while (true) {
        // Display menu
        display.fill(display.color(0, 0, 0));
        display.setTextColor(display.color(255, 255, 255));
        display.setTextSize(1);

        display.drawString("Menu", 10, 10);

        menuItems.forEach((item, index) => {
            const y = 30 + (index * 15);
            const prefix = (index === selectedIndex) ? "> " : "  ";
            display.drawString(prefix + item.text, 10, y);
        });

        // Handle input
        if (keyboard.getPrevPress(100)) {
            selectedIndex = (selectedIndex - 1 + menuItems.length) % menuItems.length;
        } else if (keyboard.getNextPress(100)) {
            selectedIndex = (selectedIndex + 1) % menuItems.length;
        } else if (keyboard.getSelPress(100)) {
            // Execute selected item
            if (menuItems[selectedIndex].action) {
                menuItems[selectedIndex].action();
            }
            return selectedIndex;
        } else if (keyboard.getEscPress(100)) {
            return -1; // Cancelled
        }

        delay(50);
    }
}

// Usage example for menu
const mainMenu = [
    {
        text: "WiFi Settings",
        action: () => dialog.info("WiFi settings opened")
    },
    {
        text: "Display Settings",
        action: () => dialog.info("Display settings opened")
    },
    {
        text: "System Info",
        action: () => {
            const info = `Device: ${device.getName()}\nBattery: ${device.getBatteryCharge()}%`;
            dialog.viewText(info, "System Info");
        }
    },
    {
        text: "Exit",
        action: () => console.log("Exiting menu")
    }
];

// Input validation helpers
function validateInput(input, type) {
    switch (type) {
        case 'email':
            return /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(input);
        case 'ip':
            return /^(\d{1,3}\.){3}\d{1,3}$/.test(input);
        case 'mac':
            return /^([0-9A-Fa-f]{2}:){5}[0-9A-Fa-f]{2}$/.test(input);
        case 'hex':
            return /^[0-9A-Fa-f]*$/.test(input);
        case 'number':
            return !isNaN(parseFloat(input));
        default:
            return true;
    }
}

function getValidatedInput(prompt, type, minLen = 0, maxLen = 50) {
    let attempts = 0;
    const maxAttempts = 3;

    while (attempts < maxAttempts) {
        const input = keyboard.keyboard(prompt, "", minLen, maxLen);

        if (!input) {
            return null; // User cancelled
        }

        if (validateInput(input, type)) {
            return input;
        }

        attempts++;
        dialog.error(`Invalid ${type} format. ${maxAttempts - attempts} attempts remaining.`);
    }

    dialog.error("Maximum attempts exceeded");
    return null;
}
```
