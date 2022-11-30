[Back](/Readme.md)

# ADR 5: CQRS and Event Sourcing for asynchronous workflows  

## Status
Proposed

## Rationale 
The User profile needs to be updated in two different ways. The user may themselves make changes to their own profile, these updates are simply handles with a CRUD component mapped to the profile database.
However there are updates that are triggered by other microservices whenever a profile related event happens within the system.  
Two such events are whenever the Connections Manager service logs a connection, and whenever an officer or citizen donates/redeem points. In both of these cases the relevant microservice puts an event in the queue or topic to be recieved by the Profile Manager service. The communication workflow between these services relies on eventual consistency rather than atomicity.
Asynchronous profile updates is an ideal use case for applying the CQRS pattern where we create another component with it's own update model to apply these event driven changes. The read model of the CRUD component may also subscribe to these events to keep the data it returns consistent when there are pending changes still in the queue or topic.
The Reporting and analytics microservice needs to obtain data from transactions that are handled by multiple other microservices. For instance, it needs data about established Officer-Civilian Connections and their associated profiles, the points that have been awarded/redemeed/donated over time and related storefront or municipal transactions. 
One possible option is for the Reporting and Analytics service to request this data synchronously from the profile microservice. The obvious drawback of this approach is the amount of data the services will have to return. Another drawback is that this adds additional coupling betwwen services, having them maintain additional endpoints for sending data for reporting.
Our proposed solution is the use of event sourcing for services that need representations of transactional data from one or more other microservices. The profile service sources events from Connections and Rewards services. The Reporting and Analytics service sources other representations of the same events from the profile service.
The profile service thus stores the events produced by Rewards and Connections services as immutable records linked to user profiles

## Decision 
We will use the CQRS pattern to apply message driven updates to User profiles when they establish a connection, or redeem/donate points
We will use event sourcing for transactional events that need to be consumed by multiple multiple microservices.

## Consequences
Positive:
+ Allows for automated asynchronous updates to User Profile and analytics data triggered within the system  
+ Reduces coupling between microservices
+ Additional services that may need the same data may subscribe to the relevant topics   
 
Negative:  
+ Giving up atomicity in workflows in favor of eventual consistency
+ Possible data and code duplication
+ Transactions could be missed due to service downtime
