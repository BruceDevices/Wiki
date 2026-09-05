# Global

The Global module provides core JavaScript utilities and global functions that are available throughout the Bruce firmware JavaScript environment.

## Global Utility Functions

### `delay(ms)`

Pauses script execution for the specified number of milliseconds.

**Parameters:**

| Parameter | Type     | Description                |
| --------- | -------- | -------------------------- |
| `ms`      | `number` | Delay time in milliseconds |

**Returns:** None

**Example:**

```javascript linenums="1"
// Simple delay
console.log("Starting...");
delay(1000); // Wait 1 second
console.log("One second later");

// Blinking pattern with delays
for (var i = 0; i < 5; i++) {
    gpio.write(2, true);  // LED on
    delay(500);
    gpio.write(2, false); // LED off
    delay(500);
}

// Progressive delay
for (var i = 1; i <= 5; i++) {
    console.log("Countdown:", 6 - i);
    delay(i * 200); // Increasing delay
}
```

### `now()`

Returns the current timestamp in milliseconds since epoch (similar to `Date.now()`).

**Parameters:** None

**Returns:** `number` - Current timestamp in milliseconds

**Example:**

```javascript linenums="1"
// Timing operations
var startTime = now();
delay(1500);
var endTime = now();
var duration = endTime - startTime;

console.log("Operation took", duration, "ms");

// Performance measurement
function measureFunction(func) {
    var start = now();
    func();
    var elapsed = now() - start;
    console.log("Function executed in", elapsed, "ms");
    return elapsed;
}

// Usage
measureFunction(function() {
    // Some operation to measure
    for (var i = 0; i < 1000; i++) {
        Math.sqrt(i);
    }
});
```

### `random([min], [max])`

Generates random numbers with various parameter combinations.

**Parameters:**

| Parameter | Type               | Description                |
| --------- | ------------------ | -------------------------- |
| `min`     | `number, optional` | Minimum value (inclusive)  |
| `max`     | `number, optional` | Maximum value (exclusive)  |

**Returns:** `number` - Random number

**Overloads:**

- `random()` - Random value from 0 to RAND_MAX
- `random(max)` - Random value from 0 to max (exclusive)
- `random(min, max)` - Random value from min (inclusive) to max (exclusive)

**Example:**

```javascript linenums="1"
// Random between 0 and RAND_MAX
console.log("Large random:", random());

// Random between 0 and 10
console.log("0-9:", random(10));

// Random between 5 and 15
console.log("5-14:", random(5, 15));

// Dice roll (1-6)
function rollDice() {
    return random(1, 7);
}

// Random boolean
function randomBool() {
    return random(2) === 1;
}

// Random array element
function randomChoice(array) {
    return array[random(array.length)];
}

var colors = ["red", "green", "blue", "yellow"];
console.log("Random color:", randomChoice(colors));

// Random delay
function randomDelay() {
    var delayTime = random(100, 1000);
    console.log("Random delay:", delayTime, "ms");
    delay(delayTime);
}
```

### `parseInt(value)`

Converts a value to an integer (similar to JavaScript's parseInt but simplified).

**Parameters:**

| Parameter | Type                        | Description       |
| --------- | --------------------------  | ----------------- |
| `value`   | `string\|number\|boolean`   | Value to convert  |

**Returns:** `number` - Integer value or NaN if conversion fails

**Example:**

```javascript linenums="1"
// String to integer
console.log(parseInt("42"));        // 42
console.log(parseInt("42.7"));      // 42
console.log(parseInt("42abc"));     // 42

// Number to integer
console.log(parseInt(3.14159));     // 3
console.log(parseInt(-5.8));        // -5

// Boolean to integer
console.log(parseInt(true));        // 1
console.log(parseInt(false));       // 0

// Input validation
function getIntegerInput(prompt) {
    var keyboard = require("keyboard");
    var dialog = require("dialog");

    var input = keyboard.keyboard(prompt, "0");
    var value = parseInt(input);

    if (isNaN(value)) {
        dialog.error("Invalid number format");
        return null;
    }

    return value;
}

// Usage
var userAge = getIntegerInput("Enter your age:");
if (userAge !== null) {
    console.log("Age in months:", userAge * 12);
}
```

### `toString(value)`

Converts any value to its string representation.

**Parameters:**

| Parameter | Type   | Description                       |
| --------- | ------ | --------------------------------- |
| `value`   | `any`  | Value to convert to string        |

**Returns:** `string` - String representation of the value

**Example:**

```javascript
// Number to string
console.log(toString(42));          // "42"
console.log(toString(3.14159));     // "3.14159"

// Boolean to string
console.log(toString(true));        // "true"
console.log(toString(false));       // "false"

// Undefined/null handling
console.log(toString(undefined));   // "undefined"
console.log(toString(null));        // "null"

// Building dynamic messages
function formatMessage(type, code, message) {
    return toString(type) + " [" + toString(code) + "]: " + toString(message);
}

console.log(formatMessage("ERROR", 404, "Not found"));
// Output: "ERROR [404]: Not found"
```

### `toHexString(value)`

Converts a numeric value to its hexadecimal string representation.

**Parameters:**

| Parameter | Type                        | Description       |
| --------- | --------------------------- | ----------------- |
| `value`   | `number\|string\|boolean`   | Value to convert  |

**Returns:** `string` - Hexadecimal representation

**Example:**

```javascript
// Decimal to hex
console.log(toHexString(255));      // "FF"
console.log(toHexString(16));       // "10"
console.log(toHexString(0));        // "0"

// Display memory addresses or hardware values
function displayRegisterValue(name, value) {
    console.log(name + ": 0x" + toHexString(value));
}

displayRegisterValue("Status", 170);   // "Status: 0xAA"
displayRegisterValue("Control", 85);   // "Control: 0x55"

// Color value formatting
function formatColor(r, g, b) {
    return "#" +
           toHexString(r).padStart(2, '0') +
           toHexString(g).padStart(2, '0') +
           toHexString(b).padStart(2, '0');
}

console.log(formatColor(255, 128, 64)); // "#FF8040"
```

### `toLowerCase(string)`

Converts a string to lowercase.

**Parameters:**

| Parameter | Type     | Description         |
| --------- | -------- | ------------------- |
| `string`  | `string` | String to convert   |

**Returns:** `string` - Lowercase string

**Example:**

```javascript
// Basic conversion
console.log(toLowerCase("HELLO WORLD"));    // "hello world"
console.log(toLowerCase("JavaScript"));     // "javascript"
console.log(toLowerCase("MixedCASE"));      // "mixedcase"

// Case-insensitive comparison
function compareIgnoreCase(str1, str2) {
    return toLowerCase(str1) === toLowerCase(str2);
}

console.log(compareIgnoreCase("Hello", "HELLO"));  // true

// Command processing
function processCommand(cmd) {
    const lowerCmd = toLowerCase(cmd);

    switch (lowerCmd) {
        case "start":
            console.log("Starting system...");
            break;
        case "stop":
            console.log("Stopping system...");
            break;
        case "status":
            console.log("System is running");
            break;
        default:
            console.log("Unknown command:", cmd);
    }
}

processCommand("START");    // Works with any case
processCommand("Stop");     // Works with any case
```

### `toUpperCase(string)`

Converts a string to uppercase.

**Parameters:**

| Parameter | Type     | Description         |
| --------- | -------- | ------------------- |
| `string`  | `string` | String to convert   |

**Returns:** `string` - Uppercase string

**Example:**

```javascript
// Basic conversion
console.log(toUpperCase("hello world"));    // "HELLO WORLD"
console.log(toUpperCase("JavaScript"));     // "JAVASCRIPT"
console.log(toUpperCase("mixedcase"));      // "MIXEDCASE"

// Protocol names
function formatProtocol(protocol) {
    return toUpperCase(protocol);
}

console.log(formatProtocol("http"));    // "HTTP"
console.log(formatProtocol("https"));   // "HTTPS"
console.log(formatProtocol("ftp"));     // "FTP"

// Display formatting
function displayTitle(title) {
    const upperTitle = toUpperCase(title);
    const border = "=".repeat(upperTitle.length);

    console.log(border);
    console.log(upperTitle);
    console.log(border);
}

displayTitle("System Status");
// Output:
// =============
// SYSTEM STATUS
// =============
```

### `assert(condition, [message])`

Validates a condition and throws an error if it fails. Useful for debugging and testing.

**Parameters:**

| Parameter   | Type               | Description          |
| ----------- | ------------------ | -------------------- |
| `condition` | `boolean`          | Condition to test    |
| `message`   | `string, optional` | Custom error message |

**Returns:** `boolean` - Returns true if assertion passes

**Throws:** TypeError if assertion fails

**Example:**

```javascript
// Basic assertions
assert(true);                           // Passes
assert(1 === 1);                       // Passes
assert(2 > 1, "Math should work");     // Passes

try {
    assert(false, "This will fail");
} catch (e) {
    console.log("Caught:", e.message);  // "Assertion failed: This will fail"
}

// Function validation
function divide(a, b) {
    assert(b !== 0, "Division by zero not allowed");
    return a / b;
}

// Parameter validation
function setSpeed(speed) {
    assert(speed >= 0, "Speed must be non-negative");
    assert(speed <= 100, "Speed must not exceed 100");

    console.log("Speed set to:", speed);
}

// Array bounds checking
function getArrayElement(arr, index) {
    assert(Array.isArray(arr), "First argument must be an array");
    assert(index >= 0, "Index must be non-negative");
    assert(index < arr.length, "Index out of bounds");

    return arr[index];
}
```

### `require(moduleName)`

Provides access to global objects/modules by name. A simplified module system.

**Parameters:**

| Parameter    | Type     | Description                            |
| ------------ | -------- | -------------------------------------- |
| `moduleName` | `string` | Name of the module/object to access    |

**Returns:** The requested global object

**Example:**

```javascript
// Access modules dynamically
const displayModule = require("display");
const storageModule = require("storage");

// Conditional module loading
function loadModule(name) {
    try {
        return require(name);
    } catch (e) {
        console.log("Failed to load module:", name);
        return null;
    }
}

// Module registry pattern
var modules = {};
var moduleNames = ["display", "storage", "gpio"];
for (var i = 0; i < moduleNames.length; i++) {
    modules[moduleNames[i]] = loadModule(moduleNames[i]);
}

// Plugin-style loading
function loadPlugin(pluginName) {
    const plugin = require(pluginName);

    if (plugin && typeof plugin.init === 'function') {
        plugin.init();
        console.log("Plugin loaded:", pluginName);
    } else {
        console.log("Invalid plugin:", pluginName);
    }
}
```

### `exit([code])`

Terminates the current script execution. Available via `runtime.exit()` as well.

**Parameters:**

| Parameter | Type               | Description               |
| --------- | ------------------ | ------------------------- |
| `code`    | `number, optional` | Exit code (default: 0)    |

**Returns:** Does not return

**Example:**

```javascript
// Simple exit
function emergencyShutdown() {
    console.log("Emergency shutdown initiated!");
    exit(1);
}

// Conditional exit
function checkSystemHealth() {
    const batteryLevel = getBatteryLevel(); // Hypothetical function

    if (batteryLevel < 5) {
        console.log("Critical battery level, shutting down");
        exit(2);
    }

    console.log("System health OK");
}

// Graceful exit with cleanup
function gracefulExit() {
    console.log("Performing cleanup...");

    // Save important data
    storage.write("last_run.txt", "Script ended at " + Date.now());

    // Turn off peripherals
    gpio.write(2, false); // Turn off LED

    console.log("Cleanup complete, exiting");
    exit(0);
}
```

## Global Constants

These constants are automatically available in the global scope:

### Device Information

- `BRUCE_VERSION` (string): Current Bruce firmware version
- `BRUCE_PRICOLOR` (number): Primary UI color
- `BRUCE_SECCOLOR` (number): Secondary UI color
- `BRUCE_BGCOLOR` (number): Background color

### GPIO Constants

- `HIGH` (number): High logic level (1)
- `LOW` (number): Low logic level (0)
- `INPUT` (number): GPIO input mode
- `OUTPUT` (number): GPIO output mode
- `PULLUP` (number): Pull-up resistor mode
- `INPUT_PULLUP` (number): Input with pull-up
- `PULLDOWN` (number): Pull-down resistor mode
- `INPUT_PULLDOWN` (number): Input with pull-down

### File Path Constants

- `__filepath` (string): Full path to current script file
- `__dirpath` (string): Directory path of current script

**Example:**

```javascript
// Using device constants
console.log("Running Bruce firmware version:", BRUCE_VERSION);
console.log("Primary color:", BRUCE_PRICOLOR);

// Using GPIO constants
gpio.mode(2, OUTPUT);
gpio.write(2, HIGH);
delay(1000);
gpio.write(2, LOW);

// Using path constants
console.log("Script file:", __filepath);
console.log("Script directory:", __dirpath);

// Load config file relative to script
var storage = require("storage");
const configPath = __dirpath + "/config.json";
try {
    const config = JSON.parse(storage.read(configPath));
    console.log("Loaded config:", config);
} catch (error) {
    console.log("Config file not found or invalid");
}
```

## Utility Applications

### Timing and Benchmarking

```javascript
// Uses global functions: now(), console.log(), random()
function benchmarkFunction(func, iterations = 1000) {
    console.log("Benchmarking function with", iterations, "iterations...");

    const start = now();

    for (let i = 0; i < iterations; i++) {
        func();
    }

    const end = now();
    const total = end - start;
    const average = total / iterations;

    console.log("Total time:", total, "ms");
    console.log("Average time:", average.toFixed(3), "ms per call");
    console.log("Calls per second:", (1000 / average).toFixed(0));

    return { total, average, callsPerSecond: 1000 / average };
}

// Example usage
const results = benchmarkFunction(() => {
    Math.sqrt(random(1000));
}, 5000);
```

### Random Data Generation

```javascript
// Uses global functions: random(), now()
function generateRandomData() {
    const data = {
        id: random(1000000),
        timestamp: now(),
        temperature: random(20, 40),
        humidity: random(30, 80),
        pressure: random(900, 1100),
        active: randomBool()
    };

    return data;
}

function randomBool() {
    return random(2) === 1;
}

function randomString(length) {
    const chars = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789";
    let result = "";

    for (let i = 0; i < length; i++) {
        result += chars[random(chars.length)];
    }

    return result;
}

// Generate test data
console.log("Random sensor data:", generateRandomData());
console.log("Random ID:", randomString(8));
```

### String Processing

```javascript
function formatText(text, options = {}) {
    let result = toString(text);

    if (options.uppercase) {
        result = toUpperCase(result);
    } else if (options.lowercase) {
        result = toLowerCase(result);
    }

    if (options.prefix) {
        result = toString(options.prefix) + result;
    }

    if (options.suffix) {
        result = result + toString(options.suffix);
    }

    return result;
}

// Usage examples
console.log(formatText("hello", { uppercase: true }));
console.log(formatText("WORLD", { lowercase: true, prefix: ">> " }));
console.log(formatText("status", {
    uppercase: true,
    prefix: "[",
    suffix: "]"
}));
```

### Validation and Testing

```javascript
function validate(value, rules) {
    try {
        if (rules.required) {
            assert(value !== null && value !== undefined, "Value is required");
        }

        if (rules.type) {
            assert(typeof value === rules.type, "Invalid type");
        }

        if (rules.min !== undefined) {
            assert(value >= rules.min, "Value below minimum");
        }

        if (rules.max !== undefined) {
            assert(value <= rules.max, "Value above maximum");
        }

        return { valid: true };
    } catch (e) {
        return { valid: false, error: e.message };
    }
}

// Usage
const userAge = parseInt(keyboard.keyboard("Enter age:", "25"));
const validation = validate(userAge, {
    required: true,
    type: "number",
    min: 0,
    max: 150
});

if (validation.valid) {
    console.log("Valid age:", userAge);
} else {
    console.log("Validation error:", validation.error);
}
```
