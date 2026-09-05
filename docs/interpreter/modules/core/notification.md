# Notification

The Notification module provides visual feedback through display notifications for user alerts and status updates.

## Functions

### `notification.ledOn()`

Turns on the device LED indicator.

**Parameters:** None

**Returns:** None

**Example:**

```javascript linenums="1"
// Turn on LED for visual indication
notification.ledOn();
```

### `notification.ledOff()`

Turns off the device LED indicator.

**Parameters:** None

**Returns:** None

**Example:**

```javascript linenums="1"
// Turn off LED
notification.ledOff();
```

## LED Control Patterns

### Basic LED Operations

```javascript linenums="1"
var notification = require("notification");
function basicLedDemo() {
    // Simple on/off control
    console.log("LED On");
    notification.ledOn();
    delay(1000);

    console.log("LED Off");
    notification.ledOff();
    delay(1000);
}

function ledBlink(duration) {
    if (duration === undefined) duration = 500;
    notification.ledOn();
    delay(duration);
    notification.ledOff();
    delay(duration);
}

function ledFlash(count, onTime, offTime) {
    if (count === undefined) count = 3;
    if (onTime === undefined) onTime = 200;
    if (offTime === undefined) offTime = 200;

    for (var i = 0; i < count; i++) {
        notification.ledOn();
        delay(onTime);
        notification.ledOff();
        if (i < count - 1) {
            delay(offTime);
        }
    }
}
```

### Status Indication Patterns

```javascript linenums="1"
var notification = require("notification");

function statusIndicator(status) {
    switch (status) {
        case "success":
            // Single long flash
            notification.ledOn();
            delay(1000);
            notification.ledOff();
            break;

        case "error":
            // Three quick flashes
            for (var i = 0; i < 3; i++) {
                notification.ledOn();
                delay(200);
                notification.ledOff();
                delay(200);
            }
            break;

        case "warning":
            // Two medium flashes
            for (var i = 0; i < 2; i++) {
                notification.ledOn();
                delay(500);
                notification.ledOff();
                delay(300);
            }
            break;

        case "info":
            // Gentle pulse
            notification.ledOn();
            delay(300);
            notification.ledOff();
            break;

        case "processing":
            // Rapid flashing for 3 seconds
            var endTime = Date.now() + 3000;
            while (Date.now() < endTime) {
                notification.ledOn();
                delay(100);
                notification.ledOff();
                delay(100);
            }
            break;

        default:
            // Default single flash
            notification.ledOn();
            delay(500);
            notification.ledOff();
    }
}

// Usage examples
statusIndicator("success");   // Operation completed successfully
statusIndicator("error");     // Error occurred
statusIndicator("warning");   // Warning condition
statusIndicator("info");      // Informational message
statusIndicator("processing");// Long operation in progress
```

### Advanced LED Patterns

```javascript linenums="1"
var notification = require("notification");

function morseCode(message) {
    // Morse code patterns (dot = short flash, dash = long flash)
    var morseMap = {
        'A': '.-',   'B': '-...', 'C': '-.-.', 'D': '-..',
        'E': '.',    'F': '..-.', 'G': '--.', 'H': '....',
        'I': '..',   'J': '.---', 'K': '-.-', 'L': '.-..',
        'M': '--',   'N': '-.',   'O': '---', 'P': '.--.',
        'Q': '--.-', 'R': '.-.',  'S': '...', 'T': '-',
        'U': '..-',  'V': '...-', 'W': '.--', 'X': '-..-',
        'Y': '-.--', 'Z': '--..', ' ': '/'
    };

    var dotTime = 200;    // Short flash
    var dashTime = 600;   // Long flash
    var gapTime = 200;    // Gap between dots/dashes
    var letterGap = 600;  // Gap between letters
    var wordGap = 1400;   // Gap between words

    for (var i = 0; i < message.length; i++) {
        var char = message[i].toUpperCase();
        var morse = morseMap[char];

        if (!morse) continue;

        if (morse === '/') {
            // Word gap
            delay(wordGap);
            continue;
        }

        // Send morse pattern for character
        for (let j = 0; j < morse.length; j++) {
            if (morse[j] === '.') {
                // Dot
                notification.ledOn();
                delay(dotTime);
                notification.ledOff();
            } else if (morse[j] === '-') {
                // Dash
                notification.ledOn();
                delay(dashTime);
                notification.ledOff();
            }

            if (j < morse.length - 1) {
                delay(gapTime);
            }
        }

        // Letter gap
        if (i < message.length - 1) {
            delay(letterGap);
        }
    }
}

function heartbeatPattern() {
    // Simulate heartbeat pattern
    for (let i = 0; i < 5; i++) {
        // Double beat
        notification.ledOn();
        delay(100);
        notification.ledOff();
        delay(100);
        notification.ledOn();
        delay(100);
        notification.ledOff();
        delay(800); // Rest period
    }
}

function breathingPattern() {
    // Simulate breathing with gradual intensity changes
    const cycles = 3;

    for (let cycle = 0; cycle < cycles; cycle++) {
        // "Inhale" - increasing intensity simulation
        for (let intensity = 1; intensity <= 10; intensity++) {
            notification.ledOn();
            delay(50 + intensity * 5); // Gradually longer on time
            notification.ledOff();
            delay(100 - intensity * 8); // Gradually shorter off time
        }

        // Brief hold
        notification.ledOn();
        delay(300);
        notification.ledOff();

        // "Exhale" - decreasing intensity simulation
        for (let intensity = 10; intensity >= 1; intensity--) {
            notification.ledOn();
            delay(50 + intensity * 5);
            notification.ledOff();
            delay(100 - intensity * 8);
        }

        // Rest between cycles
        delay(500);
    }
}

function progressIndicator(totalSteps) {
    // Show progress with LED patterns
    const pulsesPerStep = 3;

    for (let step = 1; step <= totalSteps; step++) {
        console.log(`Step ${step}/${totalSteps}`);

        // Flash pattern indicating current step
        for (let pulse = 0; pulse < pulsesPerStep; pulse++) {
            notification.ledOn();
            delay(150);
            notification.ledOff();
            delay(150);
        }

        // Longer pause between steps
        delay(500);
    }

    // Completion signal - long steady on
    notification.ledOn();
    delay(2000);
    notification.ledOff();
}
```

## Notification System

```javascript linenums="1"
var notification = require("notification");
var display = require("display");

function NotificationManager() {
    this.notificationQueue = [];
    this.isProcessing = false;
    this.patterns = {
        urgent: { count: 5, on: 200, off: 200 },
        warning: { count: 3, on: 400, off: 300 },
        info: { count: 1, on: 800, off: 0 },
        success: { count: 2, on: 300, off: 200 },
        error: { count: 4, on: 150, off: 150 }
    };
}

NotificationManager.prototype.addNotification = function(type, message) {
    this.notificationQueue.push({
        type: type,
        message: message,
        timestamp: Date.now()
    });

    if (!this.isProcessing) {
        this.processQueue();
    }
};

NotificationManager.prototype.processQueue = function() {
        if (this.notificationQueue.length === 0) {
            this.isProcessing = false;
            return;
        }

        this.isProcessing = true;
        const notification = this.notificationQueue.shift();

        console.log(`Notification: ${notification.type} - ${notification.message}`);

        // Display message briefly
        display.fill(0);
        display.print(0, 0, notification.type.toUpperCase(), 2);
        display.print(0, 25, notification.message, 1);

        // Execute LED pattern
        const pattern = this.patterns[notification.type] || this.patterns.info;
        this.executePattern(pattern);

        // Continue processing queue
        setTimeout(() => this.processQueue(), 500);
    }

    executePattern(pattern) {
        for (let i = 0; i < pattern.count; i++) {
            notification.ledOn();
            delay(pattern.on);
            notification.ledOff();

            if (i < pattern.count - 1 && pattern.off > 0) {
                delay(pattern.off);
            }
        }
    }

    urgent(message) {
        this.addNotification("urgent", message);
    }

    warning(message) {
        this.addNotification("warning", message);
    }

    info(message) {
        this.addNotification("info", message);
    }

    success(message) {
        this.addNotification("success", message);
    }

    error(message) {
        this.addNotification("error", message);
    }

    clear() {
        this.notificationQueue = [];
    }

    getQueueLength() {
        return this.notificationQueue.length;
    }
}

// Global notification manager instance
const notify = new NotificationManager();

// Usage examples
function demonstrateNotifications() {
    notify.info("System starting up...");
    delay(2000);

    notify.success("WiFi connected");
    delay(2000);

    notify.warning("Battery low");
    delay(2000);

    notify.error("Connection failed");
    delay(2000);

    notify.urgent("SECURITY ALERT!");
    delay(3000);
}
```

### System Status Monitor

```javascript linenums="1"
var dialog = require("dialog");
var display = require("display");

function systemStatusMonitor() {
    var batteryLevel = 100;
    var wifiConnected = false;
    var lastActivity = Date.now();

    while (true) {
        var choice = dialog.choice([
            "Check Status",
            "Simulate Battery Drain",
            "Toggle WiFi",
            "Activity Signal",
            "Run Diagnostics",
            "Exit"
        ]);

        switch (choice) {
            case 0: // Check Status
                displaySystemStatus(batteryLevel, wifiConnected, lastActivity);
                break;

            case 1: // Simulate Battery Drain
                if (batteryLevel > 0) {
                    batteryLevel -= 10;
                    checkBatteryStatus(batteryLevel);
                }
                break;

            case 2: // Toggle WiFi
                wifiConnected = !wifiConnected;
                if (wifiConnected) {
                    notify.success("WiFi Connected");
                } else {
                    notify.warning("WiFi Disconnected");
                }
                break;

            case 3: // Activity Signal
                lastActivity = Date.now();
                notify.info("User Activity");
                break;

            case 4: // Run Diagnostics
                runSystemDiagnostics();
                break;

            case 5: // Exit
                return;
        }

        delay(100);
    }
}

function displaySystemStatus(battery, wifi, activity) {
    const status = `System Status:

Battery: ${battery}%
WiFi: ${wifi ? "Connected" : "Disconnected"}
Last Activity: ${formatTime(activity)}
Uptime: ${formatUptime(Date.now() - activity)}

Notifications Queued: ${notify.getQueueLength()}`;

    dialog.viewText(status, "System Status");
}

function checkBatteryStatus(level) {
    if (level <= 10) {
        notify.urgent(`Critical Battery: ${level}%`);
    } else if (level <= 20) {
        notify.warning(`Low Battery: ${level}%`);
    } else if (level <= 30) {
        notify.info(`Battery: ${level}%`);
    }
}

function runSystemDiagnostics() {
    const tests = [
        "Testing LED...",
        "Checking Display...",
        "Verifying Storage...",
        "Testing GPIO...",
        "Checking Memory..."
    ];

    dialog.info("Running system diagnostics...");

    for (let i = 0; i < tests.length; i++) {
        display.fill(0);
        display.print(0, 0, "Diagnostics", 2);
        display.print(0, 25, tests[i], 1);
        display.print(0, 40, `${i + 1}/${tests.length}`, 1);

        // LED feedback for each test
        statusIndicator("processing");
        delay(1000);

        // Simulate test result (80% pass rate)
        const passed = Math.random() > 0.2;
        if (passed) {
            statusIndicator("success");
        } else {
            statusIndicator("error");
        }

        delay(500);
    }

    notify.success("Diagnostics Complete");
}

function formatTime(timestamp) {
    const date = new Date(timestamp);
    return date.toLocaleTimeString();
}

function formatUptime(ms) {
    const seconds = Math.floor(ms / 1000);
    const minutes = Math.floor(seconds / 60);
    const hours = Math.floor(minutes / 60);

    if (hours > 0) {
        return `${hours}h ${minutes % 60}m`;
    } else if (minutes > 0) {
        return `${minutes}m ${seconds % 60}s`;
    } else {
        return `${seconds}s`;
    }
}
```

### Alert System

```javascript linenums="1"
var dialog = require("dialog");
var keyboard = require("keyboard");
var notification = require("notification");

function createAlertSystem() {
    var alerts = [];

    while (true) {
        var choice = dialog.choice([
            "Add Alert",
            "View Alerts",
            "Test Alert",
            "Clear All",
            "Monitor Mode",
            "Exit"
        ]);

        switch (choice) {
            case 0: // Add Alert
                addAlert(alerts);
                break;

            case 1: // View Alerts
                viewAlerts(alerts);
                break;

            case 2: // Test Alert
                testAlert(alerts);
                break;

            case 3: // Clear All
                alerts.length = 0;
                notify.info("All alerts cleared");
                break;

            case 4: // Monitor Mode
                monitorAlerts(alerts);
                break;

            case 5: // Exit
                return;
        }
    }
}

function addAlert(alerts) {
    const name = keyboard.keyboard("Alert name:", `Alert ${alerts.length + 1}`);
    if (!name) return;

    const priority = dialog.choice(["Low", "Normal", "High", "Critical"]);
    const pattern = dialog.choice([
        "Single Flash",
        "Double Flash",
        "Triple Flash",
        "Rapid Flash",
        "Morse Code"
    ]);

    let config = {};

    switch (pattern) {
        case 0: // Single Flash
            config = { type: "single", duration: 500 };
            break;
        case 1: // Double Flash
            config = { type: "double", duration: 300, gap: 200 };
            break;
        case 2: // Triple Flash
            config = { type: "triple", duration: 200, gap: 150 };
            break;
        case 3: // Rapid Flash
            config = { type: "rapid", duration: 100, count: 10 };
            break;
        case 4: // Morse Code
            const message = keyboard.keyboard("Morse message:", name.substring(0, 5));
            config = { type: "morse", message: message || name };
            break;
    }

    alerts.push({
        id: Date.now(),
        name: name,
        priority: ["low", "normal", "high", "critical"][priority],
        pattern: config,
        enabled: true,
        triggerCount: 0,
        lastTriggered: null
    });

    notify.success(`Alert "${name}" added`);
}

function viewAlerts(alerts) {
    if (alerts.length === 0) {
        dialog.info("No alerts configured");
        return;
    }

    let list = "Alert Configuration:\\n\\n";

    for (let i = 0; i < alerts.length; i++) {
        const alert = alerts[i];
        const status = alert.enabled ? "ENABLED" : "DISABLED";

        list += `${i + 1}. ${alert.name}\\n`;
        list += `   Priority: ${alert.priority.toUpperCase()}\\n`;
        list += `   Pattern: ${alert.pattern.type}\\n`;
        list += `   Status: ${status}\\n`;
        list += `   Triggered: ${alert.triggerCount} times\\n`;

        if (alert.lastTriggered) {
            const lastTime = new Date(alert.lastTriggered).toLocaleString();
            list += `   Last: ${lastTime}\\n`;
        }

        list += "\\n";
    }

    dialog.viewText(list, "Alert List");
}

function testAlert(alerts) {
    if (alerts.length === 0) {
        dialog.info("No alerts to test");
        return;
    }

    const names = alerts.map(alert => alert.name);
    const choice = dialog.choice(names);

    if (choice >= 0 && choice < alerts.length) {
        const alert = alerts[choice];

        if (!alert.enabled) {
            dialog.warning("Alert is disabled");
            return;
        }

        dialog.info(`Testing alert: ${alert.name}`);
        executeAlert(alert);
    }
}

function executeAlert(alert) {
    alert.triggerCount++;
    alert.lastTriggered = Date.now();

    console.log(`Executing alert: ${alert.name} (Priority: ${alert.priority})`);

    switch (alert.pattern.type) {
        case "single":
            notification.ledOn();
            delay(alert.pattern.duration);
            notification.ledOff();
            break;

        case "double":
            for (let i = 0; i < 2; i++) {
                notification.ledOn();
                delay(alert.pattern.duration);
                notification.ledOff();
                if (i < 1) delay(alert.pattern.gap);
            }
            break;

        case "triple":
            for (let i = 0; i < 3; i++) {
                notification.ledOn();
                delay(alert.pattern.duration);
                notification.ledOff();
                if (i < 2) delay(alert.pattern.gap);
            }
            break;

        case "rapid":
            for (let i = 0; i < alert.pattern.count; i++) {
                notification.ledOn();
                delay(alert.pattern.duration);
                notification.ledOff();
                delay(alert.pattern.duration);
            }
            break;

        case "morse":
            morseCode(alert.pattern.message);
            break;
    }

    // Show notification message based on priority
    const priorityMap = {
        low: "info",
        normal: "info",
        high: "warning",
        critical: "urgent"
    };

    const notifyType = priorityMap[alert.priority] || "info";
    notify[notifyType](alert.name);
}

function monitorAlerts(alerts) {
    if (alerts.length === 0) {
        dialog.info("No alerts to monitor");
        return;
    }

    display.fill(0);
    display.print(0, 0, "Alert Monitor", 2);
    display.print(0, 20, `${alerts.length} alerts active`, 1);
    display.print(0, 35, "Press any key for menu", 1);
    display.print(0, 50, "Hold Back to exit", 1);

    let menuVisible = false;

    while (true) {
        const key = keyboard.getKey();

        if (key === "BACK" && !menuVisible) {
            // Hold back to exit
            let holdTime = 0;
            while (keyboard.getKey() === "BACK" && holdTime < 2000) {
                delay(100);
                holdTime += 100;
            }

            if (holdTime >= 2000) {
                break; // Exit monitor mode
            }
        } else if (key && key !== "BACK") {
            // Show alert trigger menu
            const alertNames = alerts.filter(a => a.enabled).map(a => a.name);
            alertNames.push("Cancel");

            const choice = dialog.choice(alertNames);

            if (choice >= 0 && choice < alertNames.length - 1) {
                const alert = alerts.find(a => a.name === alertNames[choice]);
                if (alert) {
                    executeAlert(alert);
                }
            }

            // Refresh monitor display
            display.fill(0);
            display.print(0, 0, "Alert Monitor", 2);
            display.print(0, 20, `${alerts.length} alerts active`, 1);
            display.print(0, 35, "Press any key for menu", 1);
            display.print(0, 50, "Hold Back to exit", 1);
                    }

        delay(100);
    }
}
```

## LED Hardware Integration

```javascript linenums="1"
var notification = require("notification");
var gpio = require("gpio");

// LED control wrapper for different hardware configurations
function LEDController(pinNumber) {
    this.pin = pinNumber || 2;
    this.isOn = false;
    this.initialize();
}

LEDController.prototype.initialize = function() {
    // Initialize GPIO pin for LED control
        gpio.mode(this.pin, "output");
        gpio.write(this.pin, false);
        this.isOn = false;
    }

    on() {
        notification.ledOn();
        this.isOn = true;
    }

    off() {
        notification.ledOff();
        this.isOn = false;
    }

    toggle() {
        if (this.isOn) {
            this.off();
        } else {
            this.on();
        }
    }

    blink(duration = 500) {
        this.on();
        delay(duration);
        this.off();
    }

    pulse(cycles = 3, onTime = 200, offTime = 200) {
        for (let i = 0; i < cycles; i++) {
            this.on();
            delay(onTime);
            this.off();
            if (i < cycles - 1) {
                delay(offTime);
            }
        }
    }

    getState() {
        return this.isOn;
    }
}

// Global LED controller
const led = new LEDController();

// Integration examples
function ledHardwareTest() {
    console.log("Testing LED hardware...");

    // Basic functionality
    led.on();
    delay(1000);
    led.off();
    delay(500);

    // Toggle test
    for (let i = 0; i < 5; i++) {
        led.toggle();
        delay(300);
    }

    // Pulse test
    led.pulse(3, 200, 300);

    // Pattern test
    morseCode("TEST");

    console.log("LED test complete");
}
```
