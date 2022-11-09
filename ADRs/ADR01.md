# ADR 1: Microservices Architecture

## Status
Proposed

## Rationale 
The principles of microservices are based on programming concepts and best practices from before distributed and service oriented architecutes were the norm. One of those principles is that the foundation of module design in software development is to keep coupling between modules low and cohesion high. This is in turn based on the more fundamental principles of programming known as separation of concerns, principle of least knowledge, and single responsibility principle.  
 Microservices incorporate the principles of Domain Driven Design which were also originally concieved for solving problems associated with technology partitioned layered monoliths
 The implication is that even if we decide to build the system as a modular monolith, we can still apply the proposed architecture when designing and constructing it's individual components. The modular structure of the system should at all times mirror the overall architecture proposed here, at each layer.

## Decision 
We will use the Microservices architecture to model the Hey Blue! system

## Consequences
Postitive:
+ Gives us a roadmap to steer the evolution of the system
+ Allows us to separate heavily used components into their own processes to allow them to scale
+ Clearly identifies the Domain model and partitioning of the system.
Negative: 
+ Data and code duplication
+ Difficult to execute quickly if we strictly adhere to the architecture and develop each microservice from scratch