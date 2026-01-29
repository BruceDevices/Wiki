---
title: SD Card and CC1101/NRF24
---
# M5StickC - SD Card and CC1101/NRF24

## Items Needed

* [CC1101](../../external-modules/sd-card-module.md#modules)
* BC547 Transistor - [Find on AliExpress](https://www.aliexpress.com/w/wholesale-BC547.html)
* Resistors [Find on AliExpress](https://www.aliexpress.com/w/wholesale-Resistors.html)
    * 1x 10k
    * 1x 5k
* [CC1101](../../external-modules/cc1101.md#modules)
* [NRF24](../../external-modules/nrf24.md#modules)


## Pins

!!! info
    Please check the wiring diagram below for full details of connections


| CC1101/NRF | M5StickC |
| -: | - |
| GND - 1 | GND |
| VCC - 2 | 3V3 |
| GDO0 - 3 | G32 |
| CSN - 4 | G33 + Connection to resistor -> transistor (base) |
| SCK - 5 | G0 |
| MOSI - 6 | G26 |
| MISO/GDO1 - 7 | G36/G25 |
| DGO2 - 8 | Not Connected |

| SD Card Module | M5StickC |
| -: | - |
| 3V3 - 1 | 3V3 |
| CS - 2 | Transistor (collector) |
| MOSI - 3 | G26 |
| CLKK - 4 | G0 |
| MISO - 5 | G36/G25 |
| GND - 6 | GND |


## Wiring Diagram

!!! info
    Please ensure you select Shared SPI

    **CC1101:** `RF -> Config -> RF Module -> CC1101 (Shared SPI)`

    **NRF24:** `NRF24 -> Config Pins -> NRF24 (Shared SPI)`

The diagram shows a CC1101, the wiring is the same for an NRF24.

[View on Cirkit Designer](https://app.cirkitdesigner.com/project/b5f4d910-647c-4dd4-b1b1-efeff06263fa)

![M5StickC - SD Card and CC1101](m5stickc-sd-card-and-cc1101.png){ width="400" }
