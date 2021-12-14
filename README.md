<!-- pandoc -V geometry:margin=1cm -V fontsize=12pt -V fontfamily=utopia README.md -o README.pdf -->

# RIIM MQTT example 

## Description
This is an intermediate complex example reflecting a simple real world scenario. The scenario is as follows:

---

   You want to create a system consisting of a few nodes logging the temperature at different locations. You need to use MQTT to access the temperature data. The system uses CoAP to send and receive data to/from a border router connected to an edge computer. The edge computer is a Raspberry Pi 3 running the Node-RED application which connects the CoAP messages to an MQTT broker.

---

## Requirements
|                   |                   |
| ----------------- | ----------------- |
| Operating system  | Raspberry Pi OS: 5.10.17-v7+   |
| Computer/Board    | Raspberry Pi 3    |

## Downloading
Download the source, either by cloning the repository like this:

`
git clone https://github.com/radiocrafts/RIIM_MQTT_Example
`

## Installation

Enter the **riim_mqtt** folder and run the install script

`
./install.sh
`


## Full Documentation
Please see the documentation located here:
[https://radiocrafts.github.io/RIIM_MQTT_Example]

## Contact
Contact Radiocrafts AS at [https://radiocrafts.com/contact/]