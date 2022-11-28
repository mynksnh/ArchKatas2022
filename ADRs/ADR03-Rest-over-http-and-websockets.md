# ADR 3: REST based API over Http for synchronous requests and websockets for messages over the web
This ADR describes the APIs and the communication protocols we will use for communication with client applications

## Status
Proposed

## Rationale 
Both HTTP and Websocket protocols are standards for transferring data over the web. Both are based on the same underlying trasport layer protocol which ensures guaranteed delivery and ordering of IP packets. Both protocols support the TLS extension for encrypting data in transit. Both protocols support access to resources over the web via URLs.

RESTful APIs are an established standard designed specifically for Http based communication over the web. Realistically there are no alternatives to a RESTful API. Often discussed alternatives to REST are SOAP, RPC and GraphQL, all of which have specfic data interchange formats and applicable to specific use cases. A RESTful API is the highest level abstraction that does not assume anything about the underlying system it provides access to.

For message based communication over the web, the alternatives to websockets are Server sent events and gRPC. 
Server sent events only allow an event stream in one direction, from server to client. Websocket client libraries for mobile, desktop and web based clients have wider availability in application frameworks than gRPC.

## Decision 
We will use REST over Http and websockets to implement our public APIs

## Consequences
Positive:

+ We use web standard protocols in public APIs
+ Allows us to identify and request server resources via urls
+ Every web server framework has implicit support for both underlying protocols
+ Free to use widest variety of serialization mechanisms for message payloads  

Negative:

+ Mobile client apps will not be able to establish background service based websocket connections. The users will need to be actively using the app for a websocket connection.
+ gRPC and protobuf is Http/2 based, faster than websockets in theory
