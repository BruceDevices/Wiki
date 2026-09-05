# Sub-GHz

The Sub-GHz module provides functionality for transmitting and receiving signals on sub-gigahertz radio frequencies, commonly used for IoT devices, remote controls, and various wireless protocols.

## Functions

### `subghz.transmitFile(filePath)`

Transmits a Sub-GHz signal from a file containing the signal data.

**Parameters:**

| Parameter  | Type     | Description                                     |
| ---------- | -------- | ----------------------------------------------- |
| `filePath` | `string` | Path to the file containing Sub-GHz signal data |

**Returns:** `boolean` - True if transmission was successful, false otherwise

**Example:**

```javascript linenums="1"
var subghz = require("subghz");
var dialog = require("dialog");
// Transmit a saved remote control signal
if (subghz.transmitFile("/sd/subghz/garage_door.sub")) {
    console.log("Garage door signal transmitted");
    dialog.success("Signal sent successfully!");
} else {
    console.log("Failed to transmit signal");
    dialog.error("Transmission failed");
}

// Transmit multiple signals
var signals = [
    "/sd/subghz/gate_open.sub",
    "/sd/subghz/gate_close.sub"
];

for (var i = 0; i < signals.length; i++) {
    console.log("Transmitting: " + signals[i]);
    if (subghz.transmitFile(signals[i])) {
        delay(1000); // Wait between transmissions
    } else {
        dialog.error("Failed to transmit: " + signals[i]);
        break;
    }
}
```

### `subghz.transmit(frequency, data)`

Transmits raw Sub-GHz data on a specified frequency.

**Parameters:**

| Parameter   | Type     | Description                                      |
| ----------- | -------- | ------------------------------------------------ |
| `frequency` | `number` | Frequency in Hz (e.g., 433920000 for 433.92 MHz) |
| `data`      | `string` | Raw signal data in appropriate format            |

**Returns:** `boolean` - True if transmission was successful, false otherwise

**Example:**

```javascript linenums="1"
// Transmit on 433.92 MHz
var freq = 433920000; // 433.92 MHz
var rawData = "RAW_Data: 1000 -1000 500 -500..."; // Example raw data

if (subghz.transmit(freq, rawData)) {
    console.log("Raw signal transmitted on 433.92 MHz");
} else {
    console.log("Raw transmission failed");
}

// Common Sub-GHz frequencies
var frequencies = {
    "315MHz": 315000000,
    "433MHz": 433920000,
    "868MHz": 868350000,
    "915MHz": 915000000
};

// Transmit test signal on multiple frequencies
for (var name in frequencies) {
    var freq = frequencies[name];
    console.log("Testing " + name + " (" + freq + " Hz)");
    var testSignal = generateTestPattern();

    if (subghz.transmit(freq, testSignal)) {
        console.log(name + ": SUCCESS");
    } else {
        console.log(name + ": FAILED");
    }

    delay(500); // Wait between tests
}

function generateTestPattern() {
    var pattern = [];
    for (var i = 0; i < 10; i++) {
        pattern.push("1000 -1000");
    }
    return "RAW_Data: " + pattern.join(" ");
}
```

## Sub-GHz Applications

### Remote Control Cloner

```javascript
var subghz = require("subghz");
var dialog = require("dialog");
var keyboard = require("keyboard");
var display = require("display");
var storage = require("storage");
var display = require("display");

function remoteControlCloner() {
    while (true) {
        const choice = dialog.choice([
            "Scan for Signals",
            "Transmit Signal",
            "Signal Library",
            "Raw Transmitter",
            "Frequency Scanner",
            "Exit"
        ]);

        switch (choice) {
            case 0: // Scan for Signals
                scanForSignals();
                break;

            case 1: // Transmit Signal
                transmitSavedSignal();
                break;

            case 2: // Signal Library
                manageSignalLibrary();
                break;

            case 3: // Raw Transmitter
                rawTransmitter();
                break;

            case 4: // Frequency Scanner
                frequencyScanner();
                break;

            case 5: // Exit
                return;
        }
    }
}

function scanForSignals() {
    const frequencies = [
        { name: "315 MHz", freq: 315000000 },
        { name: "433.92 MHz", freq: 433920000 },
        { name: "868.35 MHz", freq: 868350000 },
        { name: "915 MHz", freq: 915000000 }
    ];

    const choice = dialog.choice(frequencies.map(f => f.name).concat(["Cancel"]));

    if (choice >= 0 && choice < frequencies.length) {
        const selectedFreq = frequencies[choice];

        dialog.info(`Scanning ${selectedFreq.name}\\nPress any button to stop`);

        display.fill(0);
        display.print(0, 0, "Scanning...", 2);
        display.print(0, 20, selectedFreq.name, 1);
        display.print(0, 35, "Press Back to stop", 1);

        // Simulate signal scanning (would use actual receiver in real implementation)
        let scanTime = 0;
        const maxScanTime = 30000; // 30 seconds

        while (scanTime < maxScanTime) {
            const key = keyboard.getKey();
            if (keyboard.getEscPress()) break;

            // Simulate signal detection
            if (Math.random() > 0.99) { // 1% chance per iteration
                const signalData = captureSignal(selectedFreq.freq);
                if (signalData) {
                    handleCapturedSignal(signalData, selectedFreq);
                    return;
                }
            }

            // Update display
            var dots = "";
            for (var d = 0; d < (scanTime / 500) % 4; d++) {
                dots += ".";
            }
            display.drawFillRect(0, 50, 128, 10, 0);
            display.print(0, 50, "Scanning" + dots, 1);

            delay(100);
            scanTime += 100;
        }

        dialog.info("Scan completed - no signals detected");
    }
}

function captureSignal(frequency) {
    // Simulate signal capture (would use actual receiver)
    var simulatedSignal = {
        frequency: frequency,
        protocol: detectProtocol(),
        rawData: generateRandomSignal(),
        timestamp: Date.now(),
        rssi: -50 - Math.random() * 30 // Simulate signal strength
    };

    return simulatedSignal;
}

function detectProtocol() {
    const protocols = ["AM650", "AM270", "FM238", "FM476", "Unknown"];
    return protocols[Math.floor(Math.random() * protocols.length)];
}

function generateRandomSignal() {
    const patterns = [];
    for (let i = 0; i < 50; i++) {
        const high = 200 + Math.random() * 1000;
        const low = -(200 + Math.random() * 1000);
        patterns.push(Math.round(high), Math.round(low));
    }
    return "RAW_Data: " + patterns.join(" ");
}

function handleCapturedSignal(signal, frequency) {
    var signalInfo = "Signal Detected!\n\nFrequency: " + frequency.name + "\nProtocol: " + signal.protocol + "\nRSSI: " + signal.rssi.toFixed(1) + " dBm\nTimestamp: " + new Date(signal.timestamp).toLocaleString() + "\n\nRaw Data Preview:\n" + signal.rawData.substring(0, 100) + "...";

    dialog.viewText(signalInfo, "Captured Signal");

    const action = dialog.choice([
        "Save Signal",
        "Transmit Now",
        "Analyze",
        "Discard"
    ]);

    switch (action) {
        case 0: // Save Signal
            saveSignal(signal, frequency);
            break;

        case 1: // Transmit Now
            if (subghz.transmit(signal.frequency, signal.rawData)) {
                dialog.success("Signal retransmitted!");
            } else {
                dialog.error("Retransmission failed");
            }
            break;

        case 2: // Analyze
            analyzeSignal(signal);
            break;

        // case 3: Discard - do nothing
    }
}

function saveSignal(signal, frequency) {
    const name = keyboard.keyboard("Signal name:", `signal_${frequency.name.replace(/\\s+/g, '_')}`);
    if (!name) return;

    let filename = name;
    if (!filename.endsWith('.sub')) {
        filename += '.sub';
    }

    const subghzFormat = `Filetype: Flipper SubGhz RAW File
Version: 1
Frequency: ${signal.frequency}
Preset: FuriHalSubGhzPresetOok650Async
Protocol: ${signal.protocol}
Bit: 0
Key: 00 00 00 00 00 00 00 00
TE: 500
${signal.rawData}`;

    const filepath = `/sd/subghz/${filename}`;

    if (storage.write(filepath, subghzFormat)) {
        dialog.success(`Signal saved as: ${filename}`);

        // Add to signal library index
        addToSignalLibrary(filepath, {
            name: name,
            frequency: frequency.name,
            protocol: signal.protocol,
            timestamp: signal.timestamp
        });
    } else {
        dialog.error("Failed to save signal");
    }
}

function addToSignalLibrary(filepath, metadata) {
    let library = [];

    try {
        const libraryData = storage.read("/sd/subghz/library.json");
        if (libraryData) {
            library = JSON.parse(libraryData);
        }
    } catch (e) {
        // Start with empty library
    }

    library.push({
        filepath: filepath,
        metadata: metadata
    });

    try {
        storage.write("/sd/subghz/library.json", JSON.stringify(library, null, 2));
    } catch (e) {
        console.log("Failed to update signal library");
    }
}
```

### Signal Library Management

```javascript
function manageSignalLibrary() {
    let library = loadSignalLibrary();

    while (true) {
        if (library.length === 0) {
            dialog.info("Signal library is empty");
            return;
        }

        const choices = library.map(entry => {
            const name = entry.metadata.name;
            const freq = entry.metadata.frequency;
            const protocol = entry.metadata.protocol;
            return `${name} (${freq}, ${protocol})`;
        });

        choices.push("Refresh Library");
        choices.push("Clear Library");
        choices.push("< Back");

        const choice = dialog.choice(choices);

        if (choice >= 0 && choice < library.length) {
            // Signal selected
            manageSignalEntry(library[choice]);
            library = loadSignalLibrary(); // Refresh

        } else if (choice === library.length) {
            // Refresh
            library = loadSignalLibrary();

        } else if (choice === library.length + 1) {
            // Clear Library
            if (dialog.choice(["Cancel", "Clear All"]) === 1) {
                clearSignalLibrary();
                library = [];
            }

        } else {
            // Back
            return;
        }
    }
}

function loadSignalLibrary() {
    try {
        const libraryData = storage.read("/sd/subghz/library.json");
        if (libraryData) {
            return JSON.parse(libraryData);
        }
    } catch (e) {
        console.log("Failed to load signal library");
    }

    return [];
}

function manageSignalEntry(entry) {
    const info = `Signal: ${entry.metadata.name}

File: ${entry.filepath}
Frequency: ${entry.metadata.frequency}
Protocol: ${entry.metadata.protocol}
Saved: ${new Date(entry.metadata.timestamp).toLocaleString()}`;

    dialog.viewText(info, "Signal Details");

    const action = dialog.choice([
        "Transmit",
        "Edit Name",
        "Test Transmission",
        "View Raw Data",
        "Delete",
        "Cancel"
    ]);

    switch (action) {
        case 0: // Transmit
            transmitSignalFile(entry.filepath);
            break;

        case 1: // Edit Name
            editSignalName(entry);
            break;

        case 2: // Test Transmission
            testSignalTransmission(entry);
            break;

        case 3: // View Raw Data
            viewSignalRawData(entry.filepath);
            break;

        case 4: // Delete
            deleteSignal(entry);
            break;

        // case 5: Cancel - do nothing
    }
}

function transmitSignalFile(filepath) {
    if (subghz.transmitFile(filepath)) {
        dialog.success("Signal transmitted successfully!");
    } else {
        dialog.error("Failed to transmit signal");
    }
}

function editSignalName(entry) {
    const newName = keyboard.keyboard("New name:", entry.metadata.name);
    if (newName && newName !== entry.metadata.name) {
        entry.metadata.name = newName;
        saveSignalLibrary();
        dialog.success("Signal renamed");
    }
}

function testSignalTransmission(entry) {
    const count = parseInt(keyboard.numKeyboard("Number of transmissions:", "3"));
    const delay_ms = parseInt(keyboard.numKeyboard("Delay between (ms):", "1000"));

    if (isNaN(count) || isNaN(delay_ms) || count < 1) {
        dialog.error("Invalid parameters");
        return;
    }

    let successful = 0;

    for (let i = 1; i <= count; i++) {
        display.fill(0);
        display.print(0, 0, "Test Transmission", 2);
        display.print(0, 25, `Attempt ${i}/${count}`, 1);
        display.print(0, 40, entry.metadata.name, 1);

        if (subghz.transmitFile(entry.filepath)) {
            successful++;
            console.log(`Test ${i}/${count}: SUCCESS`);
        } else {
            console.log(`Test ${i}/${count}: FAILED`);
        }

        if (i < count) {
            delay(delay_ms);
        }
    }

    dialog.info(`Test completed\\nSuccessful: ${successful}/${count}`);
}

function viewSignalRawData(filepath) {
    try {
        const data = storage.read(filepath);
        if (data) {
            // Extract just the raw data portion for viewing
            const lines = data.split('\\n');
            let rawData = "";
            let inRawSection = false;

            for (let line of lines) {
                if (line.startsWith('RAW_Data:')) {
                    inRawSection = true;
                }
                if (inRawSection) {
                    rawData += line + "\\n";
                }
            }

            if (rawData) {
                dialog.viewText(rawData, "Raw Signal Data");
            } else {
                dialog.error("No raw data found in signal file");
            }
        } else {
            dialog.error("Failed to read signal file");
        }
    } catch (e) {
        dialog.error(`Error reading file: ${e.message}`);
    }
}

function deleteSignal(entry) {
    const confirm = dialog.choice([
        "Cancel",
        `Delete "${entry.metadata.name}"`
    ]);

    if (confirm === 1) {
        try {
            // Remove file
            storage.remove(entry.filepath);

            // Remove from library
            let library = loadSignalLibrary();
            library = library.filter(e => e.filepath !== entry.filepath);

            storage.write("/sd/subghz/library.json", JSON.stringify(library, null, 2));

            dialog.success("Signal deleted");
        } catch (e) {
            dialog.error(`Failed to delete signal: ${e.message}`);
        }
    }
}
```

### Raw Signal Transmitter

```javascript
function rawTransmitter() {
    while (true) {
        const choice = dialog.choice([
            "Manual Raw Data",
            "Pattern Generator",
            "Frequency Sweep",
            "Pulse Train",
            "Modulation Test",
            "Exit"
        ]);

        switch (choice) {
            case 0: // Manual Raw Data
                manualRawTransmission();
                break;

            case 1: // Pattern Generator
                patternGenerator();
                break;

            case 2: // Frequency Sweep
                frequencySweep();
                break;

            case 3: // Pulse Train
                pulseTrainGenerator();
                break;

            case 4: // Modulation Test
                modulationTest();
                break;

            case 5: // Exit
                return;
        }
    }
}

function manualRawTransmission() {
    const freq = parseInt(keyboard.numKeyboard("Frequency (Hz):", "433920000"));
    if (isNaN(freq) || freq < 300000000 || freq > 1000000000) {
        dialog.error("Invalid frequency (300-1000 MHz)");
        return;
    }

    const rawData = keyboard.textArea("Raw data:", "RAW_Data: 1000 -1000 500 -500");
    if (!rawData || !rawData.startsWith("RAW_Data:")) {
        dialog.error("Invalid raw data format");
        return;
    }

    if (subghz.transmit(freq, rawData)) {
        dialog.success(`Transmitted on ${(freq / 1000000).toFixed(2)} MHz`);
    } else {
        dialog.error("Transmission failed");
    }
}

function patternGenerator() {
    const patterns = [
        { name: "Square Wave", generator: generateSquareWave },
        { name: "Pulse Train", generator: generatePulseTrain },
        { name: "Random Pattern", generator: generateRandomPattern },
        { name: "Morse Code", generator: generateMorsePattern }
    ];

    const patternChoice = dialog.choice(patterns.map(p => p.name).concat(["Cancel"]));

    if (patternChoice >= 0 && patternChoice < patterns.length) {
        const pattern = patterns[patternChoice];
        const freq = parseInt(keyboard.numKeyboard("Frequency (Hz):", "433920000"));

        if (isNaN(freq)) {
            dialog.error("Invalid frequency");
            return;
        }

        const rawData = pattern.generator();

        if (rawData) {
            console.log(`Generated ${pattern.name}:`);
            console.log(rawData.substring(0, 100) + "...");

            if (subghz.transmit(freq, rawData)) {
                dialog.success(`${pattern.name} transmitted`);
            } else {
                dialog.error("Transmission failed");
            }
        }
    }
}

function generateSquareWave() {
    const highTime = parseInt(keyboard.numKeyboard("High time (μs):", "1000"));
    const lowTime = parseInt(keyboard.numKeyboard("Low time (μs):", "1000"));
    const cycles = parseInt(keyboard.numKeyboard("Number of cycles:", "10"));

    if (isNaN(highTime) || isNaN(lowTime) || isNaN(cycles)) {
        dialog.error("Invalid parameters");
        return null;
    }

    const pattern = [];
    for (let i = 0; i < cycles; i++) {
        pattern.push(highTime, -lowTime);
    }

    return "RAW_Data: " + pattern.join(" ");
}

function generatePulseTrain() {
    const pulseWidth = parseInt(keyboard.numKeyboard("Pulse width (μs):", "500"));
    const pulseGap = parseInt(keyboard.numKeyboard("Pulse gap (μs):", "1500"));
    const pulseCount = parseInt(keyboard.numKeyboard("Number of pulses:", "8"));

    if (isNaN(pulseWidth) || isNaN(pulseGap) || isNaN(pulseCount)) {
        dialog.error("Invalid parameters");
        return null;
    }

    const pattern = [];
    for (let i = 0; i < pulseCount; i++) {
        pattern.push(pulseWidth, -pulseGap);
    }

    return "RAW_Data: " + pattern.join(" ");
}

function generateRandomPattern() {
    const length = parseInt(keyboard.numKeyboard("Pattern length:", "20"));
    const minTime = parseInt(keyboard.numKeyboard("Min time (μs):", "200"));
    const maxTime = parseInt(keyboard.numKeyboard("Max time (μs):", "2000"));

    if (isNaN(length) || isNaN(minTime) || isNaN(maxTime) || minTime >= maxTime) {
        dialog.error("Invalid parameters");
        return null;
    }

    const pattern = [];
    for (let i = 0; i < length; i++) {
        const time = minTime + Math.random() * (maxTime - minTime);
        pattern.push(i % 2 === 0 ? Math.round(time) : -Math.round(time));
    }

    return "RAW_Data: " + pattern.join(" ");
}

function generateMorsePattern() {
    const message = keyboard.keyboard("Morse message:", "HELLO");
    const dotTime = parseInt(keyboard.numKeyboard("Dot time (μs):", "500"));
    const dashTime = dotTime * 3;
    const gapTime = dotTime;

    const morseMap = {
        'A': '.-', 'B': '-...', 'C': '-.-.', 'D': '-..', 'E': '.', 'F': '..-.',
        'G': '--.', 'H': '....', 'I': '..', 'J': '.---', 'K': '-.-', 'L': '.-..',
        'M': '--', 'N': '-.', 'O': '---', 'P': '.--.', 'Q': '--.-', 'R': '.-.',
        'S': '...', 'T': '-', 'U': '..-', 'V': '...-', 'W': '.--', 'X': '-..-',
        'Y': '-.--', 'Z': '--..', ' ': '/'
    };

    const pattern = [];

    for (let char of message.toUpperCase()) {
        const morse = morseMap[char];
        if (!morse) continue;

        if (morse === '/') {
            pattern.push(-dashTime * 2); // Word gap
        } else {
            for (let symbol of morse) {
                if (symbol === '.') {
                    pattern.push(dotTime, -gapTime);
                } else if (symbol === '-') {
                    pattern.push(dashTime, -gapTime);
                }
            }
            pattern.push(-dashTime); // Letter gap
        }
    }

    return "RAW_Data: " + pattern.join(" ");
}

function frequencySweep() {
    const startFreq = parseInt(keyboard.numKeyboard("Start freq (Hz):", "433900000"));
    const endFreq = parseInt(keyboard.numKeyboard("End freq (Hz):", "433950000"));
    const steps = parseInt(keyboard.numKeyboard("Number of steps:", "10"));

    if (isNaN(startFreq) || isNaN(endFreq) || isNaN(steps) || startFreq >= endFreq) {
        dialog.error("Invalid parameters");
        return;
    }

    const testPattern = "RAW_Data: 1000 -1000 500 -500 1000 -1000";
    const stepSize = (endFreq - startFreq) / (steps - 1);

    for (let i = 0; i < steps; i++) {
        const freq = startFreq + (i * stepSize);

        display.fill(0);
        display.print(0, 0, "Frequency Sweep", 2);
        display.print(0, 25, `Step ${i + 1}/${steps}`, 1);
        display.print(0, 40, `${(freq / 1000000).toFixed(3)} MHz`, 1);

        console.log(`Sweep step ${i + 1}: ${freq} Hz`);

        if (subghz.transmit(Math.round(freq), testPattern)) {
            console.log(`  Transmitted successfully`);
        } else {
            console.log(`  Transmission failed`);
        }

        delay(1000);
    }

    dialog.success("Frequency sweep completed");
}
```

### Signal Analysis and Utilities

```javascript
function analyzeSignal(signal) {
    const analysis = performSignalAnalysis(signal);

    const report = `Signal Analysis Report

=== Basic Information ===
Frequency: ${(signal.frequency / 1000000).toFixed(3)} MHz
Protocol: ${signal.protocol}
RSSI: ${signal.rssi.toFixed(1)} dBm

=== Timing Analysis ===
Total Duration: ${analysis.totalDuration} μs
Number of Transitions: ${analysis.transitionCount}
Average High Time: ${analysis.avgHighTime.toFixed(1)} μs
Average Low Time: ${analysis.avgLowTime.toFixed(1)} μs
Duty Cycle: ${analysis.dutyCycle.toFixed(1)}%

=== Pattern Analysis ===
Shortest Pulse: ${analysis.minPulse} μs
Longest Pulse: ${analysis.maxPulse} μs
Most Common Pulse: ${analysis.commonPulse} μs
Detected Patterns: ${analysis.patterns.length}

=== Quality Assessment ===
Signal Clarity: ${analysis.quality.clarity}
Timing Consistency: ${analysis.quality.consistency}
Recommended Actions: ${analysis.recommendations.join(', ')}`;

    dialog.viewText(report, "Signal Analysis");
}

function performSignalAnalysis(signal) {
    // Parse raw data
    const rawData = signal.rawData.replace('RAW_Data: ', '');
    const pulses = rawData.split(' ').map(p => parseInt(p)).filter(p => !isNaN(p));

    if (pulses.length === 0) {
        return { error: "No valid pulse data found" };
    }

    const analysis = {
        transitionCount: pulses.length,
        totalDuration: pulses.reduce((sum, p) => sum + Math.abs(p), 0),
        highPulses: pulses.filter(p => p > 0),
        lowPulses: pulses.filter(p => p < 0).map(p => Math.abs(p)),
        patterns: []
    };

    // Calculate averages
    analysis.avgHighTime = analysis.highPulses.length > 0 ?
        analysis.highPulses.reduce((sum, p) => sum + p, 0) / analysis.highPulses.length : 0;

    analysis.avgLowTime = analysis.lowPulses.length > 0 ?
        analysis.lowPulses.reduce((sum, p) => sum + p, 0) / analysis.lowPulses.length : 0;

    analysis.dutyCycle = (analysis.avgHighTime / (analysis.avgHighTime + analysis.avgLowTime)) * 100;

    // Find min/max pulses
    const allPulsesAbs = pulses.map(p => Math.abs(p));
    analysis.minPulse = Math.min(...allPulsesAbs);
    analysis.maxPulse = Math.max(...allPulsesAbs);

    // Find most common pulse (simplified)
    const pulseCounts = {};
    allPulsesAbs.forEach(p => {
        const rounded = Math.round(p / 100) * 100; // Group by 100μs
        pulseCounts[rounded] = (pulseCounts[rounded] || 0) + 1;
    });

    analysis.commonPulse = parseInt(Object.keys(pulseCounts).reduce((a, b) =>
        pulseCounts[a] > pulseCounts[b] ? a : b));

    // Quality assessment
    analysis.quality = assessSignalQuality(analysis);
    analysis.recommendations = generateRecommendations(analysis);

    return analysis;
}

function assessSignalQuality(analysis) {
    const quality = {};

    // Signal clarity based on pulse variation
    const pulseVariation = analysis.maxPulse / analysis.minPulse;
    if (pulseVariation < 2) {
        quality.clarity = "Excellent";
    } else if (pulseVariation < 5) {
        quality.clarity = "Good";
    } else if (pulseVariation < 10) {
        quality.clarity = "Fair";
    } else {
        quality.clarity = "Poor";
    }

    // Timing consistency based on duty cycle
    if (analysis.dutyCycle > 30 && analysis.dutyCycle < 70) {
        quality.consistency = "Good";
    } else if (analysis.dutyCycle > 20 && analysis.dutyCycle < 80) {
        quality.consistency = "Fair";
    } else {
        quality.consistency = "Poor";
    }

    return quality;
}

function generateRecommendations(analysis) {
    const recommendations = [];

    if (analysis.quality.clarity === "Poor") {
        recommendations.push("Check signal source and reduce interference");
    }

    if (analysis.quality.consistency === "Poor") {
        recommendations.push("Verify timing accuracy and transmission stability");
    }

    if (analysis.transitionCount < 10) {
        recommendations.push("Signal may be too short for reliable transmission");
    }

    if (analysis.totalDuration > 100000) {
        recommendations.push("Consider splitting long signals into segments");
    }

    if (recommendations.length === 0) {
        recommendations.push("Signal appears suitable for transmission");
    }

    return recommendations;
}

function signalUtilities() {
    while (true) {
        const choice = dialog.choice([
            "Signal Converter",
            "Frequency Calculator",
            "Timing Optimizer",
            "Protocol Detector",
            "Signal Generator",
            "Exit"
        ]);

        switch (choice) {
            case 0:
                signalConverter();
                break;
            case 1:
                frequencyCalculator();
                break;
            case 2:
                timingOptimizer();
                break;
            case 3:
                protocolDetector();
                break;
            case 4:
                signalGenerator();
                break;
            case 5:
                return;
        }
    }
}

function frequencyCalculator() {
    const input = keyboard.keyboard("Frequency (MHz or Hz):", "433.92");
    if (!input) return;

    let freq;
    if (input.includes('.')) {
        // MHz input
        freq = parseFloat(input) * 1000000;
    } else {
        // Hz input
        freq = parseInt(input);
    }

    if (isNaN(freq)) {
        dialog.error("Invalid frequency");
        return;
    }

    const wavelength = 299792458 / freq; // c / f
    const period = 1 / freq;

    const info = `Frequency Analysis:

Input: ${input}
Frequency: ${freq.toLocaleString()} Hz
Frequency: ${(freq / 1000000).toFixed(3)} MHz
Wavelength: ${wavelength.toFixed(3)} m
Period: ${(period * 1000000).toFixed(3)} μs

Common Bands:
${freq >= 315000000 && freq <= 315250000 ? '✓' : '○'} 315 MHz (US/Canada)
${freq >= 433050000 && freq <= 434790000 ? '✓' : '○'} 433 MHz (ISM Global)
${freq >= 868000000 && freq <= 868600000 ? '✓' : '○'} 868 MHz (Europe)
${freq >= 902000000 && freq <= 928000000 ? '✓' : '○'} 915 MHz (US/Canada)`;

    dialog.viewText(info, "Frequency Analysis");
}
```

## Technical Specifications

### Frequency Ranges

- **315 MHz**: 315.000 - 315.250 MHz (US/Canada)
- **433 MHz**: 433.050 - 434.790 MHz (ISM Band, Global)
- **868 MHz**: 868.000 - 868.600 MHz (Europe)
- **915 MHz**: 902.000 - 928.000 MHz (US/Canada)

### Supported Protocols

- **OOK** (On-Off Keying)
- **ASK** (Amplitude Shift Keying)
- **FSK** (Frequency Shift Keying)
- **MSK** (Minimum Shift Keying)

### Signal Format

Sub-GHz files typically use Flipper Zero format:

```text
Filetype: Flipper SubGhz RAW File
Version: 1
Frequency: 433920000
Preset: FuriHalSubGhzPresetOok650Async
Protocol: RAW
RAW_Data: 1000 -1000 500 -500 ...
```
