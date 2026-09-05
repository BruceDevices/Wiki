# I2C

The I2C module provides I2C bus communication functionality for interfacing with I2C devices.

## Initialization

### `i2c.begin(sda, scl, frequency)`

Initializes the I2C bus with specified pins and frequency.

**Parameters:**

| Parameter   | Type     | Description                            |
| ----------- | -------- | -------------------------------------- |
| `sda`       | `number` | SDA (data) pin number (must be ≥ 0)    |
| `scl`       | `number` | SCL (clock) pin number (must be ≥ 0)   |
| `frequency` | `number` | I2C frequency in Hz (1000 to 1000000)  |

**Returns:** `boolean` - True if initialization successful

**Example:**

```javascript
var i2c = require("i2c");

// Initialize I2C on pins 21 (SDA) and 22 (SCL) at 100kHz
if (i2c.begin(21, 22, 100000)) {
    console.log("I2C initialized successfully");
} else {
    console.log("I2C initialization failed");
}

// Common ESP32 I2C configurations
i2c.begin(21, 22, 100000);  // Standard ESP32
i2c.begin(4, 5, 400000);    // Alternative pins, fast mode
```

## Device Discovery

### `i2c.scan()`

Scans the I2C bus for connected devices.

**Parameters:** None

**Returns:** `array` - Array of found device addresses (7-bit addresses, 1-127)

**Example:**

```javascript
var i2c = require("i2c");

// Must initialize I2C first
if (!i2c.begin(21, 22, 100000)) {
    console.log("I2C init failed");
    return;
}

const devices = i2c.scan();
console.log(`Found ${devices.length} I2C devices:`);

devices.forEach(addr => {
    console.log(`  Device at address 0x${addr.toString(16).toUpperCase()}`);
});

// Check for specific device
const OLED_ADDR = 0x3C;
if (devices.includes(OLED_ADDR)) {
    console.log("OLED display found!");
}
```

## Data Transfer

### `i2c.write(address, data, stop)`

Writes data to an I2C device.

**Parameters:**

| Parameter | Type                | Description                              |
| --------- | ------------------- | ---------------------------------------- |
| `address` | `number`            | 7-bit I2C device address (1-127)         |
| `data`    | `string/Uint8Array` | Data to write (string or byte array)     |
| `stop`    | `boolean, optional` | Send stop condition (default: true)      |

**Returns:** `number` - Error code (0 = success, non-zero = error)

**Example:**

```javascript
var i2c = require("i2c");

// Write string data
const result = i2c.write(0x3C, "Hello I2C");
if (result === 0) {
    console.log("Write successful");
} else {
    console.log("Write failed, error:", result);
}

// Write binary data
const data = new Uint8Array([0x00, 0x10, 0xFF, 0x20]);
const error = i2c.write(0x48, data);

// Write command without stop condition (for repeated start)
i2c.write(0x3C, new Uint8Array([0x80, 0x01]), false);
```

### `i2c.read(address, length)`

⚠️ **Currently Not Implemented**

Reads data from an I2C device.

**Parameters:**

| Parameter | Type     | Description                      |
| --------- | -------- | -------------------------------- |
| `address` | `number` | 7-bit I2C device address         |
| `length`  | `number` | Number of bytes to read          |

**Returns:** Currently throws "not implemented" error

### `i2c.writeRead(address, writeData, readLength)`

⚠️ **Currently Not Implemented**

Performs a write followed by read operation (common I2C pattern).

**Parameters:**

| Parameter    | Type                | Description                       |
| ------------ | ------------------- | --------------------------------- |
| `address`    | `number`            | 7-bit I2C device address          |
| `writeData`  | `string/Uint8Array` | Data to write first               |
| `readLength` | `number`            | Number of bytes to read           |

**Returns:** Currently throws "not implemented" error

## Common I2C Device Addresses

| Device Type                | Typical Address | Notes              |
| -------------------------- | --------------- | ------------------ |
| OLED Display (SSD1306)     | 0x3C, 0x3D      | Common displays    |
| RTC (DS1307)               | 0x68            | Real-time clock    |
| Temperature (DS18B20)      | 0x48-0x4F       | Temperature sensor |
| Accelerometer (MPU6050)    | 0x68, 0x69      | Motion sensor      |
| EEPROM (24C32)             | 0x50-0x57       | Memory             |
| ADC (ADS1115)              | 0x48-0x4B       | Analog-to-digital  |

## Usage Examples

```javascript
var i2c = require("i2c");
var dialog = require("dialog");

// I2C Device Scanner
function scanI2CDevices() {
    console.log("Scanning I2C bus...");

    if (!i2c.begin(21, 22, 100000)) {
        dialog.error("I2C initialization failed");
        return;
    }

    const devices = i2c.scan();

    if (devices.length === 0) {
        dialog.info("No I2C devices found");
        return;
    }

    let report = `Found ${devices.length} I2C device(s):\\n\\n`;

    devices.forEach(addr => {
        const hex = "0x" + addr.toString(16).toUpperCase().padStart(2, '0');
        const deviceType = identifyDevice(addr);
        report += `${hex} - ${deviceType}\\n`;
    });

    dialog.viewText(report, "I2C Device Scan");
}

function identifyDevice(address) {
    const knownDevices = {
        0x3C: "OLED Display (SSD1306)",
        0x3D: "OLED Display (SSD1306)",
        0x68: "RTC (DS1307) or MPU6050",
        0x48: "ADS1115 ADC or Temperature Sensor",
        0x49: "ADS1115 ADC",
        0x4A: "ADS1115 ADC",
        0x4B: "ADS1115 ADC",
        0x50: "EEPROM (24C32)",
        0x51: "EEPROM (24C32)",
        0x52: "EEPROM (24C32)",
        0x53: "EEPROM (24C32)",
        0x54: "EEPROM (24C32)",
        0x55: "EEPROM (24C32)",
        0x56: "EEPROM (24C32)",
        0x57: "EEPROM (24C32)"
    };

    return knownDevices[address] || "Unknown Device";
}

// OLED Display Example (SSD1306)
class OLED_SSD1306 {
    constructor(address = 0x3C) {
        this.address = address;
        this.width = 128;
        this.height = 64;
    }

    init() {
        if (!i2c.begin(21, 22, 100000)) {
            throw new Error("I2C init failed");
        }

        // Check if device is present
        const devices = i2c.scan();
        if (!devices.includes(this.address)) {
            throw new Error(`OLED not found at 0x${this.address.toString(16)}`);
        }

        // Send initialization commands
        const initCommands = [
            0xAE, // Display OFF
            0xD5, 0x80, // Set display clock
            0xA8, 0x3F, // Set multiplex ratio
            0xD3, 0x00, // Set display offset
            0x40, // Set start line
            0x8D, 0x14, // Charge pump
            0x20, 0x00, // Memory addressing mode
            0xA1, // Set segment re-map
            0xC8, // Set COM output scan direction
            0xDA, 0x12, // Set COM pins
            0x81, 0xCF, // Set contrast
            0xD9, 0xF1, // Set pre-charge period
            0xDB, 0x40, // Set VCOMH
            0xA4, // Display follows RAM content
            0xA6, // Set normal display
            0xAF  // Display ON
        ];

        this.sendCommands(initCommands);
    }

    sendCommand(cmd) {
        const data = new Uint8Array([0x80, cmd]);
        return i2c.write(this.address, data) === 0;
    }

    sendCommands(commands) {
        for (let cmd of commands) {
            if (!this.sendCommand(cmd)) {
                throw new Error(`Failed to send command 0x${cmd.toString(16)}`);
            }
        }
    }

    sendData(data) {
        const buffer = new Uint8Array([0x40, ...data]);
        return i2c.write(this.address, buffer) === 0;
    }

    clear() {
        // Clear display memory
        const clearData = new Array(1024).fill(0x00);

        // Set addressing
        this.sendCommands([0x21, 0x00, 0x7F]); // Column address
        this.sendCommands([0x22, 0x00, 0x07]); // Page address

        // Send clear data in chunks
        const chunkSize = 32;
        for (let i = 0; i < clearData.length; i += chunkSize) {
            const chunk = clearData.slice(i, i + chunkSize);
            this.sendData(chunk);
        }
    }

    test() {
        this.clear();

        // Draw test pattern
        const testData = [];
        for (let i = 0; i < 128; i++) {
            testData.push(0xFF); // Full row
        }

        this.sendData(testData);
    }
}

// Temperature Sensor Example (hypothetical device at 0x48)
class TemperatureSensor {
    constructor(address = 0x48) {
        this.address = address;
    }

    init() {
        if (!i2c.begin(21, 22, 100000)) {
            throw new Error("I2C init failed");
        }

        const devices = i2c.scan();
        if (!devices.includes(this.address)) {
            throw new Error("Temperature sensor not found");
        }

        // Configure sensor (example configuration)
        const config = new Uint8Array([0x01, 0x60, 0xA0]); // Config register
        if (i2c.write(this.address, config) !== 0) {
            throw new Error("Sensor configuration failed");
        }
    }

    // Note: read() not implemented in I2C module yet
    readTemperature() {
        // This would require i2c.read() to be implemented
        throw new Error("Reading not yet supported - i2c.read() not implemented");

        // When implemented, would look like:
        // const tempReg = new Uint8Array([0x00]);
        // i2c.write(this.address, tempReg, false);
        // const data = i2c.read(this.address, 2);
        // return parseTemperature(data);
    }

    // Workaround: trigger conversion only
    triggerConversion() {
        const convertCmd = new Uint8Array([0x51]); // Start conversion
        return i2c.write(this.address, convertCmd) === 0;
    }
}

// RTC Example (DS1307)
class RTC_DS1307 {
    constructor(address = 0x68) {
        this.address = address;
    }

    init() {
        if (!i2c.begin(21, 22, 100000)) {
            throw new Error("I2C init failed");
        }

        const devices = i2c.scan();
        if (!devices.includes(this.address)) {
            throw new Error("RTC not found");
        }
    }

    // Enable oscillator
    enable() {
        // Clear CH bit in register 0x00
        const data = new Uint8Array([0x00, 0x00]);
        return i2c.write(this.address, data) === 0;
    }

    setTime(hour, minute, second) {
        const data = new Uint8Array([
            0x00, // Start at seconds register
            this.decToBcd(second),
            this.decToBcd(minute),
            this.decToBcd(hour)
        ]);

        return i2c.write(this.address, data) === 0;
    }

    decToBcd(decimal) {
        return ((Math.floor(decimal / 10) << 4) | (decimal % 10));
    }
}

// I2C Device Monitor
function monitorI2CDevices() {
    if (!i2c.begin(21, 22, 100000)) {
        dialog.error("I2C initialization failed");
        return;
    }

    let lastDevices = [];

    const monitor = setInterval(() => {
        const currentDevices = i2c.scan();

        // Check for new devices
        const newDevices = currentDevices.filter(addr => !lastDevices.includes(addr));
        const removedDevices = lastDevices.filter(addr => !currentDevices.includes(addr));

        newDevices.forEach(addr => {
            console.log(`Device connected: 0x${addr.toString(16).toUpperCase()}`);
            notification.blink(200);
        });

        removedDevices.forEach(addr => {
            console.log(`Device disconnected: 0x${addr.toString(16).toUpperCase()}`);
            notification.blink(1000);
        });

        lastDevices = currentDevices;

        // Update status
        dialog.drawStatusBar(`I2C: ${currentDevices.length} devices`);

    }, 2000);

    // Stop monitoring after 30 seconds or on key press
    setTimeout(() => {
        clearInterval(monitor);
        console.log("I2C monitoring stopped");
    }, 30000);
}

// Practical I2C utility functions
function i2cUtilities() {
    const choice = dialog.choice([
        "Scan Devices",
        "Test OLED",
        "Monitor Devices",
        "Exit"
    ]);

    switch (choice) {
        case 0:
            scanI2CDevices();
            break;

        case 1:
            try {
                const oled = new OLED_SSD1306();
                oled.init();
                dialog.info("OLED initialized");
                oled.test();
                dialog.success("OLED test complete");
            } catch (error) {
                dialog.error(`OLED test failed: ${error.message}`);
            }
            break;

        case 2:
            monitorI2CDevices();
            break;
    }
}
```

## Common I2C Patterns

### Device Detection

```javascript
var i2c = require("i2c");

function isDevicePresent(address) {
    const devices = i2c.scan();
    return devices.includes(address);
}
```

### Register Writing

```javascript
var i2c = require("i2c");

function writeRegister(deviceAddr, registerAddr, value) {
    const data = new Uint8Array([registerAddr, value]);
    return i2c.write(deviceAddr, data) === 0;
}
```

### Multi-byte Data

```javascript
var i2c = require("i2c");

function writeMultipleBytes(deviceAddr, startRegister, values) {
    const data = new Uint8Array([startRegister, ...values]);
    return i2c.write(deviceAddr, data) === 0;
}
```
