# Infrared

The Infrared module provides infrared signal transmission and reception functionality for remote control and communication.

## Signal Transmission

### `ir.transmitFile(filename, hideUI)`

Transmits IR commands from a file.

**Parameters:**

| Parameter | Type                | Description                               |
| --------- | ------------------- | ----------------------------------------- |
| `filename`| `string`            | Path to IR file containing signal data    |
| `hideUI`  | `boolean, optional` | Hide user interface during transmission   |

**Returns:** `boolean` - Success status

**Example:**

```javascript linenums="1"
var ir = require("ir");
// Transmit IR commands from file
if (ir.transmitFile("/ir/tv_power.ir")) {
    console.log("IR signal transmitted successfully");
} else {
    console.log("IR transmission failed");
}

// Transmit without showing UI
ir.transmitFile("/ir/ac_on.ir", true);
```

### `ir.transmit(data, protocol, bits)`

Transmits IR data with specified protocol.

**Parameters:**

| Parameter  | Type                    | Description                      |
| ---------- | ----------------------- | -------------------------------- |
| `data`     | `number/string`         | IR data to transmit (hex value)  |
| `protocol` | `string, default: "NEC"`| IR protocol to use               |
| `bits`     | `number, default: 32`   | Number of data bits              |

**Returns:** `boolean` - Success status

**Example:**

```javascript linenums="1"
var ir = require("ir");

// Transmit NEC protocol signal
ir.transmit(0x00FF00FF, "NEC", 32);

// Transmit Sony protocol signal
ir.transmit(0x12345, "SONY", 20);

// Transmit raw data
ir.transmit("0xFF00FF00", "RAW");
```

## Signal Reception

### `ir.read(timeout)`

Captures IR signal for specified timeout.

**Parameters:**

| Parameter | Type                  | Description         |
| --------- | --------------------- | ------------------- |
| `timeout` | `number, default: 10` | Timeout in seconds  |

**Returns:** `string` - Generated IR file content, or empty string if no signal

**Example:**

```javascript linenums="1"
var ir = require("ir");
var storage = require("storage");
var dialog = require("dialog");

console.log("Point remote at device and press button...");
var signal = ir.read(15); // Wait up to 15 seconds

if (signal) {
    console.log("IR signal captured!");

    // Save captured signal
    storage.write("/ir/captured.ir", signal);
    dialog.success("Signal saved to /ir/captured.ir");
} else {
    dialog.info("No IR signal detected");
}
```

### `ir.readRaw(timeout)`

Captures raw IR signal data.

**Parameters:**

| Parameter | Type                  | Description         |
| --------- | --------------------- | ------------------- |
| `timeout` | `number, default: 10` | Timeout in seconds  |

**Returns:** `string` - Raw signal data, or empty string if no signal

**Example:**

```javascript linenums="1"
var ir = require("ir");
var storage = require("storage");

console.log("Capturing raw IR data...");
var rawData = ir.readRaw(20);

if (rawData) {
    console.log("Raw IR data:", rawData);
    storage.write("/ir/raw_capture.txt", rawData);
} else {
    console.log("No raw IR data captured");
}
```

## Supported IR Protocols

| Protocol  | Description           | Typical Use            |
| --------- | --------------------- | ---------------------- |
| NEC       | Most common protocol  | TVs, audio equipment   |
| Sony      | Sony's SIRC protocol  | Sony devices           |
| RC5       | Philips RC5 protocol  | European equipment     |
| RC6       | Philips RC6 protocol  | Modern remotes         |
| Samsung   | Samsung protocol      | Samsung devices        |
| LG        | LG protocol           | LG devices             |
| Panasonic | Panasonic protocol    | Panasonic devices      |
| RAW       | Raw timing data       | Custom/unknown signals |

## Usage Examples

```javascript linenums="1"
var ir = require("ir");
var storage = require("storage");
var dialog = require("dialog");

// IR Remote Control Cloner
function irRemoteCloner() {
    var buttons = [];

    dialog.info("IR Remote Cloner\\nCapture buttons one by one");

    while (true) {
        var buttonName = dialog.prompt("Button name (" + (buttons.length + 1) + "):");
        if (!buttonName) break;

        dialog.info("Point remote at device and press '" + buttonName + "' button");

        var signal = ir.read(15);
        if (signal) {
            buttons.push({
                name: buttonName,
                data: signal
            });

            dialog.success("'" + buttonName + "' captured!");

            // Save individual button
            storage.write("/ir/remote_" + buttonName.toLowerCase() + ".ir", signal);

        } else {
            dialog.warning("No signal captured for '" + buttonName + "'");
        }

        const choice = dialog.choice(["Add Another Button", "Finish", "Test Last Button"]);

        if (choice === 1) break; // Finish
        if (choice === 2 && buttons.length > 0) {
            // Test last button
            var lastButton = buttons[buttons.length - 1];
            dialog.info("Testing '" + lastButton.name + "'...");

            storage.write("/tmp_test.ir", lastButton.data);
            ir.transmitFile("/tmp_test.ir");
            storage.remove("/tmp_test.ir");
        }
    }

    if (buttons.length > 0) {
        // Create master remote file
        var remoteData = "# Cloned Remote\\n";
        for (var i = 0; i < buttons.length; i++) {
            var btn = buttons[i];
            remoteData += "Button " + (i + 1) + ": " + btn.name + "\\n";
            remoteData += btn.data + "\\n";
        }

        var remoteName = dialog.prompt("Remote name:", "MyRemote");
        if (remoteName) {
            storage.write("/ir/" + remoteName + ".remote", remoteData);
            dialog.success("Remote saved with " + buttons.length + " buttons");
        }
    }
}

// IR Signal Analyzer
function analyzeIRSignal() {
    var ir = require("ir");
    var storage = require("storage");
    var dialog = require("dialog");

    dialog.info("IR Signal Analyzer\\nPoint remote and press button");

    var signal = ir.read(20);
    if (!signal) {
        dialog.info("No signal detected");
        return;
    }

    // Parse signal information
    var lines = signal.split('\\n');
    var protocol = "Unknown";
    var frequency = "Unknown";
    var data = "Unknown";

    for (var i = 0; i < lines.length; i++) {
        var line = lines[i];
        if (line.indexOf('protocol:') === 0) {
            protocol = line.split(':')[1].replace(/^\s+|\s+$/g, '');
        } else if (line.indexOf('frequency:') === 0) {
            frequency = line.split(':')[1].replace(/^\s+|\s+$/g, '');
        } else if (line.indexOf('data:') === 0) {
            data = line.split(':')[1].replace(/^\s+|\s+$/g, '');
        }
    }

    var analysis = "IR Signal Analysis:\n\nProtocol: " + protocol + "\nFrequency: " + frequency + "\nData: " + data + "\nSize: " + signal.length + " bytes\n\nRaw Data:\n" + signal;

    dialog.viewText(analysis, "IR Analysis");

    const action = dialog.choice([
        "Save Analysis",
        "Test Signal",
        "Close"
    ]);

    switch (action) {
        case 0:
            var filename = "/ir/analysis_" + now() + ".txt";
            storage.write(filename, analysis);
            dialog.success("Analysis saved to " + filename);
            break;

        case 1:
            dialog.info("Testing captured signal...");
            storage.write("/tmp_test.ir", signal);
            ir.transmitFile("/tmp_test.ir");
            storage.remove("/tmp_test.ir");
            break;
    }
}

// IR Remote Library Manager
function IRRemoteLibrary() {
    var storage = require("storage");
    this.remotes = {};
    this.storage = storage;
    this.loadLibrary();
}

IRRemoteLibrary.prototype.loadLibrary = function() {
    try {
        var data = this.storage.read("/ir/library.json");
        this.remotes = JSON.parse(data);
    } catch (error) {
        this.remotes = {};
    }
};

IRRemoteLibrary.prototype.saveLibrary = function() {
    this.storage.write("/ir/library.json", JSON.stringify(this.remotes, null, 2));
};

IRRemoteLibrary.prototype.addRemote = function(name, buttons) {
    this.remotes[name] = {
        name: name,
        buttons: buttons,
        created: now()
    };
    this.saveLibrary();
};

IRRemoteLibrary.prototype.getRemote = function(name) {
    return this.remotes[name] || null;
};

IRRemoteLibrary.prototype.listRemotes = function() {
    return Object.keys(this.remotes);
};

IRRemoteLibrary.prototype.deleteRemote = function(name) {
    if (this.remotes[name]) {
        delete this.remotes[name];
        this.saveLibrary();
        return true;
    }
    return false;
};

IRRemoteLibrary.prototype.transmitButton = function(remoteName, buttonName) {
    var remote = this.remotes[remoteName];
    if (!remote) return false;

    var button = null;
    for (var i = 0; i < remote.buttons.length; i++) {
        if (remote.buttons[i].name === buttonName) {
            button = remote.buttons[i];
            break;
        }
    }
    if (!button) return false;

    // Create temporary file and transmit
    var ir = require("ir");
    this.storage.write("/tmp_ir.ir", button.data);
    var success = ir.transmitFile("/tmp_ir.ir", true);
    this.storage.remove("/tmp_ir.ir");

    return success;
};

// IR Remote Control Interface
function irRemoteControl() {
    var dialog = require("dialog");
    var library = new IRRemoteLibrary();

    while (true) {
        var remotes = library.listRemotes();

        if (remotes.length === 0) {
            dialog.info("No remotes in library");
            return;
        }

        var remoteChoice = dialog.choice(remotes.concat(["Exit"]));
        if (remoteChoice === remotes.length) break;

        var selectedRemote = remotes[remoteChoice];
        var remote = library.getRemote(selectedRemote);

        while (true) {
            var buttonNames = [];
            for (var i = 0; i < remote.buttons.length; i++) {
                buttonNames.push(remote.buttons[i].name);
            }
            var choice = dialog.choice(buttonNames.concat(["Back"]));

            if (choice === buttonNames.length) break;

            var buttonName = buttonNames[choice];

            if (library.transmitButton(selectedRemote, buttonName)) {
                dialog.success(buttonName + " transmitted");
            } else {
                dialog.error("Failed to transmit " + buttonName);
            }

            delay(500); // Prevent rapid firing
        }
    }
}

// Universal Remote Finder
function findUniversalCodes() {
    var ir = require("ir");
    var storage = require("storage");
    var dialog = require("dialog");

    var deviceType = dialog.choice([
        "TV",
        "Air Conditioner",
        "Audio System",
        "DVD Player",
        "Cable Box"
    ]);

    if (deviceType === -1) return;

    var deviceTypes = ["TV", "AC", "Audio", "DVD", "Cable"];
    var selectedType = deviceTypes[deviceType];

    // Common codes for different device types
    var commonCodes = {
        "TV": [
            { name: "Power", data: "0x00FF807F", protocol: "NEC" },
            { name: "Vol+", data: "0x00FF40BF", protocol: "NEC" },
            { name: "Vol-", data: "0x00FFC03F", protocol: "NEC" },
            { name: "Ch+", data: "0x00FF20DF", protocol: "NEC" },
            { name: "Ch-", data: "0x00FFA05F", protocol: "NEC" }
        ],
        "AC": [
            { name: "Power", data: "0x12345678", protocol: "Samsung" },
            { name: "Temp+", data: "0x23456789", protocol: "Samsung" },
            { name: "Temp-", data: "0x3456789A", protocol: "Samsung" }
        ]
        // Add more device types as needed
    };

    var codes = commonCodes[selectedType] || [];

    if (codes.length === 0) {
        dialog.info("No common codes available for " + selectedType);
        return;
    }

    dialog.info("Testing common codes for " + selectedType + "\\nWatch your device for response");

    for (var i = 0; i < codes.length; i++) {
        var code = codes[i];
        var test = dialog.choice([
            "Test " + code.name,
            "Skip",
            "Exit"
        ]);

        if (test === 2) break;
        if (test === 0) {
            // Convert to IR file format and test
            var irData = "# " + selectedType + " " + code.name + "\nprotocol: " + code.protocol + "\ndata: " + code.data + "\nfrequency: 38000";

            storage.write("/tmp_test.ir", irData);
            ir.transmitFile("/tmp_test.ir", true);
            storage.remove("/tmp_test.ir");

            var worked = dialog.choice([
                "It Worked!",
                "No Response",
                "Exit"
            ]);

            if (worked === 0) {
                var save = dialog.message("Save this code?", {
                    left: "No",
                    right: "Yes"
                });

                if (save === "Yes") {
                    var filename = "/ir/" + selectedType + "_" + code.name + ".ir";
                    storage.write(filename, irData);
                    dialog.success("Code saved to " + filename);
                }
                break;
            } else if (worked === 2) {
                break;
            }
        }
    }
}

// IR Signal Database
function irSignalDatabase() {
    var dialog = require("dialog");

    while (true) {
        const action = dialog.choice([
            "Capture New Signal",
            "View Saved Signals",
            "Organize Signals",
            "Import/Export",
            "Exit"
        ]);

        switch (action) {
            case 0: // Capture
                analyzeIRSignal();
                break;

            case 1: // View
                viewSavedSignals();
                break;

            case 2: // Organize
                organizeSignals();
                break;

            case 3: // Import/Export
                importExportSignals();
                break;

            case 4: // Exit
            default:
                return;
        }
    }
}

function viewSavedSignals() {
    var ir = require("ir");
    var storage = require("storage");
    var dialog = require("dialog");

    var files = storage.readdir("/ir");
    var irFiles = [];
    for (var i = 0; i < files.length; i++) {
        if (!files[i].isDirectory && files[i].name.indexOf('.ir') === files[i].name.length - 3) {
            irFiles.push(files[i]);
        }
    }

    if (irFiles.length === 0) {
        dialog.info("No IR signals found");
        return;
    }

    var fileNames = [];
    for (var i = 0; i < irFiles.length; i++) {
        fileNames.push(irFiles[i].name);
    }
    var fileChoice = dialog.choice(fileNames.concat(["Back"]));
    if (fileChoice === irFiles.length) return;

    var filename = "/ir/" + irFiles[fileChoice].name;

    var action = dialog.choice([
        "View Content",
        "Test Signal",
        "Delete Signal",
        "Back"
    ]);

    switch (action) {
        case 0:
            dialog.viewFile(filename, irFiles[fileChoice].name);
            break;

        case 1:
            if (ir.transmitFile(filename)) {
                dialog.success("Signal transmitted");
            } else {
                dialog.error("Transmission failed");
            }
            break;

        case 2:
            var confirm = dialog.message("Delete " + irFiles[fileChoice].name + "?", {
                left: "Cancel",
                right: "Delete"
            });

            if (confirm === "Delete") {
                if (storage.remove(filename)) {
                    dialog.success("Signal deleted");
                } else {
                    dialog.error("Delete failed");
                }
            }
            break;
    }
}

function organizeSignals() {
    var storage = require("storage");
    var dialog = require("dialog");

    var files = storage.readdir("/ir");
    var irFiles = [];
    for (var i = 0; i < files.length; i++) {
        if (!files[i].isDirectory && files[i].name.indexOf('.ir') === files[i].name.length - 3) {
            irFiles.push(files[i]);
        }
    }

    // Group by device type based on filename
    var groups = {};

    for (var i = 0; i < irFiles.length; i++) {
        var file = irFiles[i];
        var name = file.name.toLowerCase();
        var category = "other";

        if (name.indexOf("tv") !== -1) category = "tv";
        else if (name.indexOf("ac") !== -1 || name.indexOf("air") !== -1) category = "ac";
        else if (name.indexOf("audio") !== -1 || name.indexOf("stereo") !== -1) category = "audio";
        else if (name.indexOf("fan") !== -1) category = "fan";

        if (!groups[category]) groups[category] = [];
        groups[category].push(file.name);
    }

    var report = "IR Signal Organization:\\n\\n";

    var categories = Object.keys(groups);
    for (var i = 0; i < categories.length; i++) {
        var category = categories[i];
        report += category.toUpperCase() + ":\\n";
        for (var j = 0; j < groups[category].length; j++) {
            report += "  - " + groups[category][j] + "\\n";
        }
        report += "\\n";
    }

    dialog.viewText(report, "Signal Organization");
}
```

## IR File Format

IR files typically contain:

```text
# Comment line
protocol: NEC
data: 0x00FF807F
frequency: 38000
bits: 32
```

For raw signals:

```text
# Raw IR timing data
protocol: RAW
frequency: 38000
raw_data: +8900,-4450,+550,-550,+550,-1650,...
```
