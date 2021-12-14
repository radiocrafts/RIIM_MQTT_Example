Acronyms
========

=========== ===========================================
UART        Universal Asynchronous Receiver Transmitter
SLIP        Serial Line IP
MQTT        Message Queuing Telemetry Transport(historical name)
=========== ===========================================

Description
===========
MQTT is a popular and lightweight protocol for sending messages between devices. It runs over TCP/IP and is used a lot in automation use cases.

   This example shows how easy it is to setup an MQTT broker on an edge computer(tested on Raspberry PI 3) with node RED and connect a RIIM network to this. The RIIM network uses a different set of protocols with CoAP over UPD/IP and therefore a protocol translation is needed on the edge computer. This protocol translation is very easy and the example show how this is done with Node RED.

   The main challenges and tasks are therefore these:

   - Connect CoAP handler for receiving and sending CoAP messages to/from the RIIM nodes
   - Translate incoming CoAP PUT requests to the MQTT publish
   - Setup of MQTT broker
   - SLIP (This example is built on top of the SLIP example found here: https://radiocrafts.github.io/RIIM_SLIP_Example)

This example provides a possible solution to these challenges and tasks


System Overview
---------------
This image shows the system. The dashed grey parts are optional, and not part of this example. They probably would, however, be in a real deployment.

.. mermaid::

   graph LR
      idInt(Internet) --- idRPI(edge computer)
      idRPI(edge computer <br/> MQTT server) -- SLIP --- idBR(BorderRouter)
      idBR(BorderRouter) -- RF --- idMR1(MeshRouter)
      idBR(BorderRouter) -- RF --- idMR2(MeshRouter)
      idMR1(MeshRouter) -- RF --- idMR3(MeshRouter)
      idMR1(MeshRouter) -- RF --- idMR4(MeshRouter)
      
      style idInt fill:#aaa,stroke:#888,stroke-width:2px,color:#fff,stroke-dasharray: 3 3
      style idMR2 fill:#aaa,stroke:#888,stroke-width:2px,color:#fff,stroke-dasharray: 3 3
      style idMR3 fill:#aaa,stroke:#888,stroke-width:2px,color:#fff,stroke-dasharray: 3 3
      style idMR4 fill:#aaa,stroke:#888,stroke-width:2px,color:#fff,stroke-dasharray: 3 3

The edge computer runs both a MQTT broker and a CoAP server/client. Both these are implemented in the Node RED flow.
The edge computer communicates with a RIIM Border Router using SLIP over UART, and is able to talk to all RIIM nodes using IPv6 natively. All RIIM nodes also run CoAP servers/clients.


Topics not covered in this example
----------------------------------
These topics are not covered in this example, but could be relevant in a real world deployment

- Installation of operating system (See `<https://www.raspberrypi.org/software/operating-systems/>`_
- How to customize (low level) Linux network setup such as routing, proxying, firewalls etc.
- Migration to other operating systems, hardware or Linux distributions
- Security policies, such as parts running with administrator privilegies, Node Red keys, DTLS etc.
- Containerizations, virtualizations, Python environments
- Edge computer application auto startup on boot (See `<https://nodered.org/docs/getting-started/raspberrypi>`_


Edge Computer
-------------

This example uses a Raspberry Pi 3 running Raspberry Pi OS. Migration to other single board computers, other Linux distributions or PCs are outside the scope of this example. However, if keeping to mainstream hardware and Linux-based operating systems, migration should be fairly simple.
The tunslip must be compiled for the specific processor in each use case.


RIIM network nodes
------------------

There are two ICI applications provided:

- Border Router application
   - Connects via SLIP using UART
   - Sets up a CoAP resource called **"BRData"** that is accessible from all nodes and SLIP
   - BRData can respond to CoAP GET and CoAP PUT, to read/write an IPv6 address
   - This address is used to send periodic CoAP messages from the BR to tempBR resource
   - If no IPv6 address is set in **BRData** , the node periodically send its IPv6 address to coap://[fd00::1:1234]/CommData
   - This is used for commisioning, as the server then gets to know the address of the node

- Mesh Router application
   - For simplicity, the MR does not implement a CoAP resource, but sends it's temperature to a static address instead
   - The application sends the module's temperature periodically to tempMR resource
   - The application also prints out status on UART


Node Red Server
---------------

In this example, the server runs Node-RED, a popular programming tool for connecting hardware, services and APIs. The Node-RED servers hosts the MQTT broker, receives the CoAP message and publishes the content to the MQTT client.

