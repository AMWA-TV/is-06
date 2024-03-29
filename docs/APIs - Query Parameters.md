# APIs: Query Parameters

_(c) AMWA 2019, CC Attribution-NoDerivatives 4.0 International (CC BY-ND 4.0)_

The Network Control API supports a range of query string parameters which may be used as part of GET requests.

The following document describes the expected usage and behaviour of these query parameters alongside the RAML specification in order to aid implementers. A description of each individual query parameter is included within the RAML.

## Pagination

Network Control APIs SHOULD support pagination of their API resources where the 'paged' trait is specified in the RAML documentation. A 501 HTTP status code MUST be returned where pagination is attempted against a Network Control API which does not implement it.

Network Control API clients MUST detect whether pagination is being used by examining the HTTP response headers for 'X-Paging-Limit' which MUST be returned in all cases where pagination is in use.

The following implementation notes should be observed:

* The network controller which serves the Network Control API is expected to maintain a 'creation' and 'update' timestamp alongside each resource in the [Data Model](Data%20Model.md). These values should not be returned to API clients in the response body, but will be made available via headers and used as pagination cursors.

* In order to ensure that pagination does not result in resources being skipped, it is important that there are no duplicate creation or update timestamps stored against resources of the same type (Network Device, Endpoint, etc). It is suggested (although not mandated) that these timestamps are stored with nanosecond resolution using a TAI timebase, which will allow clients to navigate collections based on a common understanding of time.

* The choice to page based on 'creation' or 'update' timestamp depends on the client's intended use of the data. Paging based on 'creation' time from the start of a collection provides the best mechanism to identify all resources held by the network controller. Paging based on 'update' time from the end of a collection provides the best mechanism to watch for changing resources.

* Paging through a collection for a second time, using the same cursors as previously should never result in new data appearing in the response payloads when using 'creation' ordering, provided the requested paging times are less than or equal to the maximum paging time held by the network controller. However, note that as the information is dynamic and resources may be updated or deleted a given cursor may return fewer results than it did previously. When using 'update' ordering and a query filter, new data may appear when cursors are used for a second time.

* When query parameters which perform filtering are used at the same time as paging, the filters must be applied by the implementation before applying paging parameters to the resulting data set.

* A server may choose its own default value for paging limit (see Example 1).

* Where both 'since' and 'until' parameters are specified, the 'since' value takes precedence where a resulting data set is constrained by the server's value of 'limit' (see Example 5).

* Servers should include 'prev' and 'next' Link headers and may include 'first' and 'last' Link headers in all responses.

### Examples

The following examples aim to identify how pagination should behave in the presence of a set of resource data. In order to avoid displaying full resource representations, the only data listed here is the 'update' timestamp associated with each registered record. The same procedures can be applied where 'creation' timestamps and the '?paging.order=create' parameter are used instead.

Where a paging limit is not specified in a request the server's default is used.

**Sample Data: Network Flow Update Timestamps (Comma-Separated)**
```
[0:1, 0:2, 0:3, 0:4, 0:5, 0:6, 0:7, 0:8, 0:9, 0:10, 0:11, 0:12, 0:13, 0:14, 0:15, 0:16, 0:17, 0:18, 0:19, 0:20]
```

Each of the above corresponds to the update timestamp of a corresponding Network Flow, in the format &lt;seconds&gt;:&lt;nanoseconds&gt; and displayed in ascending order. These will be used throughout the following examples.

Response payloads in the examples will show these values, but in a real implementation should be replaced by the corresponding JSON objects for the Network Flow or other resources being queried.

**Example 1: Initial /network-flows Request**

In this example there are no query parameters used in the request, but as the Network Control API supports pagination it returns a subset of the results with headers identifying how to page further into the collection.

***Request***
```
GET /x-nmos/netctrl/v1.0
```

***Response***

Headers
```
Link: <http://example.api.com/x-nmos/netctrl/v1.0/network-flows/?paging.since=0:20&paging.limit=10>; rel="next", <http://example.api.com/x-nmos/netctrl/v1.0/network-flows/?paging.until=0:10&paging.limit=10>; rel="prev"
X-Paging-Limit: 10
X-Paging-Since: 0:10
X-Paging-Until: 0:20
```

Payload Resources
```
[
  0:20,
  0:19,
  0:18,
  0:17,
  0:16,
  0:15,
  0:14,
  0:13,
  0:12,
  0:11
]
```

***Notes***
* The data set returned when no query parameters are specified must be from the most recently updated (or created) resources in the collection, returned in descending order.
* The X-Paging headers identify properties of the collection of data returned in the response. These parameters may be used to construct a URL which would return the same set of bounded data on consecutive requests (for example /x-nmos/netctrl/v1.0/network-flows?paging.since=0:10&paging.until=0:20).
* The Link header identifies the 'next' and 'prev' cursors which an application may use to make its next requests. An implementation may also provide 'first' and 'last' cursors to identify the beginning and end of a collection if this can be addressed via a consistent URL.

**Example 2: Request With Custom Limit**

This request is similar to Example 1, but the client has chosen to use a custom page size limit.

***Request***
```
GET /x-nmos/netctrl/v1.0/network-flows?paging.limit=5
```

***Response***

Headers
```
Link: <http://example.api.com/x-nmos/netctrl/v1.0/network-flows/?paging.since=0:20&paging.limit=5>; rel="next", <http://example.api.com/x-nmos/netctrl/v1.0/network-flows/?paging.until=0:15&paging.limit=5>; rel="prev"
X-Paging-Limit: 5
X-Paging-Since: 0:15
X-Paging-Until: 0:20
```

Payload Resources
```
[
  0:20,
  0:19,
  0:18,
  0:17,
  0:16
]
```

***Notes***
* In this case the server has accepted the client's paging size limit request. If the client had requested a page size which the server was unable to honour, the actual page size used would be returned in X-Paging-Limit.

**Example 3: Request With Since Parameter**

***Request***
```
GET /x-nmos/netctrl/v1.0/network-flows?paging.since=0:4
```

***Response***

Headers
```
Link: <http://example.api.com/x-nmos/netctrl/v1.0/network-flows/?paging.since=0:14&paging.limit=10>; rel="next", <http://example.api.com/x-nmos/netctrl/v1.0/network-flows/?paging.until=0:4&paging.limit=10>; rel="prev"
X-Paging-Limit: 10
X-Paging-Since: 0:4
X-Paging-Until: 0:14
```

Payload Resources
```
[
  0:14,
  0:13,
  0:12,
  0:11,
  0:10,
  0:9,
  0:8,
  0:7,
  0:6,
  0:5
]
```

**Example 4: Request With Until Parameter**

***Request***
```
GET /x-nmos/netctrl/v1.0/network-flows?paging.until=0:16
```

***Response***

Headers
```
Link: <http://example.api.com/x-nmos/netctrl/v1.0/network-flows/?paging.since=0:16&paging.limit=10>; rel="next", <http://example.api.com/x-nmos/netctrl/v1.0/network-flows/?paging.until=0:6&paging.limit=10>; rel="prev"
X-Paging-Limit: 10
X-Paging-Since: 0:6
X-Paging-Until: 0:16
```

Payload Resources
```
[
  0:16,
  0:15,
  0:14,
  0:13,
  0:12,
  0:11,
  0:10,
  0:9,
  0:8,
  0:7
]
```

**Example 5: Request With Since & Until Parameters**

***Request***
```
GET /x-nmos/netctrl/v1.0/network-flows?paging.since=0:4&paging.until=0:16
```

***Response***

Headers
```
Link: <http://example.api.com/x-nmos/netctrl/v1.0/network-flows/?paging.since=0:14&paging.limit=10>; rel="next", <http://example.api.com/x-nmos/netctrl/v1.0/network-flows/?paging.until=0:4&paging.limit=10>; rel="prev"
X-Paging-Limit: 10
X-Paging-Since: 0:4
X-Paging-Until: 0:14
```

Payload Resources
```
[
  0:14,
  0:13,
  0:12,
  0:11,
  0:10,
  0:9,
  0:8,
  0:7,
  0:6,
  0:5
]
```

***Notes***
* Whilst both 'since' and 'until' are specified, as this server example has a default paging limit of '10', the 'since' parameter must take precedence. As a result of this the value of X-Paging-Until is lower than requested and a further request must be made to retrieve any remaining data.

### Edge Cases

When a client requests data which falls at the extreme ends of the stored data set it may be less clear what values should be returned in the 'X-Paging-Limit' and 'X-Paging-Since' headers. The following examples are intended to clarify the expectation in these cases.

Where a paging limit is not specified in a request the server's default is used.

**Example 1: Client request occurs at the beginning of the data set**

***Request***
```
GET /x-nmos/netctrl/v1.0/network-flows?paging.until=0:20
```

In this case, assume that there are stored records for '0:21' and '0:22' but no earlier.

***Response***

Headers
```
Link: <http://example.api.com/x-nmos/netctrl/v1.0/network-flows/?paging.since=0:20&paging.limit=10>; rel="next", <http://example.api.com/x-nmos/netctrl/v1.0/network-flows/?paging.until=0:0&paging.limit=10>; rel="prev"
X-Paging-Limit: 10
X-Paging-Since: 0:0
X-Paging-Until: 0:20
```

Payload Resources
```
[]
```


**Example 2: Client request occurs at the end of the data set**

***Request***
```
GET /x-nmos/netctrl/v1.0/network-flows?paging.since=0:20
```

In this case, assume that there are stored records for '0:19' and '0:20' but no later.

***Response***

Headers
```
Link: <http://example.api.com/x-nmos/netctrl/v1.0/network-flows/?paging.since=0:20&paging.limit=10>; rel="next", <http://example.api.com/x-nmos/netctrl/v1.0/network-flows/?paging.until=0:20&paging.limit=10>; rel="prev"
X-Paging-Limit: 10
X-Paging-Since: 0:20
X-Paging-Until: 0:20
```

Payload Resources
```
[]
```

***Notes:***
* In this situation the client is expected to re-perform the same request (as specified in the 'next' cursor) until data is returned. If the client were to increment the value of 'since' requested it would be in danger of moving ahead of the current time and missing records.


**Example 3: Client request includes a query parameter resulting in a single result, but no paging parameters**

***Request***
```
GET /x-nmos/netctrl/v1.0/network-flows?multicast_address=226.1.1.1
```

In this case, assume that the most recently created or updated Network Flow held in the registry has a paging value of '0:20' associated with it. Also assume that the Network Flow with multicast address '226.1.1.1' has a paging time of '0:15'.

***Response***

Headers
```
Link: <http://example.api.com/x-nmos/netctrl/v1.0/network-flows/?multicast_address=226.1.1.1&paging.since=0:20&paging.limit=10>; rel="next", <http://example.api.com/x-nmos/netctrl/v1.0/network-flows/?multicast_address=226.1.1.1&paging.until=0:0&paging.limit=10>; rel="prev"
X-Paging-Limit: 10
X-Paging-Since: 0:0
X-Paging-Until: 0:20
```

Payload Resources
```
[
  0:15
]
```

**Example 4: Client request includes a query parameter resulting in a no result, with no paging parameters**

***Request***
```
GET /x-nmos/netctrl/v1.0/network-flows?multicast_address=224.1.0.37
```

In this case, assume that the most recently created or updated Network Flow held in the registry has a paging value of '0:20' associated with it.

***Response***

Headers
```
Link: <http://example.api.com/x-nmos/netctrl/v1.0/network-flows/?multicast_address=224.1.0.37&paging.since=0:20&paging.limit=10>; rel="next", <http://example.api.com/x-nmos/netctrl/v1.0/network-flows/?multicast_address=224.1.0.37&paging.until=0:0&paging.limit=10>; rel="prev"
X-Paging-Limit: 10
X-Paging-Since: 0:0
X-Paging-Until: 0:20
```

Payload Resources
```
[]
```


## Basic Queries

Network Control APIs SHOULD support basic queries against their API resources. A 501 HTTP status code MUST be returned where a basic query is attempted against a Network Control API which does not implement it.

Basic queries make use of standard HTTP GET query parameters in the form '?key1=value1&key2=value2'. Keys may match any attribute which the API schemas indicate could be returned by a given resource.

Any attribute which may be returned by a particular API resource SHOULD be available to use as a query parameter, however the RAML documentation only explicitly identifies core attributes under 'queryParameters'.

If a query parameter is requested which does not match an attribute found in any resource, an empty result set must be returned.

* Querying Within Objects
  * Querying using attributes of objects is permitted via the use of a '.' separator. For example, the /endpoints resource may be queried with '?attached_network_device.chassis_id=7a-c9-d7-f0-e2-03'.

* Querying Within Arrays
  * Querying within arrays is permitted using a single value. For example, the /network-devices resource may be queried with '?receiver_endpoint_ids=231ea016-1553-4488-8c2b-26bee7b9a1aa'.
  * Querying using attributes of objects held within arrays is permitted via the use of a '.' separator. For example, the /network-devices resource may be queried with '?interfaces.admin_status=up'.

### Examples

**Example 1: Basic Query Using One Parameter**

***Request***
```
GET /x-nmos/netctrl/v1.0/endpoints?label=My%20Endpoint
```

***Response***
* Returns all Endpoint objects which have an attribute 'label' which exactly matches the string 'My Endpoint'.

**Example 2: Basic Query Using Two Parameters**

***Request***
```
GET /x-nmos/netctrl/v1.0/endpoints?role=sender&chassis_id=2d-af-a9-41-01-b4
```

***Response***
* Returns all Endpoint objects which have an attribute of 'role' which exactly matches 'sender' AND a 'chassis_id' attribute which exactly matches '2d-af-a9-41-01-b4'.

**Example 3: Querying Within Objects**

***Request***
```
GET /x-nmos/netctrl/v1.0/endpoints?attached_network_device.chassis_id=7a-c9-d7-f0-e2-03
```

***Response***
* Returns all Endpoints which have a 'attached_network_device' property that is an object with a 'chassis_id' having the value '7a-c9-d7-f0-e2-03'.

**Example 4: Querying Within Arrays**

***Request***
```
GET /x-nmos/netctrl/v1.0/network-devices?interfaces.admin_status=up
```

***Response***
* This query returns all Network Devices where one of the interface objects has an 'admin_status' of 'up'.

### Invalid Examples

**Invalid Example 1: Duplicate Query Parameters**

***Request***
```
GET /x-nmos/netctrl/v1.0/network-links?peers.device_id=f3841396-1cbf-4a8a-915c-dd1034121532&peers.device_id=2c516096-4fa6-4b93-8b84-248ad79dff3a
```

***Response***
* This query specifies two matching query parameters with different values. As different programming frameworks may not make both of these values available, this is an invalid query for which the response is not defined and may vary by implementation. This sort of query is not currently supported by the Network Control API.
