---
tags:
 -  _TODO
todo: Properly document each menu feature
---
# RFID

![RFID Main Menu](rfid-main-menu.png)

Use built in, externally connect RFID module, Chameleon Ultra or PN532 to read/write/copy/emulate.

![RFID](rfid.png)

## RFID and NFC Overview

**RFID (Radio Frequency Identification)** is a technology that utilizes electromagnetic fields to automatically identify and track tags attached to objects. These tags store electronically encoded information that can be read by an RFID reader. RFID is commonly employed in various applications, including inventory management, access control, and transportation systems.

**NFC (Near Field Communication)** is a subset of RFID technology that operates over shorter distances, typically within a few centimeters. NFC enables devices to communicate by bringing them close together, making it ideal for contactless payments, data sharing between devices, and smart access systems.


## Types of RFID Tags: Writable vs. Clonable

### Writable RFID Tags

Writable RFID tags allow users to modify the data stored on the tag, facilitating dynamic information updates. However, these tags typically do not permit writing to **Block 0**, which usually contains manufacturer data or unique identifiers.


### Clonable RFID Tags

Clonable RFID tags pose a security risk, as cloning involves creating a duplicate of the tag's information. This can lead to unauthorized access or fraudulent activities. Cloning often requires writing to **Block 0**, necessitating a special type of tag that supports this capability.


## Common Types of RFID and NFC Tags

* **MIFARE Classic 1k/4k** Operates at 13.56MHz and offers 1KB or 4KB of memory storage. Commonly used in access control systems, transportation, and loyalty programs. You can find clonable versions of these tags [here](https://pt.aliexpress.com/item/1005006787338686.html).
* **NTAG Series** Popular NFC tag types, such as NTAG213 and NTAG215, operate at 13.56MHz and are known for their compatibility with a wide range of NFC-enabled devices and applications. These tags support NDEF records that can be interpreted by smartphones.
* **EM4100 and T5557** RFID protocols operating at 125kHz, commonly used for low-frequency access control systems, identification badges, key fobs, asset tracking, and other proximity-based identification applications.

## Supported Modules

* **RFID 13.56MHz**
    * **M5Stack [RFID2](https://docs.M5Stack.com/en/unit/rfid2) module**
    * **MFRC-522** - via [I2C](https://github.com/BruceDevices/firmware/blob/main/media/connections/rc522_stick.jpg) (Select `M5 RFID2` in the config menu)
    * **PN532** - via [I2C](https://github.com/BruceDevices/firmware/blob/main/media/connections/pn532_i2c.jpg), [SPI](https://github.com/BruceDevices/firmware/blob/main/media/connections/pn532_spi.jpg), or [BLE](https://why.yuyeye.cc/post-images/1568433091524.jpg)

* **RFID 125kHz**
    * **RDM6300** ([Connection Schema](https://github.com/BruceDevices/firmware/pull/182#issuecomment-2287692412))

## Features

### RFID 13.56MHz

* :white_check_mark: Read
* :white_check_mark: Write
* :white_check_mark: Clone
* :white_check_mark: Write NDEF Records (NFC tags only)
* :white_check_mark: Erase
* :white_check_mark: Save file
* :white_check_mark: Load file

### RFID 125kHz

* :white_check_mark: Read
* :white_check_mark: Save file
