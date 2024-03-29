# Data Model: Network Link

_(c) AMWA 2017, CC Attribution-NoDerivatives 4.0 International (CC BY-ND 4.0)_

A Network Link represents the bidirectional link between a [Network Device](Data%20Model%20-%20Network%20Device.md) and another Network Device.

The parameters of a Network Link are:

* `id`: uniquely identifies a network link
* `peers`: an array of exactly two objects identifying a network device interface (by its `device_id` and `port_id`) and the peer network device interface
* `speed`: the speed of the link

The only operation permitted for this resource is GET. When a broadcast controller performs a GET operation with a `device_id` query parameter, the network controller returns all the links that originate/terminate in the specified network device.
As these links are bi-directional, a link will be reported when a GET operation is performed for either peer network device.
