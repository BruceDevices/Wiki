# Serial

Bruce accept command inputs via serial.

List of supported commands:

> ir, subghz, music_player, say, led, power, clock, screen, color, tone, gpio, i2c, storage, settings, factory_reset, badusb, js, crypto, uptime, date, free, info, webui, loader

Most of these commands are compatible with the [Flipper Zero CLI](https://docs.flipper.net/development/cli#0Z9fs).


## How to Send Commands

* Via Web Terminal
    * [Spacehunh Serial Terminal](https://terminal.spacehuhn.com/)
    * [ESPConnect](https://thelastoutpostworkshop.github.io/ESPConnect/)
* Via the [Arduino IDE Serial Monitor](https://docs.arduino.cc/software/ide-v2/tutorials/ide-v2-serial-monitor/), [Putty](https://pbxbook.com/voip/sputty.html), or any other serial terminal app
* Via the terminal/a bash script: `echo "say My name is Bruce" | busybox microcom -s 115200 /dev/ttyACM0  -t 1000`
* Via a [python script](https://github.com/wh00hw/pyFlipper)
* From a smartphone using [SerialManager](https://github.com/delletenebre/SerialManager2) or [Serial USB Terminal](https://play.google.com/store/apps/details?id=de.kai_morich.serial_usb_terminal)
* Via the [WebUI](webui.md#serial-commands)
    * By using the `Serial Cmd` button
    * Using curl or similar `curl -XPOST "http://bruce.local/cm" -d "cmnd=say My name is Bruce"`

Serial connection settings:

* Speed: 115200
* 8 data bits, 1 stop bit, no parity, no Flow Control


## Reference

TODO: incomplete

| Full command | Arguments | Description | Alias |
| ------------ | --------- | ----------- | ----------- |
| `ir rx` | `<timeout>` (in seconds, defaults to 10s if missing) | Read an IR signal and print the dump on serial. | |
| `ir rx raw` | `<timeout>` (in seconds, defaults to 10s if missing) | Read an IR signal in RAW mode and print the dump on serial. | |
| `ir tx` | `<protocol> <address> <decoded_value>` | Send a custom decoded IR signal. | |
| `ir tx_from_file` | `<ir file path>` | Send an IR signal saved in storage. | |
| `subghz rx` | `<timeout>` (in seconds, defaults to 10s if missing) | Read an RF signal and print the dump on serial. | `rf rx` |
| `subghz rx raw` | `<timeout>` (in seconds, defaults to 10s if missing) | Read an RF signal in RAW mode and print the dump on serial. | `rf rx raw` |
| `subghz tx` | `<decoded_value> <frequency> <te> <count>` | Send a custom decoded RF signal. | `rf tx` |
| `subghz tx_from_file` | `<sub file path>` | Send an RF signal saved in storage. | |
| `music_player` | `<audio file path>` | Play an audio file (speaker req.). | `play` |
| `tone` | `<frequency> <duration>` | Play a single squarewave audio tone  (speaker req.) | `beep` |
| `say` | `<text>` | Text-To-Speech (speaker req.) | |
| `led` | `<r/g/b> <0-255>` | Change UI main color | |
| `power` | `<off/reboot/sleep>` | General power management | |
| `clock` | | show the clock UI | |
| `gpio mode` | `<pin number> <0/1>` | set GPIO pins mode (0=input, 1=output). | |
| `gpio set` | `<pin number> <0/1>` | Direct GPIO pins control (0=off, 1=on). | |
| `i2c scan` | | scan for modules connected to the I2C bus. List the results on serial. | |
| `storage` | `<list/remove/mkdir/rename/read/write/copy/md5/crc32>` `<file path>` | Common file management commands. Path must always be relative to the root. Defaults to SD card if found. | `ls, dir, md, ren, cat, type, cp, md5, crc32` |
| `settings` | | view all the current settings | `set` |
| `settings` | `<name>` | view a single setting value | `set` |
| `settings` | `<name> <new value>` | alter a single setting value | `set` |
| `factory_reset` | | Reset bruce.conf to the defaults | |


## Examples

!!! warning

    Filenames are case-sensitive on LittleFS only and cannot contain spaces/escape chars.

### Text-to-Speech (requires a speaker)

```
say My name is Bruce
```

### Play Doom Music (requires a speaker)

```
music_player doom:d=4,o=5,b=112:16e4,16e4,16e,16e4,16e4,16d,16e4,16e4,16c,16e4,16e4,16a#4,16e4,16e4,16b4,16c,16e4,16e4,16e,16e4,16e4,16d,16e4,16e4,16c,16e4,16e4,a#4,16p,16e4,16e4,16e,16e4,16e4,16d,16e4,16e4,16c,16e4,16e4,16a#4,16e4,16e4,16b4,16c,16e4,16e4,16e,16e4,16e4,16d,16e4,16e4,16c,16e4,16e4,a#4,16p,16a4,16a4,16a,16a4,16a4,16g,16a4,16a4,16f,16a4,16a4,16d#,16a4,16a4,16e,16f,16a4,16a4,16a,16a4,16a4,16g,16a4,16a4,16f,16a4,16a4,d#
```

### Put Device into Sleep Mode

```
power sleep
```

### Turn Off LG TV via a Decoded NEC code

```
ir tx NEC 04000000 08000000
```

### Turn Off LG AC via a RAW IR file (already stored on the FS)

```
ir tx_from_file AC_LG_SX122CL_off.ir
```

### Turn on a Remote-controlled Power Socket from a Decoded Sub file (already stored on the FS)

```
subghz tx_from_file plug1_on.sub
```

### Send a Custom RF Command (format `{3-byte hex_key} {frequency} {te} {count}`)

```
subghz tx 445533 433920000 174 10
```

### Execute a BadUSB Script (already stored on the FS)

```
badusb run_from_file HelloWorld.txt
```

### Set a Pin in (digital) Output Mode (only on unused GPIO pins, using Arduino `pinMode()`)

```
gpio mode 2 0
```

### Sets the Pin's Value (only on unused GPIO pins, using Arduino `digitalWrite()`)

```
gpio set 2 0
```

### Read a Pin's Value (only on unused GPIO pins, using Arduino `digitalRead()`)

```
gpio read 2
```

### Read File Content and Output on the Console

```
storage read BruceRF/bruce_0.sub
```

### List Files on the Root (SD card if present, LittleFS otherwise)

```
storage list /
```

### Delete a File

```
storage remove BruceRF/bruce_0.sub
```

### Scan Connected I2C Devices

```
i2c
```

### View Settings

```
settings
```

### Change the Radio Module Setting

```
settings RfModule 1
```

### Reset Settings to the Default

```
factory_reset
```
