# AMWA Network Control Specification

This repository contains details of this AMWA Specification for Network Control API.  The Network Control API is the interface between the NMOS Services and the Network Controller to reserve and secure Network Services.  The Network Controller is the management entity which represents the Network as a whole: see AMWA's INFO-001 for more details. Specifically it is the Northbound API from the Network Controller to the NMOS services. It is defined in the JTMN Architecture document in the Network Layer. 

The main areas of work are:
1. Discovery of Network Topology and Discovery of endpoint devices that are connected to the Network Switches
2. Create/Retrieve/Update/Delete Network Streams (Flow Management) 
3. Monitoring and Diagnostics

This github is covering 1. above. 

## Getting started

Readers are advised to be familiar with:
* The JT-NM Reference Architecture (http://jt-nm.org/)
* The [overview of Networked Media Open Specifications](https://github.com/AMWA-TV/nmos)
* Arch Sprint to update JT-NM Reference Architecture: http://www.amwa.tv/projects/INFO-001.shtml


## Contents

* README.md -- This file
* [LICENSE](LICENSE) -- Licenses for software and text documents
* [NOTICE](NOTICE) -- Disclaimer
* Use Cases: The list of use cases were used to create the required API
* API: The API specification with the necessary schemas
* Examples:  Using the API, there are some examples to guide the implementation.
