# Documentation

Welcome to the Bruce JavaScript API documentation. This section provides comprehensive documentation for all JavaScript modules available in the Bruce firmware interpreter.

## Overview

The Bruce firmware includes a powerful JavaScript interpreter built on [MicroQuickJS](https://github.com/bellard/mquickjs){target="_blank" rel="noopener"} that provides access to device hardware and functionality through a rich set of APIs. These APIs allow you to create custom scripts for device interaction, automation, and development.

It allows you to create apps or even games using JavaScript.

## Available Modules

The JavaScript API is organized into several modules, each focusing on specific device functionality:

### Core Modules

* **[Global](modules/core/globals.md)** - Global utility functions and timers
* **[Math](modules/core/math.md)** - Extended mathematical functions
* **[Runtime](modules/core/runtime.md)** - Script execution control
* **[Console](modules/core/console.md)** - Sending messages to the console/serial output
* **[Device](modules/core/device.md)** - Device information and system status
* **[Dialog](modules/core/dialog.md)** - User interface dialogs and prompts
* **[Display](modules/core/display.md)** - Graphics, text rendering, and display control
* **[Keyboard](modules/core/keyboard.md)** - On-screen keyboard input
* **[Notification](modules/core/notification.md)** - Visual notifications and indicators
* **[Storage](modules/core/storage.md)** - File system operations and data persistence

### Hardware Modules

* **[Audio](modules/hardware/audio.md)** - Audio playback and sound generation
* **[BadUSB](modules/hardware/badusb.md)** - USB HID keyboard emulation
* **[GPIO](modules/hardware/gpio.md)** - General purpose input/output pin control
* **[I2C](modules/hardware/i2c.md)** - I2C bus communication
* **[Infrared](modules/hardware/ir.md)** - IR signal transmission and reception
* **[Microphone](modules/hardware/mic.md)** - Audio recording functionality
* **[RFID](modules/hardware/rfid.md)** - RFID/NFC tag operations
* **[Serial](modules/hardware/serial.md)** - Serial communication and console output
* **[Sub-GHz](modules/hardware/subghz.md)** - Sub-GHz radio frequency operations
* **[WiFi](modules/hardware/wifi.md)** - WiFi connectivity and HTTP requests

## Getting Started

1. Scripts are executed through the Bruce firmware's script runner
2. Most modules are not loaded automatically and will need to be imported using `var moduleName = require("moduleName");`
3. Functions are called directly on module objects (e.g., `display.drawText(...)`)
4. Most functions return `undefined` unless explicitly documented otherwise

## Example Usage

```javascript
// Display hello world
var display = require("display");
display.setTextSize(2);
display.setTextColor(display.color(255, 255, 255));
display.drawString("Hello World!", 10, 10);

// Blink LED
var notification = require("notification");
notification.blink(3000);

// Get device info
var device = require("device");
const deviceName = device.getName();
const batteryLevel = device.getBatteryCharge();
console.log(`Device: ${deviceName}, Battery: ${batteryLevel}%`);
```

## Error Handling

Most functions will throw JavaScript exceptions on error. Use try-catch blocks for error handling:

```javascript
var storage = require("storage");
try {
    const data = storage.read("/config.json");
    const config = JSON.parse(data);
} catch (error) {
    console.log("Failed to read config:", error.message);
}
```

## Performance Considerations

* The JavaScript interpreter runs on an embedded system with limited resources
* Avoid excessive memory allocation in loops
* Use `gc()` to trigger garbage collection when needed
* Prefer native functions over JavaScript implementations for performance-critical code
