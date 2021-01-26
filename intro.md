IS-06 is an AMWA NMOS Specification for API control of a managed network.

### What does it do?

- Lets broadcast control applications manage what happens on the network itself

### Why does it matter?

- Ethernet switch output ports might only support a limited number of media flows before they start dropping packets
  - This is different to what happens in a typical SDI router
  - And it means corrupted video and audio

### How does it work?

- Provides a “Northbound” API from network fabric’s controller
- Includes topology discovery, flow authorization and assurances of flow bandwidth

