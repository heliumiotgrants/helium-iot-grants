---
version: "1.0"
last_updated: "March 2025"
---

# Introduction

**This documentation provides instructions for running the denylist/report-card classification data pipeline for the Helium IoT (Internet of Things) network.** The network consists of nodes (sensor devices) and hotspots, which are LoRaWAN gateways. Gateways are rewarded for providing coverage for sensors, which use HNT (Helium Network Tokens) to pay for the traffic they send. To demonstrate coverage, gateways periodically transmit packets (beacons), which are received by other gateways on the network (witnesses). The received metadata, such as signal strength, timestamps, and the identities of the witness gateways, is recorded and stored centrally on a public AWS S3 (Amazon Web Services) bucket.

Some gateway operators falsify witness data in order to receive rewards from the network unfairly. The purpose of the IoT classification pipeline is to automatically identify these gateways.

# Scope

This documentation provides the steps necessary to complete the following: 

1) Run the classification service.  
2) Interpret the classification output and determine actions that may be taken based on this information.  
3) Describe the design of the various software components to facilitate future maintenance and upgrades.  

The classification pipeline consists of several components. Not all of these components are open-source. Contact information for obtaining access to the proprietary components is included when needed. 

# Contributing

This documentation is hosted on the [Helium IoT Working Group GitHub](https://github.com/heliumiotgrants/helium-iot-grants) and is maintained by [Matthew Patrick](https://github.com/mrpatrick1991).

# Acknowledgment 

This project is supported by the [Helium Foundation](https://www.helium.foundation/) (Nova Labs IoT Classifiers and Knowledge Transfer Grant). We gratefully acknowledge engineering guidance and support from [Nova Labs](https://nova.xyz).  
