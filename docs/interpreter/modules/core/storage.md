# Storage

The Storage module provides file system operations and data persistence.

## Path Object

Represents a file path along with its storage location.

### Example

```js linenums="1"
var dialog = require("dialog");

dialog.pickFile({ fs: "user", path: "/" });
```

### Properties

| Property | Type                              | Description                                      |
| -------- | --------------------------------- | ------------------------------------------------ |
| `fs`     | [`FileSystem`](#filesystem-type)  | The storage medium where the file is located     |
| `path`   | `string`                          | The file path within the selected storage medium |

## FileSystem Type

```ts
type FileSystem = "sd" | "littlefs" | null;
```

Represents the storage medium where a file is located.

* `'sd'` - File stored on SD card.
* `'littlefs'` - File stored on LittleFS.
* `null` - Automatically choose between SD card (if available) and LittleFS as a fallback.

## File Operations

### `storage.read(path, binary)`

Reads file contents.

**Parameters:**

| Parameter | Type             | Description                                   |
| --------- | ---------------- | --------------------------------------------- |
| `path`    | `string/object`  | File path or path object with filesystem info |
| `binary`  | `boolean`        | Read as binary data if true                   |

**Returns:** `string/Uint8Array` - File contents

**Example:**

```javascript linenums="1"
var storage = require("storage");
// Read text file
var content = storage.read("/config.txt");
console.log("Config:", content);

// Read binary file
var data = storage.read("/image.jpg", true);
console.log("File size:", data.length);
```

### `storage.write(path, data, mode, position)`

Writes data to a file.

**Parameters:**

| Parameter  | Type                  | Description                                    |
| ---------- | --------------------- | ---------------------------------------------- |
| `path`     | `string/object`       | File path                                      |
| `data`     | `string/Uint8Array`   | Data to write                                  |
| `mode`     | `string`              | Write mode ("w" for overwrite, "a" for append) |
| `position` | `number`              | Write position in file                         |

**Returns:** `boolean` - Success status

**Example:**

```javascript
// Write text file
storage.write("/log.txt", "Hello World\\n", "w");

// Append to file
storage.write("/log.txt", "New entry\\n", "a");

// Write JSON data
const config = { setting1: true, value: 42 };
storage.write("/config.json", JSON.stringify(config, null, 2));
```

## Directory Operations

### `storage.readdir(path, options)`

Lists directory contents.

**Parameters:**

| Parameter | Type             | Description           |
| --------- | ---------------- | --------------------- |
| `path`    | `string/object`  | Directory path        |
| `options` | `object`         | Options for listing   |

**Returns:** `array` - Array of file/directory entries

**Example:**

```javascript
const files = storage.readdir("/");
files.forEach(file => {
    console.log(`${file.name} (${file.isDirectory ? 'DIR' : 'FILE'})`);
});
```

### `storage.mkdir(path)`

Creates a directory.

**Parameters:**

| Parameter | Type             | Description               |
| --------- | ---------------- | ------------------------- |
| `path`    | `string/object`  | Directory path to create  |

**Returns:** `boolean` - Success status

**Example:**

```javascript
var storage = require("storage");

if (storage.mkdir("/data")) {
    console.log("Directory created successfully");
}
```

### `storage.rmdir(path)`

Removes an empty directory.

**Parameters:**

| Parameter | Type             | Description               |
| --------- | ---------------- | ------------------------- |
| `path`    | `string/object`  | Directory path to remove  |

**Returns:** `boolean` - Success status

**Example:**

```javascript
storage.rmdir("/temp");
```

## File Management

### `storage.rename(oldPath, newPath)`

Renames or moves a file/directory.

**Parameters:**

| Parameter | Type             | Description  |
| --------- | ---------------- | ------------ |
| `oldPath` | `string/object`  | Current path |
| `newPath` | `string/object`  | New path     |

**Returns:** `boolean` - Success status

**Example:**

```javascript
// Rename file
storage.rename("/old_name.txt", "/new_name.txt");

// Move file to different directory
storage.rename("/temp/file.txt", "/data/file.txt");
```

### `storage.remove(path)`

Deletes a file.

**Parameters:**

| Parameter | Type             | Description           |
| --------- | ---------------- | --------------------- |
| `path`    | `string/object`  | File path to delete   |

**Returns:** `boolean` - Success status

**Example:**

```javascript
var storage = require("storage");

if (storage.remove("/unwanted.tmp")) {
    console.log("File deleted");
}
```

## Storage Information

### `storage.spaceLittleFS()`

Returns LittleFS filesystem space information.

**Parameters:** None

**Returns:** `object` - Space usage information

**Example:**

```javascript
const space = storage.spaceLittleFS();
console.log(`Used: ${space.used} bytes`);
console.log(`Free: ${space.free} bytes`);
console.log(`Total: ${space.total} bytes`);
```

### `storage.spaceSDCard()`

Returns SD card space information.

**Parameters:** None

**Returns:** `object` - Space usage information

**Example:**

```javascript
const sdSpace = storage.spaceSDCard();
if (sdSpace) {
    console.log(`SD Card - Used: ${sdSpace.used}, Free: ${sdSpace.free}`);
} else {
    console.log("No SD card detected");
}
```

## Path Objects

Many storage functions accept path objects for filesystem specification:

```javascript
const pathObj = {
    filesystem: "littlefs", // or "sdcard"
    path: "/data/config.json"
};

storage.read(pathObj);
```

## Usage Examples

```javascript
var storage = require("storage");

// Configuration file manager
class ConfigManager {
    constructor(filename = "/config.json") {
        this.filename = filename;
        this.config = {};
        this.load();
    }

    load() {
        try {
            const data = storage.read(this.filename);
            this.config = JSON.parse(data);
        } catch (error) {
            console.log("Config file not found or invalid, using defaults");
            this.config = {};
        }
    }

    save() {
        const data = JSON.stringify(this.config, null, 2);
        return storage.write(this.filename, data, "w");
    }

    get(key, defaultValue = null) {
        return this.config[key] !== undefined ? this.config[key] : defaultValue;
    }

    set(key, value) {
        this.config[key] = value;
        return this.save();
    }

    remove(key) {
        delete this.config[key];
        return this.save();
    }
}

// Usage
const config = new ConfigManager();
config.set("username", "bruce");
config.set("brightness", 75);

console.log("Username:", config.get("username"));

// Data logger
class DataLogger {
    constructor(filename = "/data.log") {
        this.filename = filename;
        this.ensureDirectory();
    }

    ensureDirectory() {
        const dir = this.filename.substring(0, this.filename.lastIndexOf('/'));
        if (dir && dir !== '') {
            storage.mkdir(dir);
        }
    }

    log(message) {
        const timestamp = new Date(now()).toISOString();
        const logLine = `[${timestamp}] ${message}\\n`;

        return storage.write(this.filename, logLine, "a");
    }

    read() {
        try {
            return storage.read(this.filename);
        } catch (error) {
            return "";
        }
    }

    clear() {
        return storage.write(this.filename, "", "w");
    }

    size() {
        try {
            const content = storage.read(this.filename);
            return content.length;
        } catch (error) {
            return 0;
        }
    }
}

// Usage
const logger = new DataLogger("/logs/system.log");
logger.log("System started");
logger.log("Temperature: 25.3°C");

// File browser
function browseDirectory(path = "/") {
    try {
        const entries = storage.readdir(path);

        console.log(`Directory: ${path}`);
        console.log("========================");

        // Sort: directories first, then files
        entries.sort((a, b) => {
            if (a.isDirectory && !b.isDirectory) return -1;
            if (!a.isDirectory && b.isDirectory) return 1;
            return a.name.localeCompare(b.name);
        });

        entries.forEach(entry => {
            const icon = entry.isDirectory ? "📁" : "📄";
            const size = entry.size ? ` (${formatBytes(entry.size)})` : "";
            console.log(`${icon} ${entry.name}${size}`);
        });

    } catch (error) {
        console.log("Error reading directory:", error.message);
    }
}

function formatBytes(bytes) {
    if (bytes === 0) return "0 B";
    const k = 1024;
    const sizes = ["B", "KB", "MB", "GB"];
    const i = Math.floor(Math.log(bytes) / Math.log(k));
    return parseFloat((bytes / Math.pow(k, i)).toFixed(2)) + " " + sizes[i];
}

// Backup utility
function backupFile(sourceFile, backupDir = "/backups") {
    try {
        // Ensure backup directory exists
        storage.mkdir(backupDir);

        // Read source file
        const data = storage.read(sourceFile, true);

        // Create backup filename with timestamp
        const timestamp = new Date(now()).toISOString().replace(/[:.]/g, "-");
        const filename = sourceFile.split("/").pop();
        const backupPath = `${backupDir}/${filename}.${timestamp}.bak`;

        // Write backup
        if (storage.write(backupPath, data, "w")) {
            console.log(`Backup created: ${backupPath}`);
            return backupPath;
        } else {
            console.log("Backup failed");
            return null;
        }
    } catch (error) {
        console.log("Backup error:", error.message);
        return null;
    }
}

// Disk space monitor
function checkDiskSpace() {
    const littlefs = storage.spaceLittleFS();
    const sdcard = storage.spaceSDCard();

    console.log("=== Disk Space ===");

    // LittleFS (internal flash)
    const lfsUsedPercent = (littlefs.used / littlefs.total * 100).toFixed(1);
    console.log(`Internal: ${formatBytes(littlefs.used)} / ${formatBytes(littlefs.total)} (${lfsUsedPercent}%)`);

    if (lfsUsedPercent > 90) {
        console.log("⚠️  Internal storage is almost full!");
    }

    // SD Card
    if (sdcard) {
        const sdUsedPercent = (sdcard.used / sdcard.total * 100).toFixed(1);
        console.log(`SD Card: ${formatBytes(sdcard.used)} / ${formatBytes(sdcard.total)} (${sdUsedPercent}%)`);
    } else {
        console.log("SD Card: Not detected");
    }
}

// File cleanup utility
function cleanupOldFiles(directory, maxAge = 7 * 24 * 60 * 60 * 1000) { // 7 days in ms
    try {
        const files = storage.readdir(directory);
        const currentTime = now();
        let deletedCount = 0;

        files.forEach(file => {
            if (!file.isDirectory && file.lastModified) {
                const age = currentTime - file.lastModified;

                if (age > maxAge) {
                    const fullPath = `${directory}/${file.name}`;
                    if (storage.remove(fullPath)) {
                        console.log(`Deleted old file: ${file.name}`);
                        deletedCount++;
                    }
                }
            }
        });

        console.log(`Cleanup complete. Deleted ${deletedCount} files.`);
        return deletedCount;

    } catch (error) {
        console.log("Cleanup error:", error.message);
        return 0;
    }
}
```
