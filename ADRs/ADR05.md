# ADR 5: CQRS for message based Profile updates  

## Status
Proposed

## Rationale 
The User profile needs to be updated in two different ways. The user may themselves make changes to their own profile, these updates are simply handles with a CRUD component mapped to the profile database.
However there are updates that need to be triggered by other microservices whenever a profile related event happens within the system.  
Two such events are whenever the Connections Manager service logs a connection, and whenever an officer or citizen donates/redeem points. In both of these latter cases the relevant microservice puts an event in the queue or topic to be recieved by the Profile Manager service.
This is an ideal use case for applying the CQRS pattern where we create another component with it's own update model to apply these event driven changes. The read model of the CRUD component may also subscribe to these events to keep the data it returns consistent when there are pending changes still in the queue or topic.

## Decision 
We will use the CQRS pattern to apply message driven updates to User profiles when they establish a connection, or redeem/donate points

## Consequences
Positive:
+ Allows for automated asynchronous updates to User Profiles triggered within the system
Negative:
+ Increase implementation complexity
