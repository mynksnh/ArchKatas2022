# ADR 8: Event sourcing

## Status
Proposed

## Rationale 
The Reporting and analytics microservice needs to obtain data from transactions that are handled by multiple other microservices. For instance, it needs data about established Officer-Civilian Connections and their associated profiles, the points that have been awarded/redemeed/donated over time and related storefront or municipal transactions. 
One possible option is for the Reporting and Analytics service to request this data from all other relevant services. The obvious drawback of this approach is the amount of data the services will have to return. Another drawback is that this adds additional coupling betwwen services, having them maintain additional endpoints for sending data for reporting.
Our proposed solution is the use of event sourcing for services that need transactional data from one or more other microservices

## Decision 
We will use event sourcing for transactional events that need to be consumed by multiple multiple microservices

## Consequences
Postive:
+ Reduces coupling between microservices
+ Additional services that may need the same data may subscribe to the relevant topics
Negative:
+ Data and code duplication
+ Transactions could be missed due to service downtime