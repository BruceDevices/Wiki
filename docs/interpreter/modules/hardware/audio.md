# Audio

The Audio module provides functionality for audio playback and sound generation.

## Functions

### `audio.playFile(filename)`

Plays an audio file from storage.

**Parameters:**

| Parameter  | Type     | Description            |
| ---------- | -------- | ---------------------- |
| `filename` | `string` | Path to the audio file |

**Returns:** `undefined`

**Example:**

```javascript
var audio = require("audio");

audio.playFile("/sounds/beep.wav");
```

### `audio.tone(frequency, duration, background)`

Generates a tone with specified frequency and duration.

**Parameters:**

| Parameter    | Type      | Description                                 |
| ------------ | --------- | ------------------------------------------- |
| `frequency`  | `number`  | Frequency in Hz (default: 500)              |
| `duration`   | `number`  | Duration in milliseconds (default: 1000)    |
| `background` | `boolean` | Play in background if true (default: false) |

**Returns:** `undefined`

**Example:**

```javascript
var audio = require("audio");

// Play a 1000Hz tone for 500ms
audio.tone(1000, 500);

// Play a tone in the background
audio.tone(800, 2000, true);
```
