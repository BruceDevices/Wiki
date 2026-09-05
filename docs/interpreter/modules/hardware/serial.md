# Serial

The Serial module provides serial communication functionality for console output and command execution.

## Output Functions

### `serial.print(...args)`

Prints values to serial output without a newline.

**Parameters:**

- `...args` (any): Multiple arguments of any type (separated by spaces)

**Returns:** `undefined`

**Supported Types:**

- `undefined` → "undefined"
- `null` → "null"
- `number` → string representation
- `boolean` → "true" or "false"
- `string` → direct output

**Example:**

```javascript
var serial = require("serial");

serial.print("Temperature: ");
serial.print(25.6);
serial.print("°C");
// Output: Temperature: 25.6°C

// Multiple arguments
serial.print("Values:", 1, 2, 3, true);
// Output: Values: 1 2 3 true
```

### `serial.println(...args)`

Prints values to serial output with a newline.

**Parameters:**

| Parameter | Type   | Description                                          |
| --------- | ------ | ---------------------------------------------------- |
| `...args` | `any`  | Multiple arguments of any type (separated by spaces) |

**Returns:** `undefined`

**Example:**

```javascript
var serial = require("serial");

serial.println("Hello World");
serial.println("Number:", 42);
serial.println(); // Empty line

// Multiple values
serial.println("Sensor data:", temp, humidity, pressure);
```

### `serial.write(...args)`

Alias for `serial.print()` - writes data without newline.

**Parameters:**

| Parameter | Type   | Description        |
| --------- | ------ | ------------------ |
| `...args` | `any`  | Arguments to write |

**Returns:** `undefined`

## Input Functions

### `serial.readln(timeout)`

Reads a line from serial input with timeout.

**Parameters:**

| Parameter | Type                     | Description                |
| --------- | ------------------------ | -------------------------- |
| `timeout` | `number, default: 10000` | Timeout in milliseconds    |

**Returns:** `string` - Read line (without newline), or empty string on timeout

**Example:**

```javascript
var serial = require("serial");

serial.println("Enter your name:");
const name = serial.readln(5000); // 5 second timeout

if (name) {
    serial.println("Hello, " + name + "!");
} else {
    serial.println("No input received");
}
```

## Command Execution

### `serial.cmd(command)`

Executes a serial CLI command.

**Parameters:**

| Parameter | Type     | Description                |
| --------- | -------- | -------------------------- |
| `command` | `string` | CLI command to execute     |

**Returns:** `boolean` - Success status

**Example:**

```javascript
var serial = require("serial");

// Execute built-in CLI commands
serial.cmd("help");
serial.cmd("wifi scan");
serial.cmd("device info");

// Check command success
if (serial.cmd("rfid read")) {
    serial.println("RFID command executed successfully");
} else {
    serial.println("RFID command failed");
}
```

## Usage Examples

```javascript
// Serial Logger
class SerialLogger {
    constructor(prefix = "[LOG]") {
        this.prefix = prefix;
        this.startTime = now();
    }

    log(level, message) {
        const timestamp = (now() - this.startTime).toFixed(0);
        serial.println(`${this.prefix} [${timestamp}ms] ${level}: ${message}`);
    }

    info(message) {
        this.log("INFO", message);
    }

    warn(message) {
        this.log("WARN", message);
    }

    error(message) {
        this.log("ERROR", message);
    }

    debug(message) {
        this.log("DEBUG", message);
    }
}

// Usage
const logger = new SerialLogger("[BRUCE]");
logger.info("System started");
logger.warn("Low battery detected");
logger.error("Connection failed");

// Data Monitor
function monitorSensorData() {
    const logger = new SerialLogger("[SENSOR]");

    logger.info("Starting sensor monitoring...");

    const interval = setInterval(() => {
        // Simulate sensor readings
        const temperature = 20 + random(10);
        const humidity = 40 + random(30);
        const battery = device.getBatteryCharge();

        // Log sensor data
        serial.print("TEMP:");
        serial.print(temperature.toFixed(1));
        serial.print(",HUMID:");
        serial.print(humidity.toFixed(1));
        serial.print(",BATT:");
        serial.println(battery);

        // Check thresholds
        if (temperature > 28) {
            logger.warn(`High temperature: ${temperature.toFixed(1)}°C`);
        }

        if (battery < 20) {
            logger.warn(`Low battery: ${battery}%`);
        }

    }, 2000);

    // Stop after 30 seconds
    setTimeout(() => {
        clearInterval(interval);
        logger.info("Monitoring stopped");
    }, 30000);
}

// Interactive Console
function interactiveConsole() {
    serial.println("=== Bruce Interactive Console ===");
    serial.println("Type 'help' for commands, 'exit' to quit");

    while (true) {
        serial.print("> ");
        const command = serial.readln(30000); // 30 second timeout

        if (!command || command === "exit") {
            serial.println("Goodbye!");
            break;
        }

        if (command === "help") {
            showConsoleHelp();
        } else if (command === "status") {
            showSystemStatus();
        } else if (command === "clear") {
            // Clear screen (send ANSI escape codes)
            serial.print("\x1b[2J\x1b[H");
        } else if (command.startsWith("echo ")) {
            const text = command.substring(5);
            serial.println(text);
        } else if (command.startsWith("delay ")) {
            const ms = parseInt(command.substring(6));
            if (!isNaN(ms)) {
                serial.println(`Waiting ${ms}ms...`);
                delay(ms);
                serial.println("Done.");
            } else {
                serial.println("Invalid delay value");
            }
        } else {
            // Try to execute as CLI command
            if (!serial.cmd(command)) {
                serial.println("Unknown command: " + command);
            }
        }
    }
}

function showConsoleHelp() {
    serial.println("Available commands:");
    serial.println("  help          - Show this help");
    serial.println("  status        - Show system status");
    serial.println("  clear         - Clear screen");
    serial.println("  echo <text>   - Echo text");
    serial.println("  delay <ms>    - Wait specified milliseconds");
    serial.println("  exit          - Exit console");
    serial.println("  <cli_cmd>     - Execute CLI command");
}

function showSystemStatus() {
    serial.println("=== System Status ===");
    serial.println("Device: " + device.getName());
    serial.println("Board: " + device.getBoard());
    serial.println("Firmware: " + device.getBruceVersion());
    serial.println("Battery: " + device.getBatteryCharge() + "%");

    const memory = device.getFreeHeapSize();
    serial.println("Memory: " + memory.free + " / " + memory.total + " bytes");

    serial.println("WiFi: " + (wifi.connected() ? "Connected" : "Disconnected"));

    const storage_space = storage.spaceLittleFS();
    const storagePercent = (storage_space.used / storage_space.total * 100).toFixed(1);
    serial.println("Storage: " + storagePercent + "% used");
}

// Data Streaming
function streamSensorData() {
    serial.println("# Starting sensor data stream");
    serial.println("# Format: timestamp,temperature,humidity,battery");

    const startTime = now();

    const stream = setInterval(() => {
        const timestamp = now() - startTime;
        const temp = (20 + random(10)).toFixed(1);
        const humid = (50 + random(20)).toFixed(1);
        const battery = device.getBatteryCharge();

        // CSV format for easy parsing
        serial.println(`${timestamp},${temp},${humid},${battery}`);

    }, 1000);

    // Stream for 60 seconds
    setTimeout(() => {
        clearInterval(stream);
        serial.println("# Stream ended");
    }, 60000);
}

// Command Parser
class CommandParser {
    constructor() {
        this.commands = {};
        this.registerDefaults();
    }

    register(name, handler, description = "") {
        this.commands[name] = {
            handler: handler,
            description: description
        };
    }

    registerDefaults() {
        this.register("help", () => this.showHelp(), "Show available commands");

        this.register("time", () => {
            serial.println("Current time: " + new Date(now()).toISOString());
        }, "Show current time");

        this.register("uptime", () => {
            const uptimeMs = now() - this.startTime;
            const seconds = Math.floor(uptimeMs / 1000);
            serial.println(`Uptime: ${seconds} seconds`);
        }, "Show system uptime");

        this.register("memory", () => {
            const mem = device.getFreeHeapSize();
            const used = mem.total - mem.free;
            const percent = (used / mem.total * 100).toFixed(1);
            serial.println(`Memory: ${used}/${mem.total} bytes (${percent}%)`);
        }, "Show memory usage");
    }

    parse(input) {
        const parts = input.trim().split(/\\s+/);
        const command = parts[0];
        const args = parts.slice(1);

        if (this.commands[command]) {
            try {
                this.commands[command].handler(args);
            } catch (error) {
                serial.println("Command error: " + error.message);
            }
        } else {
            serial.println("Unknown command: " + command);
            serial.println("Type 'help' for available commands");
        }
    }

    showHelp() {
        serial.println("Available commands:");
        Object.keys(this.commands).sort().forEach(cmd => {
            const desc = this.commands[cmd].description;
            serial.println(`  ${cmd.padEnd(15)} - ${desc}`);
        });
    }

    run() {
        this.startTime = now();
        serial.println("Command parser started. Type 'help' for commands.");

        while (true) {
            serial.print("$ ");
            const input = serial.readln(60000);

            if (!input || input === "exit" || input === "quit") {
                serial.println("Exiting...");
                break;
            }

            this.parse(input);
        }
    }
}

// Network Status Monitor
function networkMonitor() {
    serial.println("=== Network Monitor ===");

    let lastWiFiStatus = wifi.connected();

    const monitor = setInterval(() => {
        const currentWiFiStatus = wifi.connected();

        if (currentWiFiStatus !== lastWiFiStatus) {
            if (currentWiFiStatus) {
                serial.println("[NET] WiFi connected - IP: " + wifi.ipAddress());
            } else {
                serial.println("[NET] WiFi disconnected");
            }
            lastWiFiStatus = currentWiFiStatus;
        }

        // Periodic status
        const timestamp = new Date(now()).toISOString();
        serial.println(`[${timestamp}] WiFi: ${currentWiFiStatus ? 'UP' : 'DOWN'}`);

    }, 5000);

    // Run for 2 minutes
    setTimeout(() => {
        clearInterval(monitor);
        serial.println("[NET] Monitor stopped");
    }, 120000);
}

// Debug Utility
function debugUtility() {
    // Redirect console.log to serial
    const originalLog = console.log;
    console.log = function(...args) {
        serial.print("[DEBUG] ");
        serial.println(...args);
    };

    // Test various operations
    console.log("Debug utility started");

    // Test device functions
    console.log("Device name:", device.getName());
    console.log("Battery level:", device.getBatteryCharge());

    // Test GPIO
    try {
        gpio.pinMode(2, "OUTPUT");
        gpio.digitalWrite(2, true);
        console.log("GPIO test: LED on");
        delay(1000);
        gpio.digitalWrite(2, false);
        console.log("GPIO test: LED off");
    } catch (error) {
        console.log("GPIO test failed:", error.message);
    }

    // Test storage
    try {
        storage.write("/debug_test.txt", "Debug test file");
        const content = storage.read("/debug_test.txt");
        console.log("Storage test:", content);
        storage.remove("/debug_test.txt");
    } catch (error) {
        console.log("Storage test failed:", error.message);
    }

    // Restore original console.log
    console.log = originalLog;

    serial.println("Debug utility completed");
}

// Practical Examples
function practicalExamples() {
    const choice = dialog.choice([
        "Sensor Monitor",
        "Interactive Console",
        "Data Stream",
        "Network Monitor",
        "Debug Utility",
        "Command Parser",
        "Exit"
    ]);

    switch (choice) {
        case 0:
            monitorSensorData();
            break;
        case 1:
            interactiveConsole();
            break;
        case 2:
            streamSensorData();
            break;
        case 3:
            networkMonitor();
            break;
        case 4:
            debugUtility();
            break;
        case 5:
            new CommandParser().run();
            break;
    }
}
```

## Output Formatting

### ANSI Escape Codes

```javascript
// Colors
serial.print("\x1b[31mRed text\x1b[0m");      // Red text
serial.print("\x1b[32mGreen text\x1b[0m");    // Green text
serial.print("\x1b[33mYellow text\x1b[0m");   // Yellow text
serial.print("\x1b[34mBlue text\x1b[0m");     // Blue text

// Formatting
serial.print("\x1b[1mBold text\x1b[0m");      // Bold
serial.print("\x1b[4mUnderline\x1b[0m");      // Underline

// Cursor control
serial.print("\x1b[2J");        // Clear screen
serial.print("\x1b[H");         // Home cursor
serial.print("\x1b[10;5H");     // Move cursor to row 10, col 5
```

### Data Tables

```javascript
var serial = require("serial");

function printTable(headers, rows) {
    // Print header
    const headerRow = headers.join(" | ");
    serial.println(headerRow);
    serial.println("-".repeat(headerRow.length));

    // Print rows
    rows.forEach(row => {
        serial.println(row.join(" | "));
    });
}

// Usage
printTable(
    ["Name", "Value", "Unit"],
    [
        ["Temperature", "25.6", "°C"],
        ["Humidity", "60.2", "%"],
        ["Pressure", "1013.2", "hPa"]
    ]
);
```
