# Microphone

The Microphone module provides audio recording functionality for capturing sound input through the device's built-in microphone.

## Functions

### `mic.recordWav()`

Starts audio recording from the microphone and returns the recorded audio data.

**Parameters:** None

**Returns:** `ArrayBuffer` - Raw audio data or `null` if recording failed

**Example:**

```javascript linenums="1"
var mic = require("mic");
var storage = require("storage");
var dialog = require("dialog");

// Basic audio recording
var audioData = mic.record();

if (audioData) {
    console.log("Recording successful!");
    console.log("Audio data length:", audioData.byteLength, "bytes");

    // Save to file
    storage.write("audio/recording.wav", audioData);
    dialog.info("Audio saved to audio/recording.wav");
} else {
    dialog.error("Recording failed!");
}
```
