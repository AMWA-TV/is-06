# AMWA IS-06 NMOS Network Control Specification (Deprecated)

[![Lint Status](https://github.com/AMWA-TV/is-06/workflows/Lint/badge.svg)](https://github.com/AMWA-TV/is-06/actions?query=workflow%3ALint)
[![Render Status](https://github.com/AMWA-TV/is-06/workflows/Render/badge.svg)](https://github.com/AMWA-TV/is-06/actions?query=workflow%3ARender)

<!-- INTRO-START -->

> AMWA IS-06 IS DEPRECATED

### What does it do?

- IS-06 lets broadcast control applications manage what happens on the network itself

### What was the motivation?

- Ethernet switch output ports might only support a limited number of media flows before they start dropping packets
  - This is different to what happens in a typical SDI router
  - And it means corrupted video and audio

### How does it work?

- Provides a “Northbound” API from network fabric’s controller
- Includes topology discovery, flow authorization and assurances of flow bandwidth

<!-- INTRO-END -->

## Getting started

There is more information about the NMOS Specifications and their GitHub repos at <https://specs.amwa.tv/nmos>.
