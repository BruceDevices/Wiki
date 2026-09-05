# Display

The Display module provides comprehensive graphics and text rendering capabilities for drawing on the device screen or creating sprite objects for off-screen rendering.

## Basic Display Functions

### `display.width()`

Returns the width of the display in pixels.

**Parameters:** None

**Returns:** `number` - Display width in pixels

### `display.height()`

Returns the height of the display in pixels.

**Parameters:** None

**Returns:** `number` - Display height in pixels

**Example:**

```javascript linenums="1"
var display = require("display");
var w = display.width();
var h = display.height();

console.log("Display size: " + w + "x" + h);

// Center a rectangle
var rectW = 60, rectH = 40;
var x = (w - rectW) / 2;
var y = (h - rectH) / 2;
display.drawRect(x, y, rectW, rectH, 0xFFFF);
```

## Color Functions

### `display.color(r, g, b, [mode])`

Creates a color value from RGB components.

**Parameters:**

| Parameter | Type                | Description                          |
| --------- | ------------------- | ------------------------------------ |
| `r`       | `number`            | Red component (0-255)                |
| `g`       | `number`            | Green component (0-255)              |
| `b`       | `number`            | Blue component (0-255)               |
| `mode`    | `number, optional`  | Color mode - 16 (default) or 8-bit   |

**Returns:** `number` - Color value for use in drawing functions

**Example:**

```javascript linenums="1"
var display = require("display");
// Create colors
var red = display.color(255, 0, 0);
var green = display.color(0, 255, 0);
var blue = display.color(0, 0, 255);
var yellow = display.color(255, 255, 0);
var purple = display.color(128, 0, 128);

// Use colors in drawing
display.fill(0);
display.drawFillRect(10, 10, 20, 20, red);
display.drawFillRect(35, 10, 20, 20, green);
display.drawFillRect(60, 10, 20, 20, blue);

// Common color constants
var WHITE = display.color(255, 255, 255);
var BLACK = display.color(0, 0, 0);
var GRAY = display.color(128, 128, 128);
```

## Drawing Functions

### `display.fill(color)`

Fills the entire screen with the specified color.

**Parameters:**

| Parameter | Type     | Description |
| --------- | -------- | ----------- |
| `color`   | `number` | Color value |

**Returns:** None

**Example:**

```javascript linenums="1"
var display = require("display");
var system = require("system");
// Different background colors
display.fill(0x0000); // Black
system.delay(1000);

display.fill(0xFFFF); // White
system.delay(1000);

display.fill(display.color(64, 64, 128)); // Dark blue
```

### `display.drawPixel(x, y, color)`

Draws a single pixel at the specified coordinates.

**Parameters:**

| Parameter | Type     | Description  |
| --------- | -------- | ------------ |
| `x`       | `number` | X coordinate |
| `y`       | `number` | Y coordinate |
| `color`   | `number` | Color value  |

**Returns:** None

**Example:**

```javascript linenums="1"
var display = require("display");
var system = require("system");
// Draw random pixels
display.fill(0);

for (var i = 0; i < 100; i++) {
    var x = system.random(display.width());
    var y = system.random(display.height());
    var color = display.color(system.random(256), system.random(256), system.random(256));

    display.drawPixel(x, y, color);
}

```

### Rectangle Drawing

#### `display.drawRect(x, y, w, h, color)`

Draws a rectangle outline.

**Parameters:**

| Parameter | Type     | Description                      |
| --------- | -------- | -------------------------------- |
| `x`       | `number` | X coordinate of top-left corner  |
| `y`       | `number` | Y coordinate of top-left corner  |
| `w`       | `number` | Width                            |
| `h`       | `number` | Height                           |
| `color`   | `number` | Color value                      |

#### `display.drawFillRect(x, y, w, h, color)`

Draws a filled rectangle.

#### `display.drawRoundRect(x, y, w, h, r, color)`

Draws a rounded rectangle outline.

**Parameters:**

| Parameter | Type     | Description                      |
| --------- | -------- | -------------------------------- |
| `x`       | `number` | X coordinate of top-left corner  |
| `y`       | `number` | Y coordinate of top-left corner  |
| `w`       | `number` | Width                            |
| `h`       | `number` | Height                           |
| `r`       | `number` | Corner radius                    |
| `color`   | `number` | Color value                      |

#### `display.fillRoundRect(x, y, w, h, r, color)`

Draws a filled rounded rectangle.

**Example:**

```javascript linenums="1"
var display = require("display");
display.fill(0);

// Rectangle outlines
display.drawRect(10, 10, 50, 30, 0xFFFF);
display.drawRoundRect(70, 10, 50, 30, 5, 0xFFFF);

// Filled rectangles
display.drawFillRect(10, 50, 50, 30, display.color(255, 0, 0));
display.fillRoundRect(70, 50, 50, 30, 5, display.color(0, 255, 0));

```

### Circle Drawing

#### `display.drawCircle(x, y, r, color)`

Draws a circle outline.

**Parameters:**

| Parameter | Type     | Description             |
| --------- | -------- | ----------------------- |
| `x`       | `number` | X coordinate of center  |
| `y`       | `number` | Y coordinate of center  |
| `r`       | `number` | Radius                  |
| `color`   | `number` | Color value             |

#### `display.fillCircle(x, y, r, color)`

Draws a filled circle.

**Example:**

```javascript linenums="1"
var display = require("display");
display.fill(0);

// Concentric circles
var centerX = display.width() / 2;
var centerY = display.height() / 2;

for (var r = 5; r < 40; r += 5) {
    var color = display.color(r * 6, 255 - r * 6, 128);
    display.drawCircle(centerX, centerY, r, color);
}

```

### Line Drawing

#### `display.drawLine(x0, y0, x1, y1, color)`

Draws a line between two points.

**Parameters:**

| Parameter | Type     | Description              |
| --------- | -------- | ------------------------ |
| `x0, y0`  | `number` | Start point coordinates  |
| `x1, y1`  | `number` | End point coordinates    |
| `color`   | `number` | Color value              |

#### `display.drawFastHLine(x, y, w, color)`

Draws a horizontal line (optimized).

**Parameters:**

| Parameter | Type     | Description  |
| --------- | -------- | ------------ |
| `x`       | `number` | X coordinate |
| `y`       | `number` | Y coordinate |
| `w`       | `number` | Width        |
| `color`   | `number` | Color value  |

#### `display.drawFastVLine(x, y, h, color)`

Draws a vertical line (optimized).

**Parameters:**

| Parameter | Type     | Description  |
| --------- | -------- | ------------ |
| `x`       | `number` | X coordinate |
| `y`       | `number` | Y coordinate |
| `h`       | `number` | Height       |
| `color`   | `number` | Color value  |

#### `display.drawWideLine(x0, y0, x1, y1, width, color)`

Draws a line with specified width.

**Parameters:**

| Parameter | Type     | Description              |
| --------- | -------- | ------------------------ |
| `x0, y0`  | `number` | Start point coordinates  |
| `x1, y1`  | `number` | End point coordinates    |
| `width`   | `number` | Line width               |
| `color`   | `number` | Color value              |

**Example:**

```javascript linenums="1"
var display = require("display");
display.fill(0);

// Star pattern
var centerX = display.width() / 2;
var centerY = display.height() / 2;
var white = display.color(255, 255, 255);

for (var angle = 0; angle < 360; angle += 30) {
    var rad = angle * Math.PI / 180;
    var x = centerX + Math.cos(rad) * 30;
    var y = centerY + Math.sin(rad) * 30;

    display.drawLine(centerX, centerY, x, y, white);
}

```

### Triangle Drawing

#### `display.drawTriangle(x0, y0, x1, y1, x2, y2, color)`

Draws a triangle outline.

**Parameters:**

| Parameter | Type     | Description               |
| --------- | -------- | ------------------------- |
| `x0, y0`  | `number` | First vertex coordinates  |
| `x1, y1`  | `number` | Second vertex coordinates |
| `x2, y2`  | `number` | Third vertex coordinates  |
| `color`   | `number` | Color value               |

#### `display.fillTriangle(x0, y0, x1, y1, x2, y2, color)`

Draws a filled triangle.

**Example:**

```javascript linenums="1"
var display = require("display");
display.fill(0);

// Triangle pattern
var red = display.color(255, 0, 0);
var green = display.color(0, 255, 0);
var blue = display.color(0, 0, 255);

display.fillTriangle(64, 10, 44, 40, 84, 40, red);
display.fillTriangle(54, 25, 74, 25, 64, 50, green);

```

### Advanced Drawing

#### `display.drawFillRectGradient(x, y, w, h, color1, color2, [mode])`

Draws a gradient-filled rectangle.

**Parameters:**

| Parameter      | Type               | Description                                      |
| -------------- | ------------------ | ------------------------------------------------ |
| `x, y, w, h`   | `number`           | Rectangle coordinates and size                   |
| `color1`       | `number`           | Start color                                      |
| `color2`       | `number`           | End color                                        |
| `mode`         | `string, optional` | "h" for horizontal (default) or "v" for vertical |

**Example:**

```javascript linenums="1"
var display = require("display");
display.fill(0);

var blue = display.color(0, 0, 255);
var red = display.color(255, 0, 0);

// Horizontal gradient
display.drawFillRectGradient(10, 10, 100, 20, blue, red, "h");

// Vertical gradient
display.drawFillRectGradient(10, 40, 100, 20, blue, red, "v");

```

#### `display.drawArc(x, y, r1, r2, start, end, color)`

Draws an arc segment.

**Parameters:**

| Parameter | Type     | Description                  |
| --------- | -------- | ---------------------------- |
| `x, y`    | `number` | Center coordinates           |
| `r1`      | `number` | Inner radius                 |
| `r2`      | `number` | Outer radius                 |
| `start`   | `number` | Start angle in degrees       |
| `end`     | `number` | End angle in degrees         |
| `color`   | `number` | Color value                  |

**Example:**

```javascript linenums="1"
var display = require("display");
display.fill(0);

var centerX = display.width() / 2;
var centerY = display.height() / 2;

// Pie chart segments
display.drawArc(centerX, centerY, 10, 30, 0, 120, display.color(255, 0, 0));
display.drawArc(centerX, centerY, 10, 30, 120, 240, display.color(0, 255, 0));
display.drawArc(centerX, centerY, 10, 30, 240, 360, display.color(0, 0, 255));

```

## Text Functions

### Text Properties

#### `display.setTextColor(color, [bgcolor])`

Sets the text color and optional background color.

**Parameters:**

| Parameter | Type               | Description         |
| --------- | ------------------ | ------------------- |
| `color`   | `number`           | Text color          |
| `bgcolor` | `number, optional` | Background color    |

#### `display.setTextSize(size)`

Sets the text size multiplier.

**Parameters:**

| Parameter | Type     | Description                       |
| --------- | -------- | --------------------------------- |
| `size`    | `number` | Size multiplier (1, 2, 3, etc.)   |

#### `display.setTextAlign(align, [baseline])`

Sets text alignment and baseline.

**Parameters:**

| Parameter  | Type              | Description                                                     |
| ---------- | ----------------- | --------------------------------------------------------------- |
| `align`    | `string\|number`  | "left"/"l"/0, "center"/"c"/1, "right"/"r"/2                     |
| `baseline` | `string\|number`  | "top"/"t"/0, "middle"/"m"/1, "bottom"/"b"/2, "alphabetic"/"a"/3 |

### Text Drawing

#### `display.drawString(text, x, y)`

Draws text at the specified position using current text settings.

**Parameters:**

| Parameter | Type     | Description           |
| --------- | -------- | --------------------- |
| `text`    | `string` | Text to draw          |
| `x, y`    | `number` | Position coordinates  |

#### `display.setCursor(x, y)`

Sets the cursor position for print functions.

**Parameters:**

| Parameter | Type     | Description        |
| --------- | -------- | ------------------ |
| `x, y`    | `number` | Cursor coordinates |

#### `display.print(text)`

Prints text at the current cursor position (set by `setCursor`).

**Parameters:**

| Parameter | Type     | Description   |
| --------- | -------- | ------------- |
| `text`    | `string` | Text to print |

#### `display.println(text)`

Prints text followed by a newline.

**Parameters:**

| Parameter | Type     | Description   |
| --------- | -------- | ------------- |
| `text`    | `string` | Text to print |

**Example:**

```javascript linenums="1"
var display = require("display");
display.fill(0);

// Different text sizes and colors
display.setTextSize(1);
display.setTextColor(display.color(255, 255, 255));
display.drawString("Size 1", 10, 10);

display.setTextSize(2);
display.setTextColor(display.color(255, 0, 0));
display.drawString("Size 2", 10, 25);

// Aligned text
display.setTextAlign("center", "middle");
display.setTextColor(display.color(0, 255, 0));
display.drawString("Centered", display.width()/2, display.height()/2);

// Print-style text
display.setTextAlign("left", "top");
display.setTextColor(display.color(255, 255, 0));
display.setCursor(10, 60);
display.print("Line 1");
display.setCursor(10, 70);
display.println("Line 2");

```

## Image Functions

### `display.drawXBitmap(x, y, bitmap, w, h, color, [bgcolor])`

Draws a monochrome bitmap from byte array data.

**Parameters:**

| Parameter | Type                | Description                   |
| --------- | ------------------- | ----------------------------- |
| `x, y`    | `number`            | Position coordinates          |
| `bitmap`  | `ArrayBuffer`       | Bitmap data                   |
| `w, h`    | `number`            | Width and height in pixels    |
| `color`   | `number`            | Foreground color              |
| `bgcolor` | `number, optional`  | Background color              |

**Example:**

```javascript linenums="1"
var display = require("display");
// Define a simple 8x8 smiley bitmap
var smiley = new Uint8Array([
    0x3C, 0x42, 0xA5, 0x81,
    0xA5, 0x99, 0x42, 0x3C
]);

display.fill(0);
display.drawXBitmap(10, 10, smiley.buffer, 8, 8,
                   display.color(255, 255, 0));
```

## Sprite Functions

Sprites are off-screen drawing surfaces that can be created, drawn to, and then rendered to the main display.

### `new display.Sprite(width, height)`

Creates a new sprite with the specified dimensions.

**Parameters:**

| Parameter | Type     | Description              |
| --------- | -------- | ------------------------ |
| `width`   | `number` | Sprite width in pixels   |
| `height`  | `number` | Sprite height in pixels  |

**Returns:** Sprite object with all display drawing methods

**Example:**

```javascript linenums="1"
var display = require("display");
// Create a sprite
var sprite = new display.Sprite(64, 32);

// Draw to sprite (same API as display)
sprite.drawFillRect(0, 0, 64, 32, display.color(255, 0, 0));
sprite.setTextColor(display.color(255, 255, 255));
sprite.setTextSize(1);
sprite.drawString("Sprite!", 5, 5);

// Draw sprite to display at position (20, 20)
display.fill(0);
sprite.pushToDisplay(20, 20);
```

## Display Applications

### Graphics Demo

```javascript linenums="1"
var display = require("display");
function graphicsDemo() {
    display.fill(0);

    // Title
    display.setTextColor(display.color(255, 255, 255));
    display.setTextSize(2);
    display.setTextAlign("center", "top");
    display.drawString("Graphics Demo", display.width()/2, 5);

    // Shapes demonstration
    var y = 30;

    // Rectangles
    display.drawFillRect(5, y, 20, 15, display.color(255, 0, 0));
    display.drawRect(30, y, 20, 15, display.color(0, 255, 0));

    // Circles
    display.fillCircle(65, y + 7, 7, display.color(0, 0, 255));
    display.drawCircle(85, y + 7, 7, display.color(255, 255, 0));

    // Lines
    display.drawLine(5, y + 25, 25, y + 35, display.color(255, 0, 255));
    display.drawWideLine(30, y + 25, 50, y + 35, 3, display.color(0, 255, 255));

    }

graphicsDemo();
```

### Animated Graphics

```javascript linenums="1"
var display = require("display");
var keyboard = require("keyboard");
var system = require("system");
function animatedClock() {
    var centerX = display.width() / 2;
    var centerY = display.height() / 2;
    var radius = Math.min(centerX, centerY) - 10;

    while (true) {
        if (keyboard.getEscPress()) break;

        display.fill(0);

        // Clock face
        display.drawCircle(centerX, centerY, radius,
                         display.color(255, 255, 255));

        // Hour marks
        for (var hour = 0; hour < 12; hour++) {
            var angle = (hour * 30 - 90) * Math.PI / 180;
            var x1 = centerX + Math.cos(angle) * (radius - 5);
            var y1 = centerY + Math.sin(angle) * (radius - 5);
            var x2 = centerX + Math.cos(angle) * (radius - 1);
            var y2 = centerY + Math.sin(angle) * (radius - 1);

            display.drawLine(x1, y1, x2, y2,
                           display.color(255, 255, 255));
        }

        // Clock hands (simplified - using seconds for animation)
        var time = system.now();
        var seconds = (time / 1000) % 60;
        var minutes = (time / 60000) % 60;
        var hours = (time / 3600000) % 12;

        // Second hand
        var secAngle = (seconds * 6 - 90) * Math.PI / 180;
        var secX = centerX + Math.cos(secAngle) * (radius - 10);
        var secY = centerY + Math.sin(secAngle) * (radius - 10);
        display.drawLine(centerX, centerY, secX, secY,
                        display.color(255, 0, 0));

        // Minute hand
        var minAngle = (minutes * 6 - 90) * Math.PI / 180;
        var minX = centerX + Math.cos(minAngle) * (radius - 15);
        var minY = centerY + Math.sin(minAngle) * (radius - 15);
        display.drawWideLine(centerX, centerY, minX, minY, 2,
                           display.color(0, 255, 0));

        // Hour hand
        var hourAngle = (hours * 30 + minutes * 0.5 - 90) * Math.PI / 180;
        var hourX = centerX + Math.cos(hourAngle) * (radius - 20);
        var hourY = centerY + Math.sin(hourAngle) * (radius - 20);
        display.drawWideLine(centerX, centerY, hourX, hourY, 3,
                           display.color(0, 0, 255));

        // Center dot
        display.fillCircle(centerX, centerY, 3,
                         display.color(255, 255, 255));

                system.delay(100);
    }
}
```

### Text Display System

```javascript linenums="1"
var display = require("display");
// Constructor function instead of class
function TextDisplay() {
    this.lines = [];
    this.maxLines = Math.floor(display.height() / 10);
    this.textColor = display.color(255, 255, 255);
    this.bgColor = display.color(0, 0, 0);
}

TextDisplay.prototype.addLine = function(text) {
    this.lines.push(text);
    if (this.lines.length > this.maxLines) {
        this.lines.shift(); // Remove oldest line
    }
};

TextDisplay.prototype.clear = function() {
    this.lines = [];
};

TextDisplay.prototype.render = function() {
    display.fill(this.bgColor);
    display.setTextColor(this.textColor);
    display.setTextSize(1);
    display.setTextAlign("left", "top");

    for (var i = 0; i < this.lines.length; i++) {
        display.drawString(this.lines[i], 2, i * 10 + 2);
    }

    };

// Usage
var textDisplay = new TextDisplay();

function logMessage(message) {
    textDisplay.addLine("[" + Date.now() + "] " + message);
    textDisplay.render();
}

logMessage("System started");
logMessage("Loading modules...");
logMessage("WiFi connecting...");
logMessage("Ready!");
```

### Data Visualization

```javascript linenums="1"
var display = require("display");
function drawBarChart(data, labels) {
    display.fill(0);

    var maxValue = Math.max.apply(Math, data);
    var barWidth = Math.floor(display.width() / data.length) - 2;
    var maxBarHeight = display.height() - 30;

    // Title
    display.setTextColor(display.color(255, 255, 255));
    display.setTextSize(1);
    display.setTextAlign("center", "top");
    display.drawString("Data Chart", display.width()/2, 5);

    // Draw bars
    for (var i = 0; i < data.length; i++) {
        var barHeight = (data[i] / maxValue) * maxBarHeight;
        var x = i * (barWidth + 2) + 2;
        var y = display.height() - barHeight - 15;

        // Generate color based on value
        var colorValue = Math.floor((data[i] / maxValue) * 255);
        var barColor = display.color(colorValue, 255 - colorValue, 128);

        display.drawFillRect(x, y, barWidth, barHeight, barColor);

        // Label
        display.setTextAlign("center", "top");
        display.drawString(labels[i], x + barWidth/2, display.height() - 12);

        // Value
        display.setTextAlign("center", "bottom");
        display.drawString(String(data[i]), x + barWidth/2, y - 2);
    }

    }

// Example usage
var sensorData = [23, 45, 67, 34, 56, 78];
var sensorLabels = ["A", "B", "C", "D", "E", "F"];
drawBarChart(sensorData, sensorLabels);
```

### Sprite Animation

```javascript linenums="1"
var display = require("display");
var keyboard = require("keyboard");
var system = require("system");
function createAnimatedSprite() {
    var sprite = new display.Sprite(32, 32);
    var frame = 0;

    while (true) {
        if (keyboard.getEscPress()) break;

        // Clear sprite
        sprite.drawFillRect(0, 0, 32, 32, display.color(0, 0, 0));

        // Animated circle
        var radius = 5 + Math.sin(frame * 0.2) * 3;
        var color = display.color(
            128 + Math.sin(frame * 0.1) * 127,
            128 + Math.cos(frame * 0.1) * 127,
            128 + Math.sin(frame * 0.15) * 127
        );

        sprite.fillCircle(16, 16, radius, color);

        // Move sprite around screen
        var x = (display.width() - 32) / 2 + Math.cos(frame * 0.05) * 30;
        var y = (display.height() - 32) / 2 + Math.sin(frame * 0.05) * 20;

        display.fill(0);
        sprite.pushToDisplay(x, y);

        frame++;
        system.delay(50);
    }
}
```
