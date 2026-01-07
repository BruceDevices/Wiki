# Keyboard Shortcuts

!!! info

    Only available on Cardputer

## Usage

When you are on the main menu, you can use the following keyboard shortcuts:

* `i` : Navigate to IR codes.
* `r` or `s` : Navigate to RF codes.
* `b` : Navigate to BadUSB.
* `w` : Navigate to WebUI.
* `f` : Start the file manager on SD card (if present).
* `l` : Start the file manager on LittleFS.

> While not on the main menu these shortcuts are disabled.

In the file picker you can jump to files by pressing the first letter on the keyboard. You can also scroll whole pages with left and right.

## Customize Keys

You can customize these keys by using a `/shortcuts.json` file.

The action string on the right can be any supported [serial command](serial.md).

> Uppercase/lowercase letters are considered different and therefore can have different actions associated with them.
>
> Symbols are also supported.


### Example

```js
{
    "f": "loader open files",
    "i": "loader open ir",
    "r": "loader open rf",
    "b": "loader open badusb",
    "w": "loader open webui",
    "c": "loader open config",
    "1": "rf tx_from_file BruceRF/plug1_on.sub",
    "2": "rf tx_from_file BruceRF/plug2_on.sub",
    "t": "ir tx_from_file BruceIR/tv_telefunken_power.ir",
    "l": "ir tx_from_file BruceIR/desklamp_off.ir",
    "L": "ir tx_from_file BruceIR/desklamp_on.ir"
}
```
