##### Register Host (Phase 1): define a host to the Network Controller
##### POST (Description, Chassis Id, Port ID)
Returns Host ID

Notes:  We have required the host to support ARP or LLDP prior to sending the NetworkFlow or performing any IGMP requests. This applies to receiver-only endpoints as well. 

##### Register Sender or Receiver Application 
##### POST …/Application (Host ID, IP address, Switch ID/Interface (optional), Sender/Receiver/Both) 

Notes:  
1.	SwitchID/Interface is optional.  If not provided, it is assumed that the Network Controller knows the Switch ID/Interface as it has already discovered the host using the MAC address from the register Host call
2.	At the end, the Network Controller has to know the following for any sender or receiver:
	* MAC Address   
	* Switch ID/Interface information
	* IP address 

The first two are learnt through a host doing ARP or LLDP and reconfirmed by the Broadcast Controller using the Register Host(). 
The IP address is learnt through the register application ()

##### Permit a Networkflow 
##### POST …/NetworkFlow (Source address, Source Port (UDP/TCP) (optional), Destination Address, Destination Port (UDP/TCP) (Optional), protocol ID, Bandwidth and DSCP (optional))
Returns NetworkFlow ID

##### Permit a Multicast Networkflow 
##### POST …/Multicast-NetworkFlow (Source address, Source Port (UDP/TCP) (optional), Multicast group Address, Multicast port, Receivers, Bandwidth and DSCP (optional))
Returns NetworkFlow ID

Notes:
* Receivers: JSON structure which will contain one or more receivers with optional UDP/TCP port
* Bandwidth: JSON structure which will contain average rate for now.  Used for admission control. Does not imply any per-flow handling in the network

##### Add a listener 
##### POST …/listener (Receiver Address, Receiver port (UDP) (optional), NetworkFlow ID)
