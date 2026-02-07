---
tags:
  - _TODO
todo: Need to expand on the features available.
---
# Ethernet

![Ethernet Main Menu](ethernet-main-menu.png)

Bruce supports W5500 Ethernet SPI Card to perform some Layer 2 and Layer 3 attacks

![Ethernet](ethernet.png)


## Module

You will need a [W5500](../external-modules/w5500-ethernet-module.md) module to use these features.


## Scan Hosts

The scan hosts feature perform a ARP scanning of the network, furthermore it can scan all ports of a single host using the Host Info feature


## SSH Connect

Tries to connect into the Host using SSH.


## ARP Spoofing

Sends fake ARP Resonses to the host and to the Gateway, provoking communication interruption. this is the fist step of a Man-In-The-Middle attack using the 2nd OSI layer vulnerability.


## ARP Poisoning

Sends fale ARP responses to all hosts and to the gateway with random MAC addresses. It can possibly cause CAOS in the network, as all devices won't find the gateway to communicate.
