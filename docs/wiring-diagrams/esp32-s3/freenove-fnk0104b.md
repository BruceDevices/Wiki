---
title: Freenove FNK0104B (FNK0104AB)
---
# Freenove FNK0104B (FNK0104AB)

2.8" IPS ESP32-S3 display module (ESP32-S3R8, 16 MB flash, 8 MB OPI PSRAM)
with capacitive touch, audio codec and SD card slot. The design is the same as
the **ES3C28P**; the Freenove variant differs in the RGB LED and battery ADC
pins.

Bruce environment: `Freenove-FNK0104AB`.

!!! note "Community support"
    The board fixes and prebuilt firmware are currently provided by the
    [bruce-freenove-fnk0104b](https://github.com/chinderdelis/bruce-freenove-fnk0104b)
    community repository until the upstream pull requests are merged.

## On-board peripherals

| Function | Pins |
| --- | --- |
| Display (ILI9341, SPI) | SCLK 12, MOSI 11, MISO 13, CS 10, DC 46, RST board-reset, BL 45 |
| Touch (FT6336, I2C @0x38) | SDA 16, SCL 15, INT 17, RST 18 |
| SD card (SDMMC 4-bit) | CLK 38, CMD 40, D0 39, D1 41, D2 48, D3 47 |
| Audio (I2S) | MCLK 4, BCLK 5, LRC 7, DOUT 8, DIN 6 |
| Audio amplifier enable | GPIO1 (active low) |
| RGB LED (WS2812) | 42 |
| Button (Select) | 0 (BOOT) |
| Battery ADC | 9 (x2 divider) |

## Free GPIOs (broken-out headers)

| Header | Free GPIOs | Notes |
| --- | --- | --- |
| Expanded IO | 2, 3, 14, 21 | + 3V3 + GND |
| UART | 43 (TX0), 44 (RX0) | free if the serial console is unused |
| IIC | 16 (SDA), 15 (SCL) | shared touch/audio bus — for I2C add-ons |

Power external modules from the header **3V3** pin. CC1101 and nRF24 are 3.3 V
devices, not 5 V-tolerant.

> Match wiring by **GPIO number to the header silkscreen** (IO2/IO3/IO14/IO21,
> TX/RX), not by connector position.

## External radios (SPI)

| Device | SCK | MOSI | MISO | CS | GDO0/CE |
| --- | :---: | :---: | :---: | :---: | :---: |
| CC1101 | 14 | 3 | 2 | 21 | 43 (GDO0) |
| nRF24 | 14 | 3 | 2 | 44 | 3V3 (CE) |

- CC1101 and nRF24 share SCK/MOSI/MISO; set the pins in Bruce → Settings →
  Hardware/Pins.
- **CC1101 GDO0** default collides with MISO (GPIO2) — use GPIO43.
- Both radios at once need 7 GPIOs but only 6 are free. Tie nRF24 **CE to 3V3**
  or use one radio at a time and swap CS.

## I2C add-ons

PN532 (NFC), DS3231 (RTC) and other I2C devices join the IIC header
(SDA 16 / SCL 15). The touch controller (0x38) and ES8311 codec (0x18) already
use this bus.

## Photos

<!-- TODO: add board photos (PCB front/back, headers). -->
Photos of the board, headers and wiring will be added here.
