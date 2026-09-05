# Device

The Device module provides access to system information, hardware status, and device capabilities. These functions are available globally and provide essential information about the Bruce firmware and underlying hardware.

## Device Information

### `device.getName()`

Returns the device name configured for WiFi AP mode.

**Parameters:** None

**Returns:** `string` - Device name (defaults to "Bruce" if not configured)

**Example:**

```javascript linenums="1"
var device = require("device");
var deviceName = device.getName();
console.log("Device name: " + deviceName);

// Use in WiFi AP configuration or identification
if (deviceName === "BruceNet") {
    console.log("Using default device name");
} else {
    console.log("Custom device name configured: " + deviceName);
}
```

### `device.getBoard()`

Returns the board/hardware model identifier.

**Parameters:** None

**Returns:** `string` - Board identifier (e.g., "M5StickC-Plus", "ESP32-Dev", etc.)

**Example:**

```javascript linenums="1"
var device = require("device");
var board = device.getBoard();
console.log("Board: " + board);

// Hardware-specific functionality
switch (board) {
    case "M5StickC Plus2":
        console.log("M5StickC Plus2 detected - enabling specific features");
        break;
    case "Lilygo T-Embed CC1101":
        console.log("Lilygo T-Embed CC1101 detected - enabling specific features");
        break;
    default:
        console.log("Other board: " + board);
}
```

### `device.getBruceVersion()`

Returns the current Bruce firmware version.

**Parameters:** None

**Returns:** `string` - Firmware version string

**Example:**

```javascript linenums="1"
var device = require("device");
var display = require("display");
var keyboard = require("keyboard");
var version = device.getBruceVersion();
console.log("Bruce firmware version: " + version);

// Version checking for compatibility
var majorVersion = version.split('.')[0];
if (parseInt(majorVersion) >= 1) {
    console.log("Modern Bruce version - all features available");
} else {
    console.log("Older Bruce version - some features may be limited");
}

// Display version in status screen

display.fill(0);
display.setTextColor(display.color(255, 255, 255));
display.setTextSize(2);
display.drawString("Bruce v" + version, 10, 20);

while (true) {
    if (keyboard.getEscPress()) break;
}
```

## Battery and Power Management

### `device.getBatteryCharge()`

Returns the current battery percentage.

**Parameters:** None

**Returns:** `number` - Battery percentage (0-100)

**Example:**

```javascript linenums="1"
var device = require("device");
var batteryLevel = device.getBatteryCharge();
console.log("Battery: " + batteryLevel + "%");

// Low battery warning
if (batteryLevel < 15) {
    console.log("Low Battery", "Battery level: " + batteryLevel + "%\\nPlease charge device");
} else {
    console.log("Battery level: " + batteryLevel + "%");
}
```

### `device.getBatteryDetailed()`

Returns comprehensive battery and power information (if supported by hardware).

**Parameters:** None

**Returns:** `object` - Detailed battery information object

**Object Properties:**

| Property              | Type      | Description                                            |
| --------------------- | --------- | ------------------------------------------------------ |
| `battery_percent`     | `number`  | Battery percentage (0-100)                             |
| `remaining_capacity`  | `number`  | Remaining capacity in mAh (if supported)               |
| `full_capacity`       | `number`  | Full charge capacity in mAh (if supported)             |
| `design_capacity`     | `number`  | Design capacity in mAh (if supported)                  |
| `is_charging`         | `boolean` | Whether device is currently charging (if supported)    |
| `charging_voltage`    | `number`  | Charging voltage in volts (if supported)               |
| `charging_current`    | `number`  | Charging current in mA (if supported)                  |
| `time_to_empty`       | `number`  | Estimated time to empty in minutes (if supported)      |
| `average_power_use`   | `number`  | Average power consumption in mW (if supported)         |
| `voltage`             | `number`  | Battery voltage in volts (if supported)                |
| `voltage_raw`         | `number`  | Raw voltage reading (if supported)                     |
| `current_instant`     | `number`  | Instantaneous current in mA (if supported)             |
| `current_average`     | `number`  | Average current in mA (if supported)                   |
| `current_raw`         | `number`  | Raw current reading (if supported)                     |

**Note:** Advanced properties are only available on hardware with BQ27220 battery management IC.

**Example:**

```javascript linenums="1"
var device = require("device");
var batteryInfo = device.getBatteryDetailed();

console.log("Battery Details:");
console.log("- Percentage: " + batteryInfo.battery_percent + "%");

// Check if advanced battery management is available
if (batteryInfo.remaining_capacity !== undefined) {
    console.log("- Remaining: " + batteryInfo.remaining_capacity + " mAh");
    console.log("- Full capacity: " + batteryInfo.full_capacity + " mAh");
    console.log("- Design capacity: " + batteryInfo.design_capacity + " mAh");
    console.log("- Charging: " + (batteryInfo.is_charging ? "Yes" : "No"));
    console.log("- Voltage: " + batteryInfo.voltage.toFixed(2) + " V");

    if (batteryInfo.time_to_empty > 0) {
        var hours = Math.floor(batteryInfo.time_to_empty / 60);
        var minutes = batteryInfo.time_to_empty % 60;
        console.log("- Time to empty: " + hours + "h " + minutes + "m");
    }

    console.log("- Average power: " + batteryInfo.average_power_use + " mW");
} else {
    console.log("- Advanced battery info not available on this hardware");
}

// Battery health check
function checkBatteryHealth() {
    var info = device.getBatteryDetailed();

    if (info.full_capacity && info.design_capacity) {
        var health = (info.full_capacity / info.design_capacity) * 100;
        console.log("Battery health: " + health.toFixed(1) + "%");

        if (health < 80) {
            console.log("Battery Health", "Battery capacity degraded to " +
                         health.toFixed(1) + "% of original capacity");
        }
    }
}
```

## Memory Management

### `device.getFreeHeapSize()`

Returns detailed memory usage information.

**Parameters:** None

**Returns:** `object` - Memory information object

**Object Properties:**

| Property                  | Type     | Description                               |
| ------------------------- | -------- | ----------------------------------------- |
| `ram_free`                | `number` | Free RAM in bytes                         |
| `ram_min_free`            | `number` | Minimum free RAM since boot in bytes      |
| `ram_largest_free_block`  | `number` | Largest contiguous free block in bytes    |
| `ram_size`                | `number` | Total RAM size in bytes                   |
| `psram_free`              | `number` | Free PSRAM in bytes                       |
| `psram_size`              | `number` | Total PSRAM size in bytes                 |

**Example:**

```javascript linenums="1"
var device = require("device");
var display = require("display");
var memInfo = device.getFreeHeapSize();

console.log("Memory Usage:");
console.log("- Free RAM: " + (memInfo.ram_free / 1024).toFixed(1) + " KB");
console.log("- Total RAM: " + (memInfo.ram_size / 1024).toFixed(1) + " KB");
console.log("- RAM usage: " + (((memInfo.ram_size - memInfo.ram_free) / memInfo.ram_size) * 100).toFixed(1) + "%");
console.log("- Minimum free: " + (memInfo.ram_min_free / 1024).toFixed(1) + " KB");
console.log("- Largest block: " + (memInfo.ram_largest_free_block / 1024).toFixed(1) + " KB");

if (memInfo.psram_size > 0) {
    console.log("- Free PSRAM: " + (memInfo.psram_free / 1024).toFixed(1) + " KB");
    console.log("- Total PSRAM: " + (memInfo.psram_size / 1024).toFixed(1) + " KB");
} else {
    console.log("- PSRAM: Not available");
}

// Memory monitoring function
function monitorMemory() {
    var mem = device.getFreeHeapSize();
    var freePercent = (mem.ram_free / mem.ram_size) * 100;

    if (freePercent < 10) {
        console.log("WARNING: Low memory! Only " + freePercent.toFixed(1) + "% free");
        return false;
    } else if (freePercent < 25) {
        console.log("CAUTION: Memory usage high, " + freePercent.toFixed(1) + "% free");
        return true;
    }

    console.log("Memory OK: " + freePercent.toFixed(1) + "% free");
    return true;
}

// Memory visualization
function displayMemoryChart() {
    var mem = device.getFreeHeapSize();
    var usedRam = mem.ram_size - mem.ram_free;
    var usedPercent = (usedRam / mem.ram_size) * 100;

    display.fill(0);
    display.setTextColor(display.color(255, 255, 255));
    display.setTextSize(1);
    display.drawString("Memory Usage", 10, 10);

    // Memory bar
    var barWidth = 100;
    var barHeight = 20;
    var usedWidth = (usedPercent / 100) * barWidth;

    // Background bar
    display.drawRect(10, 30, barWidth, barHeight, display.color(255, 255, 255));

    // Used memory (red)
    display.drawFillRect(10, 30, usedWidth, barHeight, display.color(255, 0, 0));

    // Text info
    display.drawString("Used: " + (usedRam / 1024).toFixed(0) + " KB (" + usedPercent.toFixed(1) + "%)", 10, 55);
    display.drawString("Free: " + (mem.ram_free / 1024).toFixed(0) + " KB", 10, 70);

}

while (true) {
    if (keyboard.getEscPress()) break;

    monitorMemory();
    displayMemoryChart();
    delay(1000); // Update every 1 seconds
}
```

### `device.getEEPROMSize()`

Returns the EEPROM size available for configuration storage.

**Parameters:** None

**Returns:** `number` - EEPROM size in bytes

**Example:**

```javascript linenums="1"
var device = require("device");
var eepromSize = device.getEEPROMSize();
console.log("EEPROM size: " + eepromSize + " bytes");

// Configuration management
function checkConfigSpace() {
    var eepromSize = device.getEEPROMSize();
    console.log("Available EEPROM: " + eepromSize + " bytes");

    if (eepromSize >= 4096) {
        console.log("Sufficient space for full configuration");
    } else {
        console.log("Limited configuration space available");
    }
}

// Storage calculation
var storageInfo = {
    eeprom: device.getEEPROMSize(),
    ram: device.getFreeHeapSize().ram_size,
    psram: device.getFreeHeapSize().psram_size
};

console.log("Storage Summary:");
console.log("- EEPROM: " + storageInfo.eeprom + " bytes (config)");
console.log("- RAM: " + (storageInfo.ram / 1024).toFixed(0) + " KB (runtime)");
console.log("- PSRAM: " + (storageInfo.psram / 1024).toFixed(0) + " KB (extended)");
```

## System Information Applications

### Device Status Display

```javascript linenums="1"
var device = require("device");
var display = require("display");

function showDeviceStatus() {
    display.fill(0);
    display.setTextColor(display.color(255, 255, 255));
    display.setTextSize(1);

    var y = 5;
    var lineHeight = 12;

    // Device info
    display.drawString("Device: " + device.getName(), 5, y);
    y += lineHeight;
    display.drawString("Board: " + device.getBoard(), 5, y);
    y += lineHeight;
    display.drawString("Version: " + device.getBruceVersion(), 5, y);
    y += lineHeight;

    // Battery
    var battery = device.getBatteryCharge();
    var batteryColor = display.color(0, 255, 0);
    if (battery < 20) batteryColor = display.color(255, 0, 0);
    else if (battery < 50) batteryColor = display.color(255, 255, 0);

    display.setTextColor(batteryColor);
    display.drawString("Battery: " + battery + "%", 5, y);
    y += lineHeight;

    // Memory
    display.setTextColor(display.color(255, 255, 255));
    var mem = device.getFreeHeapSize();
    var freeKB = (mem.ram_free / 1024).toFixed(0);
    var totalKB = (mem.ram_size / 1024).toFixed(0);
    display.drawString("RAM: " + freeKB + "/" + totalKB + " KB", 5, y);

}

while (true) {
    if (keyboard.getEscPress()) break;
    showDeviceStatus();
    delay(1000);
}
```


### Hardware Compatibility Check

```javascript linenums="1"
var device = require("device");

function repeatChar(ch, count) {
    var s = "";
    var i;
    for (i = 0; i < count; i++) {
        s += ch;
    }
    return s;
}

function checkHardwareCapabilities() {
    console.log("Hardware Capability Check");
    console.log("=" + repeatChar("=", 25));

    var board = device.getBoard();
    var mem = device.getFreeHeapSize();
    var battery = device.getBatteryDetailed();

    /* Basic info */
    console.log("Board: " + board);
    console.log("Firmware: " + device.getBruceVersion());

    /* Memory capabilities */
    console.log("\nMemory:");
    console.log("- RAM: " + (mem.ram_size / 1024).toFixed(0) + " KB");

    if (mem.psram_size > 0) {
        console.log("- PSRAM: " + (mem.psram_size / 1024).toFixed(0) + " KB \u2713");
    } else {
        console.log("- PSRAM: Not available");
    }

    /* Battery management */
    console.log("\nPower Management:");
    console.log("- Basic battery: \u2713");

    if (battery && battery.remaining_capacity != undefined) {
        console.log("- Advanced battery: \u2713 (BQ27220)");
        console.log("  - Capacity monitoring: \u2713");
        console.log("  - Charge detection: \u2713");
        console.log("  - Time estimation: \u2713");
    } else {
        console.log("- Advanced battery: Not available");
    }

    /* Recommendations */
    console.log("\nRecommendations:");

    if (mem.ram_size < (320 * 1024)) {
        console.log("- Consider memory usage optimization for this device");
    }

    if (mem.psram_size == 0) {
        console.log("- Large data processing may be limited without PSRAM");
    }

    if (!battery || battery.remaining_capacity == undefined) {
        console.log("- Battery monitoring is basic - no capacity/health info");
    }
}

checkHardwareCapabilities();
```
