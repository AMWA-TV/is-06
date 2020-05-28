# APIs: Discovery

_(c) AMWA 2019, CC Attribution-NoDerivatives 4.0 International (CC BY-ND 4.0)_

NMOS Discovery makes use of the DNS Service Discovery protocol as described in [RFC 6763](https://tools.ietf.org/html/rfc6763). DNS-SD specifies a mechanism for the use of Unicast or Multicast DNS for the purpose of identifying one or more endpoints on a network associated with a named service.

This specification defines one DNS-SD service type:

* **_nmos-netctrl._tcp:** A logical host which advertises a Network Control API.

Advertisements of the above type are accompanied by the following DNS TXT records.

### DNS-SD TXT Records

**api\_proto**

The DNS-SD advertisement MUST be accompanied by a TXT record of name 'api\_proto' with a value of either 'http' or 'https' dependent on the protocol in use by the Network Control API server.

**api\_ver**

The DNS-SD advertisement MUST be accompanied by a TXT record of name 'api\_ver'. The value of this TXT record is a comma separated list of API versions supported by the server. For example: 'v1.0,v1.1,v2.0'. There should be no whitespace between commas, and versions should be listed in ascending order.

**api\_auth**

The DNS-SD advertisement MUST be accompanied by a TXT record of name 'api\_auth' with a value of either 'true' or 'false' dependent on whether authorization is required in order to interact with the Network Control API or not.

**pri**

The DNS-SD advertisement MUST include a TXT record with key 'pri' and an integer value. Servers MAY additionally represent a matching priority via the DNS-SD SRV record 'priority' and 'weight' as defined in RFC 2782. The TXT record should be used in favour to the SRV priority and weight where these values differ in order to overcome issues in the Bonjour and Avahi implementations.
Values 0 to 99 correspond to an active NMOS Network Control API (zero being the highest priority). Values 100+ are reserved for development work to avoid colliding with a live system.

## DNS-SD Advertisement

Dependent on the architecture of a network, it may make sense to use one or both of unicast or multicast DNS for discovery.

The recommended method of Network Control API advertisement is via unicast DNS-SD advertisement of the type \_nmos-netctrl.\_tcp. Network Control APIs SHOULD additionally be capable of producing an mDNS advertisement. This MAY be disabled via a user-configurable method.

## Client Interaction Procedure

Network Control clients may optionally be configured to support just one of unicast or multicast service discovery, however it is recommended that both are used by default in order to simplify initial configuration.

If Network Control clients are not otherwise configured with the location of the Network Control API, they should proceed as follows:

1. Identify whether the client has been configured with DNS server addresses and a default search domain either manually or automatically via DHCP.
2. If such configuration exists and the discovery mechanism is not explicitly set to mDNS, perform a unicast DNS browse for services of the required type via the search domain.
3. If no unicast responses are received and the discovery mechanism is not explicitly set to unicast DNS, perform a multicast DNS browse for services of the required type in the '.local' domain.
   * Note that if a unicast response is received, multicast browsing should not be performed even if the unicast-discovered API is unresponsive.
4. Where both unicast and multicast DNS are used, merge the results of the above operations into a single list.
5. Discard any advertisements which do not meet the client's requirements, and sort the filtered list of discovered services using the API version and priority values associated with each record.
   * Given multiple returned Network Control APIs of the same API version and priority, the client makes a random selection.
6. The above list should be maintained via the methods defined by the DNS-SD specification.
   * If the chosen Network Control API does not respond correctly at any time, another Network Control API should be selected from the discovered list. Should no further Network Control APIs be available or TTLs on advertised services expired, a re-query may be performed.

The IP address and port of the Network Control API SHOULD be identified via the DNS-SD advertisement, with the full HTTP path then being resolved via the standard NMOS API path documentation.

Multiple DNS-SD advertisements for the same API are permitted where the API is exposed via multiple ports and/or protocols.

## Implementation Notes

It is expected that timeouts specified by DNS-SD and DNS specifications will be abided by when maintaining a cache, however clients may wish to temporarily mark particular advertisements as invalid where they have timed out or produced HTTP 5xx errors during API interactions. In the case of multicast DNS the re-query mechanism should be used, and is particularly important for servers which cannot unregister their mDNS announcements before going offline.

Due to the caching mechanisms present within mDNS implementations, if a service disappears from the network without cleanly removing its mDNS announcement, it will still be present in the caches of browsing systems until the defined expiry time (120 seconds to 75 minutes dependent on record type). When mDNS advertised data is found to be invalid (for example by performing an HTTP request to an advertised service API and seeing a timeout), the procedure defined in [RFC 6762](https://tools.ietf.org/html/rfc6762) section 10.2 should be followed. Additionally, [RFC 6762](https://tools.ietf.org/html/rfc6762) section 10.5 may be used to pre-empt these failures.
