# Dialog

The Dialog module provides user interface dialogs and file operations for user interaction.

## Message Dialogs

### `dialog.message(msg, buttons)`

Shows a message dialog with customizable buttons.

**Parameters:**

| Parameter | Type     | Description                                                              |
| --------- | -------- | ------------------------------------------------------------------------ |
| `msg`     | `string` | Message text to display                                                  |
| `buttons` | `object` | Button configuration with optional `left`, `center`, `right` properties  |

**Returns:** `string` - Name of the pressed button

**Example:**

```javascript
var dialog = require("dialog");

const result = dialog.message("Save changes?", {
    left: "Cancel",
    center: "Don't Save",
    right: "Save"
});

if (result === "Save") {
    // Save the file
    console.log("Saving...");
} else if (result === "Cancel") {
    console.log("Operation cancelled");
}
```

### `dialog.info(msg, wait)`

Shows an information dialog.

**Parameters:**

| Parameter | Type                | Description                      |
| --------- | ------------------- | -------------------------------- |
| `msg`     | `string`            | Information message              |
| `wait`    | `boolean, optional` | Whether to wait for user input   |

**Returns:** `undefined`

**Example:**

```javascript
var dialog = require("dialog");

dialog.info("Operation completed successfully");
dialog.info("Processing...", false); // Non-blocking
```

### `dialog.success(msg, wait)`

Shows a success dialog with success styling.

**Parameters:**

| Parameter | Type                | Description                      |
| --------- | ------------------- | -------------------------------- |
| `msg`     | `string`            | Success message                  |
| `wait`    | `boolean, optional` | Whether to wait for user input   |

**Returns:** `undefined`

**Example:**

```javascript
var dialog = require("dialog");

dialog.success("File saved successfully!");
```

### `dialog.warning(msg, wait)`

Shows a warning dialog with warning styling.

**Parameters:**

| Parameter | Type                | Description                      |
| --------- | ------------------- | -------------------------------- |
| `msg`     | `string`            | Warning message                  |
| `wait`    | `boolean, optional` | Whether to wait for user input   |

**Returns:** `undefined`

**Example:**

```javascript
var dialog = require("dialog");

dialog.warning("Low battery detected");
```

### `dialog.error(msg, wait)`

Shows an error dialog with error styling.

**Parameters:**

| Parameter | Type                | Description                      |
| --------- | ------------------- | -------------------------------- |
| `msg`     | `string`            | Error message                    |
| `wait`    | `boolean, optional` | Whether to wait for user input   |

**Returns:** `undefined`

**Example:**

```javascript
var dialog = require("dialog");

dialog.error("Failed to connect to WiFi");
```

## Selection Dialogs

### `dialog.choice(options)`

Shows a multi-choice selection dialog.

**Parameters:**

| Parameter | Type    | Description               |
| --------- | ------- | ------------------------- |
| `options` | `array` | Array of choice strings   |

**Returns:** `number` - Index of selected option (0-based), or -1 if cancelled

**Example:**

```javascript
var dialog = require("dialog");

const choice = dialog.choice([
    "Read RFID",
    "Write RFID",
    "Clone Tag",
    "Exit"
]);

switch (choice) {
    case 0:
        console.log("Reading RFID...");
        break;
    case 1:
        console.log("Writing RFID...");
        break;
    case 2:
        console.log("Cloning tag...");
        break;
    case 3:
    default:
        console.log("Exiting...");
        break;
}
```

### `dialog.prompt(defaultValue, maxLength, title)`

Text input prompt (uses keyboard module functionality).

**Parameters:**

| Parameter     | Type                | Description                          |
| ------------- | ------------------- | ------------------------------------ |
| `defaultValue`| `string, optional`  | Default input value (default empty)  |
| `maxLength`   | `number, optional`  | Maximum input length                 |
| `title`       | `string, optional`  | Prompt title                         |

**Returns:** `string` - User input text

**Example:**

```javascript
var dialog = require("dialog");

var name = dialog.prompt("Enter your name:", 50, "Bruce");
if (name) {
    console.log("Hello, " + name + "!");
}
```

## File Operations

### `dialog.pickFile(title, filter)`

Shows a file picker dialog.

**Parameters:**

| Parameter | Type               | Description                                 |
| --------- | ------------------ | ------------------------------------------- |
| `title`   | `string`           | Dialog title                                |
| `filter`  | `string, optional` | File extension filter (e.g., ".txt", ".js") |

**Returns:** `string` - Selected file path, or empty string if cancelled

**Example:**

```javascript
var dialog = require("dialog");

const scriptFile = dialog.pickFile("Select JavaScript File", ".js");
if (scriptFile) {
    console.log("Selected:", scriptFile);
    // Load and execute the script
    load(scriptFile);
}

const logFile = dialog.pickFile("Select Log File", ".log");
if (logFile) {
    const content = storage.read(logFile);
    dialog.viewText(content, "Log Contents");
}
```

### `dialog.viewFile(filename, title)`

Displays the contents of a file in a scrollable viewer.

**Parameters:**

| Parameter  | Type               | Description                |
| ---------- | ------------------ | -------------------------- |
| `filename` | `string`           | Path to file to display    |
| `title`    | `string, optional` | Dialog title               |

**Returns:** `undefined`

**Example:**

```javascript
dialog.viewFile("/config.txt", "Configuration File");
dialog.viewFile("/logs/system.log");
```

### `dialog.viewText(text, title)`

Displays text content in a scrollable viewer.

**Parameters:**

| Parameter | Type               | Description                  |
| --------- | ------------------ | ---------------------------- |
| `text`    | `string`           | Text content to display      |
| `title`   | `string, optional` | Dialog title                 |

**Returns:** `undefined`

**Example:**

```javascript
const systemInfo = `Device: ${device.getName()}
Board: ${device.getBoard()}
Battery: ${device.getBatteryCharge()}%
Memory: ${JSON.stringify(device.getFreeHeapSize())}`;

dialog.viewText(systemInfo, "System Information");
```

## TextViewer Class

### `dialog.createTextViewer(title, text)`

Creates a scrollable text viewer instance with advanced functionality.

**Parameters:**

| Parameter | Type     | Description                  |
| --------- | -------- | ---------------------------- |
| `title`   | `string` | Viewer title                 |
| `text`    | `string` | Text content to display      |

**Returns:** `object` - TextViewer instance

**TextViewer Methods:**

#### `viewer.draw()`

Refreshes the display.

#### `viewer.scrollUp()`

Scrolls up one line.

#### `viewer.scrollDown()`

Scrolls down one line.

#### `viewer.scrollToLine(line)`

Scrolls to a specific line number.

**Parameters:**

| Parameter | Type     | Description                  |
| --------- | -------- | ---------------------------- |
| `line`    | `number` | Line number to scroll to     |

#### `viewer.getLine(index)`

Gets the text of a specific line.

**Parameters:**

| Parameter | Type     | Description |
| --------- | -------- | ----------- |
| `index`   | `number` | Line index  |

**Returns:** `string` - Line text

#### `viewer.getMaxLines()`

Returns the total number of lines.

**Returns:** `number` - Total line count

#### `viewer.getVisibleText()`

Returns the currently visible text.

**Returns:** `string` - Visible text

#### `viewer.clear()`

Clears all content.

#### `viewer.close()`

Closes the viewer and frees resources.

**Example:**

```javascript
// Create a log viewer
const logContent = storage.read("/system.log");
const viewer = dialog.createTextViewer("System Log", logContent);

viewer.draw();

// Navigate through content
while (true) {
    if (keyboard.getNextPress(100)) {
        viewer.scrollDown();
        viewer.draw();
    } else if (keyboard.getPrevPress(100)) {
        viewer.scrollUp();
        viewer.draw();
    } else if (keyboard.getEscPress(100)) {
        break;
    }

    delay(50);
}

viewer.close();
```

## Status Bar

### `dialog.drawStatusBar(text)`

Draws a status bar with the specified text.

**Parameters:**

| Parameter | Type     | Description                |
| --------- | -------- | -------------------------- |
| `text`    | `string` | Status text to display     |

**Returns:** `undefined`

**Example:**

```javascript
dialog.drawStatusBar("Processing... 75%");
```

## Usage Examples

```javascript
// Configuration wizard
function configurationWizard() {
    dialog.info("Welcome to the Configuration Wizard");

    // Get device name
    const deviceName = dialog.prompt("Enter device name:", "Bruce");
    if (!deviceName) {
        dialog.error("Configuration cancelled");
        return;
    }

    // Get network settings
    const networkChoice = dialog.choice([
        "Configure WiFi",
        "Use Ethernet",
        "Offline Mode"
    ]);

    let config = {
        deviceName: deviceName,
        networkMode: ["wifi", "ethernet", "offline"][networkChoice]
    };

    if (networkChoice === 0) {
        // WiFi configuration
        const ssid = dialog.prompt("WiFi SSID:");
        const password = dialog.prompt("WiFi Password:");

        config.wifi = { ssid: ssid, password: password };
    }

    // Confirm settings
    const summary = JSON.stringify(config, null, 2);
    dialog.viewText(summary, "Configuration Summary");

    const confirm = dialog.message("Save this configuration?", {
        left: "Cancel",
        right: "Save"
    });

    if (confirm === "Save") {
        storage.write("/config.json", JSON.stringify(config, null, 2));
        dialog.success("Configuration saved!");
        return config;
    } else {
        dialog.warning("Configuration not saved");
        return null;
    }
}

// File manager
function fileManager(directory = "/") {
    while (true) {
        const files = storage.readdir(directory);
        const choices = [".."].concat(files.map(f =>
            f.isDirectory ? `📁 ${f.name}` : `📄 ${f.name}`
        ));

        const selection = dialog.choice(choices);

        if (selection === -1) break; // Cancelled

        if (selection === 0) {
            // Go up one directory
            directory = directory.split('/').slice(0, -1).join('/') || "/";
            continue;
        }

        const selectedFile = files[selection - 1];
        const fullPath = `${directory}/${selectedFile.name}`;

        if (selectedFile.isDirectory) {
            directory = fullPath;
        } else {
            // File selected - show options
            const action = dialog.choice([
                "View File",
                "Delete File",
                "Rename File",
                "Back"
            ]);

            switch (action) {
                case 0: // View
                    try {
                        dialog.viewFile(fullPath, selectedFile.name);
                    } catch (error) {
                        dialog.error(`Cannot view file: ${error.message}`);
                    }
                    break;

                case 1: // Delete
                    const confirm = dialog.message(`Delete ${selectedFile.name}?`, {
                        left: "Cancel",
                        right: "Delete"
                    });

                    if (confirm === "Delete") {
                        if (storage.remove(fullPath)) {
                            dialog.success("File deleted");
                        } else {
                            dialog.error("Delete failed");
                        }
                    }
                    break;

                case 2: // Rename
                    const newName = dialog.prompt("New name:", selectedFile.name);
                    if (newName) {
                        const newPath = `${directory}/${newName}`;
                        if (storage.rename(fullPath, newPath)) {
                            dialog.success("File renamed");
                        } else {
                            dialog.error("Rename failed");
                        }
                    }
                    break;
            }
        }
    }
}

// Interactive log viewer
function interactiveLogViewer() {
    const logFiles = storage.readdir("/logs").filter(f =>
        !f.isDirectory && f.name.endsWith('.log')
    );

    if (logFiles.length === 0) {
        dialog.info("No log files found");
        return;
    }

    const fileChoice = dialog.choice(logFiles.map(f => f.name));
    if (fileChoice === -1) return;

    const selectedFile = `/logs/${logFiles[fileChoice].name}`;

    try {
        const content = storage.read(selectedFile);
        const viewer = dialog.createTextViewer(
            logFiles[fileChoice].name,
            content
        );

        dialog.drawStatusBar("Use ↑/↓ to scroll, ESC to exit");
        viewer.draw();

        while (true) {
            if (keyboard.getNextPress(100)) {
                viewer.scrollDown();
                viewer.draw();
            } else if (keyboard.getPrevPress(100)) {
                viewer.scrollUp();
                viewer.draw();
            } else if (keyboard.getSelPress(100)) {
                // Jump to end
                viewer.scrollToLine(viewer.getMaxLines() - 1);
                viewer.draw();
            } else if (keyboard.getEscPress(100)) {
                break;
            }

            // Update status bar with current position
            const currentLine = viewer.getCurrentLine ? viewer.getCurrentLine() : 0;
            const maxLines = viewer.getMaxLines();
            dialog.drawStatusBar(`Line ${currentLine + 1}/${maxLines} - ↑/↓ scroll, ESC exit`);

            delay(50);
        }

        viewer.close();

    } catch (error) {
        dialog.error(`Cannot read log file: ${error.message}`);
    }
}

// System diagnostics with user interaction
function systemDiagnostics() {
    dialog.info("Running system diagnostics...");

    const results = [];

    // Battery check
    const battery = device.getBatteryCharge();
    results.push(`Battery: ${battery}% ${battery < 20 ? '⚠️ LOW' : '✓'}`);

    // Memory check
    const memory = device.getFreeHeapSize();
    const memoryPercent = ((memory.total - memory.free) / memory.total * 100).toFixed(1);
    results.push(`Memory: ${memoryPercent}% used ${memoryPercent > 80 ? '⚠️ HIGH' : '✓'}`);

    // Storage check
    const storage_space = storage.spaceLittleFS();
    const storagePercent = (storage_space.used / storage_space.total * 100).toFixed(1);
    results.push(`Storage: ${storagePercent}% used ${storagePercent > 90 ? '⚠️ FULL' : '✓'}`);

    // WiFi check
    const wifiStatus = wifi.connected() ? "Connected ✓" : "Disconnected ⚠️";
    results.push(`WiFi: ${wifiStatus}`);

    const report = results.join('\\n');

    dialog.viewText(report, "System Diagnostics");

    const action = dialog.choice([
        "Save Report",
        "Send Report",
        "Close"
    ]);

    switch (action) {
        case 0:
            const timestamp = new Date(now()).toISOString();
            const filename = `/diagnostics_${timestamp}.txt`;
            storage.write(filename, report);
            dialog.success(`Report saved to ${filename}`);
            break;

        case 1:
            dialog.info("Report sending not implemented");
            break;
    }
}
```
