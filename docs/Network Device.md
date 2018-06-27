# AMWA NMOS Network Control API Specification: Network Device

_(c) AMWA 2017, CC Attribution-ShareAlike 4.0 International (CC BY-SA 4.0)_

The Network Device API permits the broadcast controller to get the network device information which allows it to map the network topology into its GUI.  
The network device-Get API returns, at a minimum,  an identifier for further reference, its chassis ID (same as what was reported via LLDP), its mgmt IP, the global (switch wide) MTU,  and an array of interfaces.  The interfaces information includes port id (same as what is transmitted through LLDP), speed, admin and operation status. The MTU is optional and can be included if different from the global MTU. 
Only a GET operation is permitted on Network device(s). 
