# WiFi

The WiFi module provides WiFi connectivity and HTTP request functionality.

## Connection Management

### `wifi.connected()`

Checks if WiFi is currently connected.

**Parameters:** None

**Returns:** `boolean` - Connection status

**Example:**

```javascript
var wifi = require("wifi");

if (wifi.connected()) {
    console.log("WiFi is connected");
} else {
    console.log("No WiFi connection");
}
```

### `wifi.connect(ssid, timeout, password)`

Connects to a WiFi network.

**Parameters:**

| Parameter  | Type     | Description                     |
| ---------- | -------- | ------------------------------- |
| `ssid`     | `string` | Network name                    |
| `timeout`  | `number` | Connection timeout in seconds   |
| `password` | `string` | Network password                |

**Returns:** `boolean` - Success status

**Example:**

```javascript
var wifi = require("wifi");

const success = wifi.connect("MyNetwork", 30, "password123");
if (success) {
    console.log("Connected to WiFi");
}
```

### `wifi.connectDialog()`

Shows WiFi connection dialog for user to select network.

**Parameters:** None

**Returns:** `boolean` - Success status

**Example:**

```javascript
var wifi = require("wifi");

if (wifi.connectDialog()) {
    console.log("User connected to network");
}
```

### `wifi.disconnect()`

Disconnects from the current WiFi network.

**Parameters:** None

**Returns:** `undefined`

**Example:**

```javascript
var wifi = require("wifi");

wifi.disconnect();
```

### `wifi.scan()`

Scans for available WiFi networks.

**Parameters:** None

**Returns:** `array` - Array of network objects

**Example:**

```javascript
var wifi = require("wifi");

const networks = wifi.scan();
networks.forEach(network => {
    console.log(`SSID: ${network.ssid}, Signal: ${network.rssi}dBm`);
});
```

## HTTP Requests

### `wifi.httpFetch(url, options)`

Performs an HTTP request.

**Parameters:**

| Parameter | Type     | Description                                        |
| --------- | -------- | -------------------------------------------------- |
| `url`     | `string` | Request URL                                        |
| `options` | `object` | Request options including method, headers, body    |

**Returns:** `object` - Response object with status, headers, body

**Example:**

```javascript
var wifi = require("wifi");

// GET request
const response = wifi.httpFetch("https://api.example.com/data");
if (response.status === 200) {
    const data = JSON.parse(response.body);
    console.log("Data received:", data);
}

// POST request
const postResponse = wifi.httpFetch("https://api.example.com/submit", {
    method: "POST",
    headers: {
        "Content-Type": "application/json"
    },
    body: JSON.stringify({
        name: "Bruce",
        value: 42
    })
});
```

## Network Information

### `wifi.macAddress()`

Returns the device MAC address.

**Parameters:** None

**Returns:** `string` - MAC address

**Example:**

```javascript
var wifi = require("wifi");

const mac = wifi.macAddress();
console.log("MAC Address:", mac);
```

### `wifi.ipAddress()`

Returns the current IP address.

**Parameters:** None

**Returns:** `string` - IP address

**Example:**

```javascript
var wifi = require("wifi");

if (wifi.connected()) {
    const ip = wifi.ipAddress();
    console.log("IP Address:", ip);
}
```

## Usage Examples

```javascript
var wifi = require("wifi");

// Automatic WiFi connection with retry
function connectWiFi(ssid, password, maxRetries = 3) {
    for (let i = 0; i < maxRetries; i++) {
        console.log(`Connection attempt ${i + 1}...`);

        if (wifi.connect(ssid, 30, password)) {
            console.log("Connected successfully!");
            console.log("IP:", wifi.ipAddress());
            return true;
        }

        delay(2000); // Wait before retry
    }

    console.log("Failed to connect after", maxRetries, "attempts");
    return false;
}

// Network scanner with signal strength
function scanAndDisplayNetworks() {
    console.log("Scanning for networks...");
    const networks = wifi.scan();

    // Sort by signal strength
    networks.sort((a, b) => b.rssi - a.rssi);

    console.log("Available networks:");
    networks.forEach((network, index) => {
        const bars = getSignalBars(network.rssi);
        console.log(`${index + 1}. ${network.ssid} ${bars} (${network.rssi}dBm)`);
    });
}

function getSignalBars(rssi) {
    if (rssi > -50) return "████";
    if (rssi > -60) return "███ ";
    if (rssi > -70) return "██  ";
    if (rssi > -80) return "█   ";
    return "    ";
}

// HTTP API client
class APIClient {
    constructor(baseURL) {
        this.baseURL = baseURL;
    }

    request(endpoint, options = {}) {
        const url = this.baseURL + endpoint;
        return wifi.httpFetch(url, options);
    }

    get(endpoint) {
        return this.request(endpoint, { method: "GET" });
    }

    post(endpoint, data) {
        return this.request(endpoint, {
            method: "POST",
            headers: { "Content-Type": "application/json" },
            body: JSON.stringify(data)
        });
    }
}

// Usage
const api = new APIClient("https://jsonplaceholder.typicode.com");

if (wifi.connected()) {
    const posts = api.get("/posts/1");
    if (posts.status === 200) {
        const data = JSON.parse(posts.body);
        console.log("Post title:", data.title);
    }
}

// Weather station example
function getWeather(city) {
    if (!wifi.connected()) {
        console.log("No internet connection");
        return null;
    }

    // Note: Replace with actual API key and service
    const url = `https://api.openweathermap.org/data/2.5/weather?q=${city}&appid=YOUR_API_KEY&units=metric`;

    try {
        const response = wifi.httpFetch(url);

        if (response.status === 200) {
            const weather = JSON.parse(response.body);
            return {
                temperature: weather.main.temp,
                humidity: weather.main.humidity,
                description: weather.weather[0].description,
                city: weather.name
            };
        } else {
            console.log("Weather API error:", response.status);
            return null;
        }
    } catch (error) {
        console.log("Network error:", error.message);
        return null;
    }
}

// Display weather on screen
function displayWeather(city) {
    const weather = getWeather(city);

    if (weather) {
        display.fill(display.color(0, 0, 0));
        display.setTextColor(display.color(255, 255, 255));
        display.setTextSize(2);

        display.drawString(`${weather.city}`, 10, 10);
        display.drawString(`${weather.temperature}°C`, 10, 40);
        display.drawString(`${weather.humidity}% humidity`, 10, 70);
        display.setTextSize(1);
        display.drawString(weather.description, 10, 100);
    } else {
        display.fill(display.color(255, 0, 0));
        display.setTextColor(display.color(255, 255, 255));
        display.drawString("Weather unavailable", 10, 50);
    }
}
```
