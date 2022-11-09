# ADR 2: Hexagonal Microservices 

## Status
Proposed

## Rationale 
Hexagonal architecture allows us implement our microservices as a set of interchangeable components, each of which may provide multiple interfaces tailored to the needs of the consumer. 
Use of multiple interfaces allows us to consumer to easily request different views of the same data. A specific example of this is when a citizen requests a connection with an officer, they only need to see a subset of the Officer's profile (Like their name, profile picture and number of connections they have made)
Hexagonal architecture aligns well with microservices, as each service is meant to own it's data. Thus it is also responsible for mocking out the data it owns without involving external interfaces, for testing purposes withou

## Decision 
We will use the Hexagonal architectural pattern for designing our microservices

## Consequences
Positive:
+ Hexagonal service design promotes independently testable components
+ Allows us to support multiple UIs for the same read model

Negative:
+ Increase development effort when trying to support multiple interfaces for the same functionality
