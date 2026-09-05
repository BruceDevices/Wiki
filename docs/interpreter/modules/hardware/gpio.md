# GPIO

The GPIO module provides general purpose input/output pin control and hardware interfacing.

## Functions

### `gpio.pinMode(pin, mode, pull)`

Sets the pin mode and pull configuration.

**Parameters:**

| Parameter | Type                | Description                                                                      |
| --------- | ------------------- | -------------------------------------------------------------------------------- |
| `pin`     | `number/string`     | Pin number or name                                                               |
| `mode`    | `string/number`     | Pin mode ("INPUT", "OUTPUT", "INPUT_PULLUP", "INPUT_PULLDOWN" or numeric values) |
| `pull`    | `string, optional`  | Pull resistor configuration                                                      |

**Returns:** `undefined`

**Example:**

```javascript
var gpio = require("gpio");

gpio.pinMode(2, "OUTPUT");
gpio.pinMode(4, "INPUT_PULLUP");
```

### `gpio.digitalWrite(pin, value)`

Writes a digital value to a pin.

**Parameters:**

| Parameter | Type             | Description                           |
| --------- | ---------------- | ------------------------------------- |
| `pin`     | `number/string`  | Pin number                            |
| `value`   | `boolean`        | Digital value (true/false or 1/0)     |

**Returns:** `undefined`

**Example:**

```javascript
var gpio = require("gpio");

gpio.digitalWrite(2, true);  // Set pin high
gpio.digitalWrite(2, false); // Set pin low
```

### `gpio.digitalRead(pin)`

Reads the digital value from a pin.

**Parameters:**

| Parameter | Type     | Description |
| --------- | -------- | ----------- |
| `pin`     | `number` | Pin number  |

**Returns:** `boolean` - Pin state (true/false)

**Example:**

```javascript
var gpio = require("gpio");

const state = gpio.digitalRead(4);
if (state) {
    console.log("Pin is HIGH");
}
```

### `gpio.analogWrite(pin, value)`

Writes an analog value (PWM) to a pin.

**Parameters:**

| Parameter | Type     | Description                        |
| --------- | -------- | ---------------------------------- |
| `pin`     | `number` | Pin number                         |
| `value`   | `number` | Analog value (typically 0-255)     |

**Returns:** `undefined`

**Example:**

```javascript
var gpio = require("gpio");

gpio.analogWrite(5, 128); // 50% duty cycle
```

### `gpio.analogRead(pin)`

Reads an analog value from a pin.

**Parameters:**

| Parameter | Type     | Description |
| --------- | -------- | ----------- |
| `pin`     | `number` | Pin number  |

**Returns:** `number` - Analog value (0-4095 for ESP32)

**Example:**

```javascript
var gpio = require("gpio");

const value = gpio.analogRead(36);
console.log("Analog value:", value);
```

### `gpio.touchRead(pin)`

Reads a capacitive touch sensor value.

**Parameters:**

| Parameter | Type     | Description                |
| --------- | -------- | -------------------------- |
| `pin`     | `number` | Touch-capable pin number   |

**Returns:** `number` - Touch value

**Example:**

```javascript
var gpio = require("gpio");

const touch = gpio.touchRead(4);
if (touch < 40) {
    console.log("Touch detected!");
}
```

### `gpio.dacWrite(pin, value)`

Writes to a Digital-to-Analog Converter pin.

**Parameters:**

| Parameter | Type     | Description         |
| --------- | -------- | ------------------- |
| `pin`     | `number` | DAC pin number      |
| `value`   | `number` | DAC value (0-255)   |

**Returns:** `undefined`

**Example:**

```javascript
var gpio = require("gpio");

gpio.dacWrite(25, 128); // Output ~1.65V on DAC1
```

## LEDC (LED Control) Functions

### `gpio.ledcSetup(channel, frequency, resolution)`

Sets up an LEDC channel.

**Parameters:**

| Parameter    | Type     | Description              |
| ------------ | -------- | ------------------------ |
| `channel`    | `number` | LEDC channel (0-15)      |
| `frequency`  | `number` | PWM frequency in Hz      |
| `resolution` | `number` | Bit resolution (1-16)    |

**Returns:** `undefined`

**Example:**

```javascript
var gpio = require("gpio");

gpio.ledcSetup(0, 1000, 8); // Channel 0, 1kHz, 8-bit resolution
```

### `gpio.ledcAttachPin(pin, channel)`

Attaches a pin to an LEDC channel.

**Parameters:**

| Parameter | Type     | Description   |
| --------- | -------- | ------------- |
| `pin`     | `number` | Pin number    |
| `channel` | `number` | LEDC channel  |

**Returns:** `undefined`

**Example:**

```javascript
gpio.ledcAttachPin(2, 0); // Attach pin 2 to channel 0
```

### `gpio.ledcWrite(channel, value)`

Writes a value to an LEDC channel.

**Parameters:**

| Parameter | Type     | Description   |
| --------- | -------- | ------------- |
| `channel` | `number` | LEDC channel  |
| `value`   | `number` | PWM value     |

**Returns:** `undefined`

**Example:**

```javascript
gpio.ledcWrite(0, 128); // 50% duty cycle on channel 0
```

## Utility Functions

### `gpio.pins()`

Returns the pin configuration object for the current board.

**Parameters:** None

**Returns:** `object` - Pin mapping and configuration

**Example:**

```javascript
const pins = gpio.pins();
console.log("Available pins:", Object.keys(pins));
```

## Usage Examples

```javascript
// LED control with PWM
function setupLED() {
    gpio.ledcSetup(0, 1000, 8);     // Setup channel 0
    gpio.ledcAttachPin(2, 0);       // Attach LED to pin 2

    // Fade LED in and out
    for (let i = 0; i <= 255; i += 5) {
        gpio.ledcWrite(0, i);
        delay(20);
    }
    for (let i = 255; i >= 0; i -= 5) {
        gpio.ledcWrite(0, i);
        delay(20);
    }
}

// Button input with debouncing
function readButton(pin) {
    gpio.pinMode(pin, "INPUT_PULLUP");

    let lastState = true;
    let debounceTime = 0;

    return function() {
        const currentState = gpio.digitalRead(pin);
        const currentTime = now();

        if (currentState !== lastState &&
            currentTime - debounceTime > 50) {
            debounceTime = currentTime;
            lastState = currentState;
            return !currentState; // Inverted due to pullup
        }
        return false;
    };
}

// Analog sensor monitoring
function monitorSensor() {
    gpio.pinMode(36, "INPUT");

    setInterval(() => {
        const value = gpio.analogRead(36);
        const voltage = (value / 4095.0) * 3.3;

        console.log(`Sensor: ${value} (${voltage.toFixed(2)}V)`);

        // Trigger action based on threshold
        if (voltage > 2.5) {
            notification.blink(200);
        }
    }, 1000);
}

// Touch interface
function setupTouchPads() {
    const touchPins = [4, 12, 13, 14];

    touchPins.forEach((pin, index) => {
        gpio.pinMode(pin, "INPUT");
    });

    setInterval(() => {
        touchPins.forEach((pin, index) => {
            const value = gpio.touchRead(pin);
            if (value < 40) {
                console.log(`Touch pad ${index + 1} activated`);
                // Handle touch event
            }
        });
    }, 100);
}
```

## Pin Mapping Notes

- Pin availability varies by board model
- Some pins have special functions (boot pins, etc.)
- Check your specific board documentation for pin capabilities
- Use `gpio.pins()` to get board-specific pin information
