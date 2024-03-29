# Data Model: Endpoint

_(c) AMWA 2017, CC Attribution-NoDerivatives 4.0 International (CC BY-ND 4.0)_

The Endpoint, in this context, equals a single connection of a host device to a [Network Device](Data%20Model%20-%20Network%20Device.md). Therefore, if a host device has multiple interfaces through which the flows are sent or received, then each of them will be classified as an "endpoint" as per this specification. The broadcast controller can register as many "endpoints" as there are interfaces on the host devices that are connected to the network devices to send and receive flows.

![Class Diagram](images/IS-06-Endpoint.png)

The endpoint has to be registered with the Network Controller before it can send and/or receive any flows. The Broadcast Controller will provide a unique identifier in the PUT request to register the endpoint. The Broadcast Controller may create this identifier by itself or it may be generated elsewhere in the system. This identifier uniquely identifies the endpoint within the network and is used on all subsequent endpoint-related operations.

The required parameters for registering the endpoint are:
* `id`: A unique identifier for this endpoint as explained above
* `chassis_id`: The endpoint should provide its MAC address or another identifier permitted by the IEEE Link Layer Discovery Protocol (LLDP) for the Chassis ID parameter
* `port_id`: The endpoint shall provide its MAC address as permitted by LLDP for the Port ID parameter
* `ip_address`: The endpoint's IP address that will be used to send and/or receive the flows

The `attached_network_device`, including the switch's Chassis ID and Port ID, should be included in an endpoint registration request, so that a network controller can verify these details. These values should be provided by the switch in the LLDP mandatory parameters so they can be fetched by the endpoint.
When these details are not included in the request, the network controller may attempt to determine them and include them in the registered Endpoint.
However, if the network controller cannot determine them, it may reject the the request.

![Sequence Diagram](images/Endpoint-information-flow.png)

The operations allowed on an Endpoint are GET, PUT, PATCH and DELETE. GET is to retrieve an endpoint using the unique identifier. PUT is to create or register a new endpoint. PATCH is for modification of an endpoint. Only certain fields can be modified as explained in the API schema.
