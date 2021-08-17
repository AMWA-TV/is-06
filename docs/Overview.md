# AMWA IS-06 NMOS Network Control API Specification: Overview

_(c) AMWA 2017, CC Attribution-NoDerivatives 4.0 International (CC BY-ND 4.0)_

## Documentation

The documents included in this directory provide details and recommendations for implementations of the defined APIs, or their consumers.

Familiarity with the [JT-NM reference architecture](https://jt-nm.org/reference-architecture/) is assumed.

## Introduction

The purpose of this document is to explain the AMWA IS-06 NMOS Network Control API. The API is between the broadcast controller and the network controller as shown in the figure below. The broadcast controller is the overall policy control point for all media endpoints and sessions. The network controller abstracts the details of the network from the broadcast controller and provides an API for all required network services.

![System Diagram](images/BC-NC.png)

The API is "Northbound" of the Network Controller. The purpose of this API is to:
* Discover network topology
* Create and modify media flows in the network from a sender to one or more receiver devices
* Control how flows move on the network
* Assure bandwidth for these media flows
* Ensure network security by only allowing authorized senders, receivers and flows.

Conventions used in the Network Control API that are common with the specification of AMWA IS-04 and IS-05 are covered in the next section, [APIs](APIs.md).

## Data Model

The resources that are managed by AMWA IS-06 NMOS Network Control API are specified in the [Data Model](Data%20Model.md).

## LLDP

The Link Layer Discovery Protocol (LLDP), defined by [IEEE 802.1AB](https://standards.ieee.org/standard/802_1AB-2016.html), is a vendor-neutral protocol for network devices (switches) and endpoints to advertise their identity and describe their capabilities.

In order that the broadcast controller and the network controller can communicate about the network topology reliably, this specification strongly recommends LLDP be configured on network devices (switches) and endpoints.

LLDP defines three mandatory parameters including Chassis ID and Port ID.

Where LLDP is implemented, the LLDP identifiers should correspond to the values included in the resources managed by the Network Control API. Otherwise, a broadcast controller may require a manual step to use this API.

## Relationship with other Specifications

This specification has no dependency on AMWA IS-04 or IS-05. However the overall system is more fully automated with IS-04 and IS-05.

Where LLDP is implemented, this specification recommends that IS-04 Node APIs transmit the Chassis ID and Port ID of each interface, matching the `chassis_id` and `port_id` properties of the `interfaces` in the 'self' resource.
This specification also recommends that IS-04 Node APIs indicate the received Chassis ID and Port ID of each interface's peer network device in the `chassis_id` and `port_id` properties of the `attached_network_device` for all `interfaces` in the 'self' resource.

![Architecture Diagram](images/CurrentArchitecture.png)
