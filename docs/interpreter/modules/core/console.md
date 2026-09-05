# Console

The Console module provides standard console output functionality for debugging and logging, similar to browser console APIs.

## Console Object

### `console.log(...args)`

Outputs messages to both the serial console and display (depending on configuration). The primary logging function for debugging.

**Parameters:**

| Parameter | Type   | Description                     |
| --------- | ------ | ------------------------------- |
| `...args` | `any`  | Any number of values to output  |

**Returns:** None

**Example:**

```javascript linenums="1"
// Basic logging
console.log("Hello, World!");

// Multiple arguments
console.log("Counter:", 42, "Status:", true);

// Objects and arrays
var user = { name: "Alice", age: 30 };
var numbers = [1, 2, 3, 4, 5];
console.log("User:", user);
console.log("Numbers:", numbers);

// Variables and expressions
var x = 10, y = 20;
console.log("Sum:", x + y);
console.log("Result:", x * y > 100 ? "Large" : "Small");
```

## Global Print Functions

### `print(...args)`

Outputs values without a newline. Useful for building output line by line.

**Parameters:**

| Parameter | Type   | Description                     |
| --------- | ------ | ------------------------------- |
| `...args` | `any`  | Any number of values to output  |

**Returns:** None

**Example:**

```javascript linenums="1"
// Single line output
print("Loading");
print(".");
print(".");
print(".");
print(" Complete!");

// Building formatted output
var progress = 75;
print("Progress: [");
for (var i = 0; i < 20; i++) {
    print(i < progress / 5 ? "█" : "░");
}
print("] " + progress + "%");
```

### `println(...args)`

Outputs values followed by a newline. Equivalent to `console.log()`.

**Parameters:**

| Parameter | Type   | Description                     |
| --------- | ------ | ------------------------------- |
| `...args` | `any`  | Any number of values to output  |

**Returns:** None

**Example:**

```javascript linenums="1"
// Line-by-line output
println("System Status:");
println("CPU: OK");
println("Memory: OK");
println("Storage: OK");

// Formatted logging
var timestamp = Date.now();
println("[" + timestamp + "] Application started");
```

## Console Applications


### System Monitor

```javascript
var display = require("display");
var keyboard = require("keyboard");

function systemMonitor() {
    var startTime = Date.now();
    var loopCount = 0;

    console.log("=== System Monitor Started ===");
    console.log("Start Time:", Date.now());

    while (true) {
        if (keyboard.getEscPress()) break;

        loopCount++;
        var uptime = Date.now() - startTime;

        // Display status line
        display.println("Uptime: " + formatUptime(uptime));
        display.println(" | Loops: " + loopCount);
        display.println(" | Avg: " + (uptime / loopCount).toFixed(1) + "ms");
        display.println(" | Press Back to exit");

        delay(100);
    }

    console.log("=== System Monitor Stopped ===");
    console.log("Total uptime:", formatUptime(Date.now() - startTime));
    console.log("Total loops:", loopCount);
}

function formatUptime(ms) {
    var seconds = Math.floor(ms / 1000);
    var minutes = Math.floor(seconds / 60);
    var hours = Math.floor(minutes / 60);

    if (hours > 0) {
        return hours + "h " + (minutes % 60) + "m " + (seconds % 60) + "s";
    } else if (minutes > 0) {
        return minutes + "m " + (seconds % 60) + "s";
    } else {
        return seconds + "s";
    }
}

// Start monitoring
systemMonitor();
```

### Data Table Formatter

```javascript linenums="1"
function TableFormatter(headers) {
    this.headers = headers;
    this.rows = [];
    this.columnWidths = [];
    for (var i = 0; i < headers.length; i++) {
        this.columnWidths.push(headers[i].length);
    }
}

TableFormatter.prototype.addRow = function(row) {
    if (row.length !== this.headers.length) {
        throw new Error("Row length doesn't match headers");
    }

    var stringRow = [];
    for (var i = 0; i < row.length; i++) {
        stringRow.push(String(row[i]));
    }
    this.rows.push(stringRow);

    // Update column widths
    for (var i = 0; i < row.length; i++) {
        this.columnWidths[i] = Math.max(
            this.columnWidths[i],
            String(row[i]).length
        );
    }
};

TableFormatter.prototype.print = function() {
    var separator = "+";
    for (var i = 0; i < this.columnWidths.length; i++) {
        var dashes = "";
        for (var j = 0; j < this.columnWidths[i] + 2; j++) {
            dashes += "-";
        }
        separator += dashes + "+";
    }

    // Top border
    console.log(separator);

    // Headers
    this.printRow(this.headers);
    console.log(separator);

    // Data rows
    for (var i = 0; i < this.rows.length; i++) {
        this.printRow(this.rows[i]);
    }

    // Bottom border
    console.log(separator);
};

TableFormatter.prototype.printRow = function(row) {
    var line = "|";
    for (var i = 0; i < row.length; i++) {
        var cell = String(row[i]);
        var padding = "";
        for (var j = 0; j < this.columnWidths[i] - cell.length; j++) {
            padding += " ";
        }
        line += " " + cell + padding + " |";
    }
    console.log(line);
};

// Usage example
function networkStatus() {
    const table = new TableFormatter(["Interface", "Status", "IP", "Signal"]);

    table.addRow(["WiFi", "Connected", "192.168.1.100", "-45 dBm"]);
    table.addRow(["Bluetooth", "Disconnected", "-", "N/A"]);
    table.addRow(["USB", "Connected", "10.0.0.1", "N/A"]);

    console.log("Network Interface Status:");
    table.print();
}

networkStatus();
```


### Log Analysis

```javascript
function logAnalyzer(logData) {
    var lines = logData.split("\n");

    var analysis = {
        totalLines: lines.length,
        errorCount: 0,
        warningCount: 0,
        infoCount: 0,
        debugCount: 0,
        patterns: {}
    };

    console.log("Analyzing log data...");

    var i, j, line, matches, match;

    for (i = 0; i < lines.length; i++) {
        line = lines[i];

        /* Manual trim check (no String.trim) */
        if (!line || line.replace(/\s/g, "") === "") {
            continue;
        }

        /* Log level counting (no includes) */
        if (line.indexOf("[ERROR]") !== -1) {
            analysis.errorCount++;
        } else if (line.indexOf("[WARN]") !== -1) {
            analysis.warningCount++;
        } else if (line.indexOf("[INFO]") !== -1) {
            analysis.infoCount++;
        } else if (line.indexOf("[DEBUG]") !== -1) {
            analysis.debugCount++;
        }

        /* Pattern matching */
        matches = line.match(/\b\w+Exception\b|\b\w+Error\b/g);
        if (matches) {
            for (j = 0; j < matches.length; j++) {
                match = matches[j];
                if (analysis.patterns[match]) {
                    analysis.patterns[match]++;
                } else {
                    analysis.patterns[match] = 1;
                }
            }
        }
    }

    /* Display results */
    console.log("\n=== Log Analysis Results ===");
    console.log("Total lines:", analysis.totalLines);
    console.log("Errors:", analysis.errorCount);
    console.log("Warnings:", analysis.warningCount);
    console.log("Info:", analysis.infoCount);
    console.log("Debug:", analysis.debugCount);

    /* Check if patterns exist (no Object.keys) */
    var hasPatterns = false;
    for (match in analysis.patterns) {
        hasPatterns = true;
        break;
    }

    if (hasPatterns) {
        console.log("\nError patterns:");
        for (match in analysis.patterns) {
            console.log("  " + match + ":", analysis.patterns[match]);
        }
    }

    /* Health score */
    var total = analysis.errorCount + analysis.warningCount + analysis.infoCount;
    var healthScore = 100;

    if (total > 0) {
        healthScore = Math.round((1 - (analysis.errorCount / total)) * 100);
    }

    console.log("\nSystem health score:", healthScore + "%");

    return analysis;
}

var sampleLog =
"[2024-01-30T10:00:00Z] [INFO] System started\n" +
"[2024-01-30T10:00:01Z] [DEBUG] Loading modules\n" +
"[2024-01-30T10:00:02Z] [WARN] Low battery detected\n" +
"[2024-01-30T10:00:03Z] [ERROR] ConnectionException: Failed to connect\n" +
"[2024-01-30T10:00:04Z] [INFO] Retrying connection\n" +
"[2024-01-30T10:00:05Z] [ERROR] TimeoutError: Request timed out";

var analysis = logAnalyzer(sampleLog);
```

## Output Formatting

### Formatting Utilities

```javascript
/* String formatting helpers */

function pad(str, length, char) {
    if (char === undefined) {
        char = " ";
    }

    str = String(str);

    while (str.length < length) {
        str = char + str;
    }

    return str;
}

function formatBytes(bytes) {
    var units = ["B", "KB", "MB", "GB"];
    var size = bytes;
    var unitIndex = 0;

    while (size >= 1024 && unitIndex < units.length - 1) {
        size = size / 1024;
        unitIndex++;
    }

    return size.toFixed(1) + " " + units[unitIndex];
}

function formatDuration(ms) {
    if (ms < 1000) {
        return ms + "ms";
    }

    if (ms < 60000) {
        return (ms / 1000).toFixed(1) + "s";
    }

    if (ms < 3600000) {
        return Math.floor(ms / 60000) + "m " +
               Math.floor((ms % 60000) / 1000) + "s";
    }

    var hours = Math.floor(ms / 3600000);
    var minutes = Math.floor((ms % 3600000) / 60000);

    return hours + "h " + minutes + "m";
}

/* Usage in console output */

function systemReport() {
    var now = Date.now();

    console.log("=== System Report ===");
    console.log("Uptime:", formatDuration(now));
    console.log("Memory:", formatBytes(512000), "used of", formatBytes(1048576));
    console.log("CPU:", pad("85%", 6), "load");
    console.log("Temperature:", pad("42°C", 6));
}

systemReport();
```
