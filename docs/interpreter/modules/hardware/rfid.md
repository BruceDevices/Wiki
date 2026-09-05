# RFID

The RFID module provides functionality for reading, writing, and managing RFID/NFC tags.

## Standard RFID Functions

### `rfid.read(timeout)`

Reads RFID tag data.

**Parameters:**

| Parameter | Type                  | Description             |
| --------- | --------------------- | ----------------------- |
| `timeout` | `number, default: 10` | Read timeout in seconds |

**Returns:** `object` - Tag data including UID, type, and content

**Example:**

```javascript
var rfid = require("rfid");

const tag = rfid.read(15);
if (tag) {
    console.log("UID:", tag.uid);
    console.log("Type:", tag.type);
    console.log("Data:", tag.data);
}
```

### `rfid.readUID(timeout)`

Reads only the UID of an RFID tag.

**Parameters:**

| Parameter | Type                 | Description             |
| --------- | -------------------- | ----------------------- |
| `timeout` | `number, default: 5` | Read timeout in seconds |

**Returns:** `string` - Tag UID in hexadecimal format

**Example:**

```javascript
var rfid = require("rfid");

const uid = rfid.readUID(10);
if (uid) {
    console.log("Tag UID:", uid);
}
```

### `rfid.write(data, timeout)`

Writes data to an RFID tag.

**Parameters:**

| Parameter | Type                  | Description              |
| --------- | --------------------- | ------------------------ |
| `data`    | `object/string`       | Data to write to the tag |
| `timeout` | `number, default: 10` | Write timeout in seconds |

**Returns:** `boolean` - Success status

**Example:**

```javascript
var rfid = require("rfid");

const success = rfid.write({
    type: "text",
    content: "Hello RFID World!"
}, 15);

if (success) {
    console.log("Tag written successfully");
}
```

### `rfid.save(filename)`

Saves current tag data to a file.

**Parameters:**

| Parameter  | Type     | Description                |
| ---------- | -------- | -------------------------- |
| `filename` | `string` | File path to save tag data |

**Returns:** `boolean` - Success status

**Example:**

```javascript
var rfid = require("rfid");

// Read tag first
const tag = rfid.read(10);
if (tag) {
    rfid.save("/rfid/backup.rfid");
}
```

### `rfid.load(filename)`

Loads tag data from a file.

**Parameters:**

| Parameter  | Type     | Description                       |
| ---------- | -------- | --------------------------------- |
| `filename` | `string` | File path to load tag data from   |

**Returns:** `boolean` - Success status

**Example:**

```javascript
var rfid = require("rfid");

if (rfid.load("/rfid/card.rfid")) {
    console.log("Tag data loaded successfully");
}
```

### `rfid.clear()`

Clears the currently loaded tag data.

**Parameters:** None

**Returns:** `undefined`

**Example:**

```javascript
var rfid = require("rfid");

rfid.clear();
console.log("Tag data cleared");
```

### `rfid.clone()`

Clones the currently loaded tag to a new tag.

**Parameters:** None

**Returns:** `boolean` - Success status

**Example:**

```javascript
var rfid = require("rfid");

// Load source tag data
rfid.load("/rfid/source.rfid");

// Clone to new tag
if (rfid.clone()) {
    console.log("Tag cloned successfully");
}
```

## MIFARE Key Management

### `rfid.AddMifareKey(key)`

Adds a MIFARE authentication key.

**Parameters:**

| Parameter | Type     | Description                        |
| --------- | -------- | ---------------------------------- |
| `key`     | `string` | 6-byte key in hexadecimal format   |

**Returns:** `boolean` - Success status

**Example:**

```javascript
// Add default MIFARE key
rfid.AddMifareKey("FFFFFFFFFFFF");

// Add custom key
rfid.AddMifareKey("A1B2C3D4E5F6");
```

## SRIX Functions

SRIX (ST SRI512/SRIX4K) specific functions for special tag types.

### `srix.read(timeout)`

Reads SRIX tag data.

**Parameters:**

| Parameter | Type                  | Description             |
| --------- | --------------------- | ----------------------- |
| `timeout` | `number, default: 10` | Read timeout in seconds |

**Returns:** `object` - SRIX tag data

### `srix.write(data, timeout)`

Writes data to SRIX tag.

**Parameters:**

| Parameter | Type                  | Description              |
| --------- | --------------------- | ------------------------ |
| `data`    | `object`              | Data to write            |
| `timeout` | `number, default: 10` | Write timeout in seconds |

**Returns:** `boolean` - Success status

### `srix.save(filename)`

Saves SRIX tag data to file.

**Parameters:**

| Parameter  | Type     | Description |
| ---------- | -------- | ----------- |
| `filename` | `string` | File path   |

**Returns:** `boolean` - Success status

### `srix.load(filename)`

Loads SRIX tag data from file.

**Parameters:**

| Parameter  | Type     | Description |
| ---------- | -------- | ----------- |
| `filename` | `string` | File path   |

**Returns:** `boolean` - Success status

### `srix.clear()`

Clears SRIX tag data.

**Parameters:** None

**Returns:** `undefined`

### `srix.writeBlock(block, data)`

Writes data to a specific SRIX block.

**Parameters:**

| Parameter | Type     | Description                   |
| --------- | -------- | ----------------------------- |
| `block`   | `number` | Block number                  |
| `data`    | `string` | Hexadecimal data to write     |

**Returns:** `boolean` - Success status

## Usage Examples

```javascript
// RFID Tag Reader
function readAndDisplayTag() {
    display.fill(display.color(0, 0, 0));
    display.setTextColor(display.color(255, 255, 255));
    display.setTextSize(1);
    display.drawString("Place tag on reader...", 10, 10);

    const tag = rfid.read(15);

    if (tag) {
        display.fill(display.color(0, 0, 0));
        display.drawString("Tag Found!", 10, 10);
        display.drawString(`UID: ${tag.uid}`, 10, 30);
        display.drawString(`Type: ${tag.type}`, 10, 50);

        if (tag.data) {
            display.drawString("Data:", 10, 70);
            display.drawString(tag.data.substring(0, 20), 10, 90);
        }

        delay(3000);
        return tag;
    } else {
        display.drawString("No tag detected", 10, 30);
        delay(2000);
        return null;
    }
}

// RFID Badge System
class BadgeSystem {
    constructor() {
        this.authorizedTags = new Set();
        this.loadAuthorizedTags();
    }

    loadAuthorizedTags() {
        try {
            const data = storage.read("/rfid/authorized.json");
            const tags = JSON.parse(data);
            this.authorizedTags = new Set(tags);
        } catch (error) {
            console.log("No authorized tags file found");
        }
    }

    saveAuthorizedTags() {
        const tags = Array.from(this.authorizedTags);
        storage.write("/rfid/authorized.json", JSON.stringify(tags, null, 2));
    }

    addAuthorizedTag() {
        display.fill(display.color(0, 0, 100));
        display.setTextColor(display.color(255, 255, 255));
        display.drawString("Present tag to authorize", 10, 10);

        const uid = rfid.readUID(10);
        if (uid) {
            this.authorizedTags.add(uid);
            this.saveAuthorizedTags();
            dialog.success(`Tag ${uid} authorized`);
            return uid;
        } else {
            dialog.error("No tag detected");
            return null;
        }
    }

    checkAccess() {
        display.fill(display.color(0, 0, 0));
        display.setTextColor(display.color(255, 255, 255));
        display.drawString("Present badge...", 10, 10);

        const uid = rfid.readUID(5);

        if (uid) {
            if (this.authorizedTags.has(uid)) {
                display.fill(display.color(0, 255, 0));
                display.setTextColor(display.color(0, 0, 0));
                display.drawString("ACCESS GRANTED", 10, 30);
                audio.tone(1000, 200);
                return true;
            } else {
                display.fill(display.color(255, 0, 0));
                display.setTextColor(display.color(255, 255, 255));
                display.drawString("ACCESS DENIED", 10, 30);
                audio.tone(300, 500);
                return false;
            }
        }

        return false;
    }

    removeTag(uid) {
        if (this.authorizedTags.delete(uid)) {
            this.saveAuthorizedTags();
            return true;
        }
        return false;
    }

    listAuthorizedTags() {
        return Array.from(this.authorizedTags);
    }
}

// RFID Backup System
function backupAllTags() {
    const tagCount = 0;
    let successful = 0;

    display.fill(display.color(0, 0, 0));
    display.setTextColor(display.color(255, 255, 255));
    display.drawString("RFID Backup System", 10, 10);

    while (true) {
        display.fill(display.color(0, 0, 0));
        display.drawString(`Backup ${successful} tags`, 10, 10);
        display.drawString("Present next tag or ESC", 10, 30);

        // Wait for tag or escape
        let tag = null;
        const startTime = now();

        while (!tag && (now() - startTime) < 5000) {
            tag = rfid.read(1);
            if (keyboard.getEscPress(50)) {
                break;
            }
        }

        if (keyboard.getEscPress(50)) {
            break;
        }

        if (tag) {
            const filename = `/rfid/backup_${tag.uid}_${now()}.rfid`;
            if (rfid.save(filename)) {
                successful++;
                display.fill(display.color(0, 255, 0));
                display.drawString(`Saved: ${filename}`, 10, 30);
                delay(1000);
            } else {
                display.fill(display.color(255, 0, 0));
                display.drawString("Backup failed!", 10, 30);
                delay(1000);
            }
        }
    }

    dialog.info(`Backup complete: ${successful} tags saved`);
}

// RFID Database Manager
class RFIDDatabase {
    constructor() {
        this.database = {};
        this.load();
    }

    load() {
        try {
            const data = storage.read("/rfid/database.json");
            this.database = JSON.parse(data);
        } catch (error) {
            this.database = {};
        }
    }

    save() {
        storage.write("/rfid/database.json", JSON.stringify(this.database, null, 2));
    }

    addTag(tag, metadata = {}) {
        this.database[tag.uid] = {
            uid: tag.uid,
            type: tag.type,
            data: tag.data,
            firstSeen: now(),
            lastSeen: now(),
            readCount: 1,
            metadata: metadata
        };
        this.save();
    }

    updateTag(tag) {
        if (this.database[tag.uid]) {
            this.database[tag.uid].lastSeen = now();
            this.database[tag.uid].readCount++;
            this.database[tag.uid].data = tag.data; // Update data if changed
        } else {
            this.addTag(tag);
        }
        this.save();
    }

    getTag(uid) {
        return this.database[uid] || null;
    }

    getAllTags() {
        return Object.values(this.database);
    }

    removeTag(uid) {
        if (this.database[uid]) {
            delete this.database[uid];
            this.save();
            return true;
        }
        return false;
    }

    search(query) {
        const results = [];
        const lowerQuery = query.toLowerCase();

        Object.values(this.database).forEach(tag => {
            if (tag.uid.toLowerCase().includes(lowerQuery) ||
                tag.type.toLowerCase().includes(lowerQuery) ||
                (tag.data && tag.data.toLowerCase().includes(lowerQuery))) {
                results.push(tag);
            }
        });

        return results;
    }

    getStatistics() {
        const tags = Object.values(this.database);
        const types = {};
        let totalReads = 0;

        tags.forEach(tag => {
            types[tag.type] = (types[tag.type] || 0) + 1;
            totalReads += tag.readCount;
        });

        return {
            totalTags: tags.length,
            totalReads: totalReads,
            typeDistribution: types,
            mostReadTag: tags.reduce((max, tag) =>
                tag.readCount > (max?.readCount || 0) ? tag : max, null)
        };
    }
}

// MIFARE Card Manager
function setupMifareKeys() {
    const commonKeys = [
        "FFFFFFFFFFFF", // Default key
        "000000000000", // Blank key
        "A1B2C3D4E5F6", // Common key 1
        "B1B2C3D4E5F6", // Common key 2
        "123456789ABC", // Common key 3
    ];

    console.log("Adding MIFARE keys...");
    commonKeys.forEach((key, index) => {
        if (rfid.AddMifareKey(key)) {
            console.log(`Key ${index + 1} added: ${key}`);
        } else {
            console.log(`Failed to add key: ${key}`);
        }
    });
}

// Quick tag operations
function quickTagOperations() {
    while (true) {
        const choice = dialog.choice([
            "Read Tag",
            "Write Tag",
            "Clone Tag",
            "Backup Tag",
            "Load Tag",
            "Exit"
        ]);

        switch (choice) {
            case 0: // Read
                readAndDisplayTag();
                break;

            case 1: // Write
                const text = keyboard.keyboard("Text to write:", "", 0, 100);
                if (text) {
                    if (rfid.write({type: "text", content: text}, 15)) {
                        dialog.success("Tag written successfully");
                    } else {
                        dialog.error("Failed to write tag");
                    }
                }
                break;

            case 2: // Clone
                dialog.info("Present source tag...");
                const sourceTag = rfid.read(15);
                if (sourceTag) {
                    dialog.info("Present destination tag...");
                    if (rfid.clone()) {
                        dialog.success("Tag cloned successfully");
                    } else {
                        dialog.error("Clone failed");
                    }
                }
                break;

            case 3: // Backup
                const tag = rfid.read(15);
                if (tag) {
                    const filename = `/rfid/backup_${now()}.rfid`;
                    if (rfid.save(filename)) {
                        dialog.success(`Saved to ${filename}`);
                    }
                }
                break;

            case 4: // Load
                const file = dialog.pickFile("Select RFID file", ".rfid");
                if (file && rfid.load(file)) {
                    dialog.success("Tag data loaded");
                }
                break;

            case 5: // Exit
            default:
                return;
        }
    }
}
```
