# Runtime

The Runtime module provides control over JavaScript script execution, environment management, and runtime behavior configuration.

## Functions

### `runtime.exit([code])`

Exits the current JavaScript script with an optional exit code.

**Parameters:**

| Parameter | Type               | Description               |
| --------- | ------------------ | ------------------------- |
| `code`    | `number, optional` | Exit code (default: 0)    |

**Returns:** Does not return (terminates script)

**Example:**

```javascript linenums="1"
var runtime = require("runtime");

// Exit with success code
runtime.exit(0);

// Exit with error code
runtime.exit(1);

// Exit with specific error code
if (someCondition) {
    console.log("Critical error occurred");
    runtime.exit(-1);
}
```

### `runtime.exec(scriptPath)`

Executes another JavaScript file from the current script.

**Parameters:**

| Parameter    | Type     | Description                            |
| ------------ | -------- | -------------------------------------- |
| `scriptPath` | `string` | Path to the JavaScript file to execute |

**Returns:** `boolean` - True if script executed successfully, false otherwise

**Example:**

```javascript linenums="1"
var runtime = require("runtime");
// Execute another script
if (runtime.exec("/sd/BruceJS/helper.js")) {
    console.log("Helper script executed successfully");
} else {
    console.log("Failed to execute helper script");
    runtime.exit(1);
}

// Chain script execution
var scripts = [
    "/sd/BruceJS/init.js",
    "/sd/BruceJS/config.js",
    "/sd/BruceJS/main.js"
];

for (var i = 0; i < scripts.length; i++) {
    if (!runtime.exec(scripts[i])) {
        console.log("Failed to execute " + scripts[i]);
        runtime.exit(1);
    }
}
```

## Script Management System

### Script Launcher

```javascript linenums="1"
var runtime = require("runtime");
var dialog = require("dialog");
var storage = require("storage");

function scriptLauncher() {
    while (true) {
        var choice = dialog.choice([
            "Browse Scripts",
            "Recent Scripts",
            "Create Script",
            "Script Editor",
            "Runtime Info",
            "Exit"
        ]);

        switch (choice) {
            case 0: // Browse Scripts
                browseAndRunScripts();
                break;

            case 1: // Recent Scripts
                runRecentScripts();
                break;

            case 2: // Create Script
                createNewScript();
                break;

            case 3: // Script Editor
                launchScriptEditor();
                break;

            case 4: // Runtime Info
                showRuntimeInfo();
                break;

            case 5: // Exit
                runtime.exit(0);
        }
    }
}

function browseAndRunScripts() {
    var scriptDir = "/sd/BruceJS";

    try {
        var files = storage.readdir(scriptDir);
        var jsFiles = [];
        for (var i = 0; i < files.length; i++) {
            if (files[i].name && files[i].name.indexOf(".js") === files[i].name.length - 3) {
                jsFiles.push(files[i].name);
            }
        }

        if (jsFiles.length === 0) {
            dialog.info("No JavaScript files found in /sd/BruceJS/");
            return;
        }

        jsFiles.push("< Back");
        var choice = dialog.choice(jsFiles);

        if (choice >= 0 && choice < jsFiles.length - 1) {
            var scriptPath = scriptDir + "/" + jsFiles[choice];
            runScriptWithLogging(scriptPath);
        }

    } catch (error) {
        dialog.error("Failed to browse scripts: " + error.message);
    }
}

function runScriptWithLogging(scriptPath) {
    var startTime = Date.now();

    dialog.info("Executing: " + scriptPath);

    try {
        console.log("[Runtime] Starting script: " + scriptPath);

        var success = runtime.exec(scriptPath);
        var duration = Date.now() - startTime;

        if (success) {
            console.log("[Runtime] Script completed successfully in " + duration + "ms");
            updateRecentScripts(scriptPath, true, duration);
            dialog.success("Script completed in " + duration + "ms");
        } else {
            console.log("[Runtime] Script failed after " + duration + "ms");
            updateRecentScripts(scriptPath, false, duration);
            dialog.error(`Script execution failed`);
        }

    } catch (error) {
        const duration = Date.now() - startTime;
        console.log(`[Runtime] Script error: ${error.message}`);
        updateRecentScripts(scriptPath, false, duration);
        dialog.error(`Script error: ${error.message}`);
    }
}

function updateRecentScripts(scriptPath, success, duration) {
    let recent = [];

    try {
        const recentData = storage.read("scripts/recent.json");
        if (recentData) {
            recent = JSON.parse(recentData);
        }
    } catch (e) {
        // Ignore parse errors, start with empty array
    }

    // Remove existing entry for this script
    recent = recent.filter(entry => entry.path !== scriptPath);

    // Add new entry at beginning
    recent.unshift({
        path: scriptPath,
        timestamp: Date.now(),
        success: success,
        duration: duration
    });

    // Keep only last 10 entries
    recent = recent.slice(0, 10);

    try {
        storage.write("scripts/recent.json", JSON.stringify(recent));
    } catch (e) {
        console.log("Failed to save recent scripts");
    }
}

function runRecentScripts() {
    let recent = [];

    try {
        const recentData = storage.read("scripts/recent.json");
        if (recentData) {
            recent = JSON.parse(recentData);
        }
    } catch (e) {
        dialog.info("No recent scripts found");
        return;
    }

    if (recent.length === 0) {
        dialog.info("No recent scripts found");
        return;
    }

    var choices = [];
    for (var i = 0; i < recent.length; i++) {
        var entry = recent[i];
        var pathParts = entry.path.split('/');
        var filename = pathParts[pathParts.length - 1];
        var status = entry.success ? "✓" : "✗";
        var time = new Date(entry.timestamp).toLocaleString();
        choices.push(status + " " + filename + " (" + entry.duration + "ms)");
    }

    choices.push("< Back");

    const choice = dialog.choice(choices);

    if (choice >= 0 && choice < recent.length) {
        const scriptPath = recent[choice].path;
        runScriptWithLogging(scriptPath);
    }
}
```

### Script Creation and Editing

```javascript
function createNewScript() {
    const name = keyboard.keyboard("Script name:", "new_script.js");
    if (!name) return;

    let filename = name;
    if (!filename.endsWith(".js")) {
        filename += ".js";
    }

    const template = dialog.choice([
        "Blank Script",
        "Hello World",
        "GPIO Control",
        "Display Demo",
        "File Operations",
        "Custom Template"
    ]);

    let content = "";

    switch (template) {
        case 0: // Blank Script
            content = `// ${filename}
// Created: ${Date.now()}

console.log("Script started");

// Your code here

console.log("Script completed");
`;
            break;

        case 1: // Hello World
            content = `// Hello World Example
console.log("Hello, World!");

display.fill(0);
display.print(0, 0, "Hello World!", 2);

dialog.info("Hello from JavaScript!");
`;
            break;

        case 2: // GPIO Control
            content = "// GPIO Control Example\nvar LED_PIN = 2;\n\n// Configure LED pin\ngpio.mode(LED_PIN, 'output');\n\n// Blink LED\nfor (var i = 0; i < 10; i++) {\n    gpio.write(LED_PIN, true);\n    delay(500);\n    gpio.write(LED_PIN, false);\n    delay(500);\n}\n\nconsole.log('GPIO demo completed');\n";
            break;

        case 3: // Display Demo
            content = "// Display Demo\ndisplay.fill(0);\n\n// Title\ndisplay.print(0, 0, 'Display Demo', 2);\n\n// Show counter\nfor (var i = 1; i <= 10; i++) {\n    display.drawFillRect(0, 25, 128, 15, 0); // Clear counter area\n    display.print(0, 25, 'Count: ' + i, 1);\n    delay(500);\n}\n\ndisplay.print(0, 40, 'Demo Complete!', 1);\ndelay(2000);\n";
            break;

        case 4: // File Operations
            content = `// File Operations Example
const testFile = "/sd/test_output.txt";
const testData = "Hello from script!\\nTimestamp: " + Date.now();

// Write file
if (storage.write(testFile, testData)) {
    console.log("File written successfully");

    // Read file back
    const readData = storage.read(testFile);
    if (readData) {
        console.log("File content:", readData);
        dialog.info("File operations successful!");
    } else {
        dialog.error("Failed to read file");
    }
} else {
    dialog.error("Failed to write file");
}
`;
            break;

        case 5: // Custom Template
            content = keyboard.textArea("Enter script content:", "// Custom script\\nconsole.log('Hello');");
            break;
    }

    const scriptPath = `/sd/BruceJS/${filename}`;

    if (storage.write(scriptPath, content)) {
        dialog.success(`Script created: ${scriptPath}`);

        const runNow = dialog.choice(["Run Now", "Edit First", "Just Save"]);

        switch (runNow) {
            case 0: // Run Now
                runScriptWithLogging(scriptPath);
                break;
            case 1: // Edit First
                editScript(scriptPath);
                break;
            // case 2: Just save - do nothing
        }
    } else {
        dialog.error("Failed to create script");
    }
}

function launchScriptEditor() {
    const scriptFile = dialog.pickFile("/sd/BruceJS", ".js");
    if (scriptFile) {
        editScript(scriptFile);
    }
}

function editScript(scriptPath) {
    try {
        const content = storage.read(scriptPath);
        if (content === null) {
            dialog.error("Failed to read script file");
            return;
        }

        const newContent = keyboard.textArea("Edit script:", content);
        if (newContent !== null) {
            if (storage.write(scriptPath, newContent)) {
                dialog.success("Script saved successfully");

                const action = dialog.choice(["Run Script", "Save Only", "Validate Syntax"]);

                switch (action) {
                    case 0: // Run Script
                        runScriptWithLogging(scriptPath);
                        break;
                    case 2: // Validate Syntax
                        validateScriptSyntax(newContent);
                        break;
                }
            } else {
                dialog.error("Failed to save script");
            }
        }

    } catch (error) {
        dialog.error(`Failed to edit script: ${error.message}`);
    }
}

function validateScriptSyntax(code) {
    try {
        // Basic syntax validation (would need actual parser in real implementation)
        // This is a simplified check
        const lines = code.split('\\n');
        let braceCount = 0;
        let parenCount = 0;
        let issues = [];

        for (var i = 0; i < lines.length; i++) {
            var line = lines[i].trim();
            if (line.length === 0 || line.indexOf('//') === 0) continue;

            // Count braces and parentheses
            for (var j = 0; j < line.length; j++) {
                var char = line[j];
                if (char === '{') braceCount++;
                if (char === '}') braceCount--;
                if (char === '(') parenCount++;
                if (char === ')') parenCount--;
            }

            // Check for common issues
            if (line.includes('console.log') && !line.includes('(')) {
                issues.push(`Line ${i + 1}: Missing parentheses in console.log`);
            }
        }

        if (braceCount !== 0) {
            issues.push(`Mismatched braces: ${braceCount > 0 ? 'extra opening' : 'extra closing'}`);
        }

        if (parenCount !== 0) {
            issues.push(`Mismatched parentheses: ${parenCount > 0 ? 'extra opening' : 'extra closing'}`);
        }

        if (issues.length === 0) {
            dialog.success("No syntax issues found");
        } else {
            dialog.error(`Syntax issues found:\\n${issues.join('\\n')}`);
        }

    } catch (error) {
        dialog.error(`Validation error: ${error.message}`);
    }
}
```

### Runtime Environment Management

```javascript
function showRuntimeInfo() {
    const info = gatherRuntimeInfo();
    dialog.viewText(info, "Runtime Information");
}

function gatherRuntimeInfo() {
    let info = "JavaScript Runtime Information:\\n\\n";

    // Memory information (simulated)
    info += "Memory Usage:\\n";
    info += "  Used: ~150KB\\n";
    info += "  Available: ~350KB\\n";
    info += "  Total: ~500KB\\n\\n";

    // Execution context
    info += "Execution Context:\\n";
    info += "  Engine: MicroQuickJS\\n";
    info += "  Strict Mode: Yes\\n";
    info += "  Script Directory: /sd/BruceJS/\\n\\n";

    // Available modules
    info += "Available Modules:\\n";
    const modules = [
        "audio", "badusb", "device", "dialog", "display",
        "globals", "gpio", "i2c", "ir", "keyboard",
        "math", "mic", "notification", "rfid", "runtime",
        "serial", "storage", "subghz", "wifi"
    ];

    for (var i = 0; i < modules.length; i++) {
        info += "  ✓ " + modules[i] + "\n";
    }

    info += "\\nEnvironment Variables:\\n";
    info += "  BRUCE_VERSION: " + device.getVersion() + "\n";
    info += "  HARDWARE: " + device.getModel() + "\n";
    info += "  STORAGE: SD Card\n";

    return info;
}

function runtimeDiagnostics() {
    const tests = [
        { name: "Module Loading", test: testModuleLoading },
        { name: "Memory Access", test: testMemoryAccess },
        { name: "File System", test: testFileSystem },
        { name: "Script Execution", test: testScriptExecution },
        { name: "Error Handling", test: testErrorHandling }
    ];

    let results = "Runtime Diagnostics:\\n\\n";

    for (var i = 0; i < tests.length; i++) {
        var test = tests[i];
        try {
            var passed = test.test();
            results += (passed ? '✓' : '✗') + " " + test.name + ": " + (passed ? 'PASS' : 'FAIL') + "\n";
        } catch (error) {
            results += "✗ " + test.name + ": ERROR - " + error.message + "\n";
        }
    }

    return results;
}

function testModuleLoading() {
    // Test that core modules are available
    return typeof console !== 'undefined' &&
           typeof display !== 'undefined' &&
           typeof dialog !== 'undefined';
}

function testMemoryAccess() {
    // Test basic memory operations
    try {
        const arr = new Array(1000);
        for (let i = 0; i < arr.length; i++) {
            arr[i] = i;
        }
        return arr[999] === 999;
    } catch (e) {
        return false;
    }
}

function testFileSystem() {
    // Test file system access
    try {
        const testData = "runtime_test_" + Date.now();
        const testFile = "/sd/runtime_test.tmp";

        if (!storage.write(testFile, testData)) {
            return false;
        }

        const readData = storage.read(testFile);
        storage.remove(testFile); // Cleanup

        return readData === testData;
    } catch (e) {
        return false;
    }
}

function testScriptExecution() {
    // Test basic script execution capabilities
    try {
        const testScript = `/sd/BruceJS/runtime_test_${Date.now()}.js`;
        const scriptContent = 'console.log("Runtime test successful");';

        if (!storage.write(testScript, scriptContent)) {
            return false;
        }

        const result = runtime.exec(testScript);
        storage.remove(testScript); // Cleanup

        return result;
    } catch (e) {
        return false;
    }
}

function testErrorHandling() {
    // Test error handling mechanisms
    try {
        try {
            throw new Error("Test error");
        } catch (e) {
            return e.message === "Test error";
        }
    } catch (e) {
        return false;
    }
}
```

### Process Control System

```javascript
class ScriptManager {
    constructor() {
        this.runningScripts = [];
        this.scriptHistory = [];
        this.maxHistory = 50;
    }

    executeScript(scriptPath, args = []) {
        const execution = {
            id: Date.now(),
            path: scriptPath,
            args: args,
            startTime: Date.now(),
            status: "running",
            exitCode: null,
            error: null
        };

        this.runningScripts.push(execution);

        try {
            console.log(`[ScriptManager] Starting: ${scriptPath}`);

            // Set up execution environment
            this.setupScriptEnvironment(execution);

            // Execute the script
            const success = runtime.exec(scriptPath);

            execution.status = success ? "completed" : "failed";
            execution.exitCode = success ? 0 : 1;
            execution.endTime = Date.now();

        } catch (error) {
            execution.status = "error";
            execution.error = error.message;
            execution.exitCode = -1;
            execution.endTime = Date.now();

            console.log(`[ScriptManager] Error in ${scriptPath}: ${error.message}`);
        }

        // Move to history
        this.runningScripts = this.runningScripts.filter(s => s.id !== execution.id);
        this.addToHistory(execution);

        return execution;
    }

    setupScriptEnvironment(execution) {
        // Set global variables for script
        global.SCRIPT_PATH = execution.path;
        global.SCRIPT_ID = execution.id;
        global.SCRIPT_ARGS = execution.args;
        global.SCRIPT_START_TIME = execution.startTime;
    }

    addToHistory(execution) {
        this.scriptHistory.unshift(execution);

        if (this.scriptHistory.length > this.maxHistory) {
            this.scriptHistory = this.scriptHistory.slice(0, this.maxHistory);
        }

        this.saveHistory();
    }

    saveHistory() {
        try {
            const historyData = JSON.stringify(this.scriptHistory);
            storage.write("scripts/execution_history.json", historyData);
        } catch (e) {
            console.log("Failed to save script history");
        }
    }

    loadHistory() {
        try {
            const historyData = storage.read("scripts/execution_history.json");
            if (historyData) {
                this.scriptHistory = JSON.parse(historyData);
            }
        } catch (e) {
            this.scriptHistory = [];
        }
    }

    getHistory() {
        return this.scriptHistory.slice(); // Return copy
    }

    clearHistory() {
        this.scriptHistory = [];
        this.saveHistory();
    }

    getRunningScripts() {
        return this.runningScripts.slice(); // Return copy
    }

    killScript(scriptId) {
        const script = this.runningScripts.find(s => s.id === scriptId);
        if (script) {
            script.status = "killed";
            script.exitCode = -9;
            script.endTime = Date.now();

            // Remove from running list
            this.runningScripts = this.runningScripts.filter(s => s.id !== scriptId);

            // Add to history
            this.addToHistory(script);

            return true;
        }
        return false;
    }

    formatExecutionInfo(execution) {
        const duration = execution.endTime ?
            execution.endTime - execution.startTime :
            Date.now() - execution.startTime;

        let info = `Script: ${execution.path}\\n`;
        info += `ID: ${execution.id}\\n`;
        info += `Status: ${execution.status.toUpperCase()}\\n`;
        info += `Duration: ${duration}ms\\n`;
        info += `Started: ${new Date(execution.startTime).toLocaleString()}\\n`;

        if (execution.endTime) {
            info += `Ended: ${new Date(execution.endTime).toLocaleString()}\\n`;
        }

        if (execution.exitCode !== null) {
            info += `Exit Code: ${execution.exitCode}\\n`;
        }

        if (execution.error) {
            info += `Error: ${execution.error}\\n`;
        }

        if (execution.args && execution.args.length > 0) {
            info += `Arguments: ${execution.args.join(', ')}\\n`;
        }

        return info;
    }
}

// Global script manager instance
const scriptManager = new ScriptManager();
scriptManager.loadHistory();

function processManager() {
    while (true) {
        const choice = dialog.choice([
            "Running Scripts",
            "Execution History",
            "Run Script",
            "System Info",
            "Clear History",
            "Exit"
        ]);

        switch (choice) {
            case 0: // Running Scripts
                showRunningScripts();
                break;

            case 1: // Execution History
                showExecutionHistory();
                break;

            case 2: // Run Script
                const scriptFile = dialog.pickFile("/sd/BruceJS", ".js");
                if (scriptFile) {
                    const execution = scriptManager.executeScript(scriptFile);
                    const info = scriptManager.formatExecutionInfo(execution);
                    dialog.viewText(info, "Execution Result");
                }
                break;

            case 3: // System Info
                const info = gatherRuntimeInfo() + "\\n\\n" + runtimeDiagnostics();
                dialog.viewText(info, "System Information");
                break;

            case 4: // Clear History
                scriptManager.clearHistory();
                dialog.info("Execution history cleared");
                break;

            case 5: // Exit
                return;
        }
    }
}

function showRunningScripts() {
    const running = scriptManager.getRunningScripts();

    if (running.length === 0) {
        dialog.info("No scripts currently running");
        return;
    }

    var choices = [];
    for (var i = 0; i < running.length; i++) {
        var script = running[i];
        var duration = Date.now() - script.startTime;
        var pathParts = script.path.split('/');
        var name = pathParts[pathParts.length - 1];
        choices.push(name + " (" + duration + "ms)");
    }

    choices.push("< Back");

    const choice = dialog.choice(choices);

    if (choice >= 0 && choice < running.length) {
        const script = running[choice];
        const info = scriptManager.formatExecutionInfo(script);

        dialog.viewText(info, "Script Details");
    }
}

function showExecutionHistory() {
    const history = scriptManager.getHistory();

    if (history.length === 0) {
        dialog.info("No execution history");
        return;
    }

    var choices = [];
    for (var i = 0; i < Math.min(history.length, 20); i++) {
        var execution = history[i];
        var pathParts = execution.path.split('/');
        var name = pathParts[pathParts.length - 1];
        var status = execution.status === "completed" ? "✓" :
                      execution.status === "failed" ? "✗" : "⚠";
        var duration = execution.endTime ?
            execution.endTime - execution.startTime : "?";
        choices.push(status + " " + name + " (" + duration + "ms)");
    }

    choices.push("< Back");

    const choice = dialog.choice(choices);

    if (choice >= 0 && choice < history.length) {
        const execution = history[choice];
        const info = scriptManager.formatExecutionInfo(execution);
        dialog.viewText(info, "Execution Details");
    }
}
```

## Error Handling and Debugging

```javascript
function createDebugScript(scriptPath) {
    var debugWrapper = "\n// Debug wrapper for: " + scriptPath + "\nconsole.log('[Debug] Script starting: " + scriptPath + "');\nconsole.log('[Debug] Timestamp:', Date.now());\n\ntry {\n    // Execute original script\n    var result = runtime.exec('" + scriptPath + "');\n    console.log('[Debug] Script result:', result);\n    \n    if (result) {\n        console.log('[Debug] Script completed successfully');\n    } else {\n        console.log('[Debug] Script failed');\n    }\n    \n} catch (error) {\n    console.log('[Debug] Script error:', error.message);\n    console.log('[Debug] Stack trace:', error.stack || 'Not available');\n    \n    // Save error log\n    var errorLog = {\n        script: '" + scriptPath + "',\n        error: error.message,\n        timestamp: Date.now(),\n        stack: error.stack || null\n    };\n    \n    var logFile = '/sd/debug_errors.json';\n    var errors = [];\n    \n    try {\n        var existingData = storage.read(logFile);\n        if (existingData) {\n            errors = JSON.parse(existingData);\n        }\n    } catch (e) {\n        // Ignore parse errors\n    }\n    \n    errors.push(errorLog);\n    storage.write(logFile, JSON.stringify(errors, null, 2));\n    \n    dialog.error('Script error logged to debug_errors.json');\n}\n\nconsole.log('[Debug] Debug session completed');\n";

    const debugPath = "/sd/debug_wrapper.js";
    storage.write(debugPath, debugWrapper);

    return debugPath;
}
```
