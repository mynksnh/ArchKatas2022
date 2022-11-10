# ADR 3: REST API over Http and Pub/Sub pattern over Websocket protocol
This ADR describes the API standards and the default underlying communication protocols we will use in our system

## Status
Proposed

## Rationale 
Both HTTP and Websocket protocols are standards for transferring data over the web. Both are based on the same underlying trasport layer protocol  which ensures guaranteed delivery and ordering of IP packets. Both protocols support the TLS extension for encrypting data in transit. Both protocols support access to resources over the web via URLs.

RESTful APIs are an established standard designed specifically for Http based communication over the web. Realistically there are no alternatives to a RESTful API. Often discussed alternatives to REST are SOAP, RPC and GraphQL, all of which have specfic data interchange formats and applicable to specific use cases. A RESTful API is the highest level abstraction that does not assume anything about the underlying system it provides access to.

For message and event driven architectures, the presence of a widely accepted API standard is contentious. We choose the Pub/Sub pattern as the basis for creating our message driven APIs because it is the greatest common denominator of proprietary APIs exposed by message broker tools. All prominent message broker tools such as ApacheKafka, RabbitMQ, ActiveMQ have API language specific implementations based on the Pub/Sub pattern. Same goes for cloud based implementations such as Google and Azure PubSub, Amazon SQS

## Decision 
We will use REST and Pub/Sub as the default standard for our service interfaces.
We will use HTTP and Websockets as the underlying communication protocols for our services.

## Consequences
Positive:  
+ Both REST and Pub/Sub are language agnostic patterns
+ Both REST and Pub/Sub allow identifying and accessing 
+ Every web server framework has implicit support for both underlying protocols
+ Both REST and Pub/Sub are abstractions that do not require specific serialization for their message payloads  
Negative:  
+ Higher development effort initially if we do not use out of the box messaging solutions
+ We will have rewrite the a lot of the underlying implementation when switching to a proprietary message broker tool
