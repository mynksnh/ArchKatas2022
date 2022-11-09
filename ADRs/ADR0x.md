# ADR 1: Use of an API layer as the externally accessible interface to the system
The proposed architecture for Hey Blue! consists of a set of cohesive services exposing interfaces to be consumed by client applications as well as other services that are part of the system. We define a consumer to be external if it is invoked from outside the boundaries of the network where the service is hosted (on premises LAN, VPN or the network of a single cloud services provider).

## Rationale 
The Hey Blue! system is meant to be accessed primarily via a mobile app by external users. The architecture consists of containerized services encapsulating multiple components , each with their own interface. Only a subset of these interfaces are meant to be consumed by external users. Instead of each service exposing it's functinality directly to eternal users, we need to make those relevant interfaces accessible only through the API layer.

## Status
[Proposed]

## Decision
We will use an API layer to expose the unified public facing interface of the Hey Blue! System.

## Consequences
Positive:
+ API layer can be used to cross cutting concerns like load balancing, logging and monitoring, authentication ([ADR2](/ADRs/ADR2.md))

Adverse:

+ Development overhead of creating and maintaining an additional component
+ Possible increased latency due to API layer having to route requests to target component