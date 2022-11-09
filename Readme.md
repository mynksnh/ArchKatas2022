# Architecture Katas

This is a team submission for O'Reilly Architecture Katas fall 2022.

Team Members:  
Aryaveer Singh  
Manav Rathi  
Mayank Sinha  

## Contents
- [Introduction](#introduction)  
- [Business Case](#business-case)
    - [Requirements](#requirements)
    - [Technical Constraints](#technical-constraints)
    - [Business Constraints](#business-constraints)
    - [Assumptions](#assumptions)
- [Architecture Approach](#architecture-approach)
    - [Goals](#goals)
- [Architecture Characteristics](#architecture-characteristics) 
    - [Driving Characteristics](#driving-characteristics)
    - [Implicit Characteristics](#implicit-characteristics)
    - [Others Considered](#others-considered)
- [Context](#context)  
    - [Actors](#actors)
    - [Use Cases](#use-cases)
    - [Event Storming](#event-storming)
- [Containers](#containers)
    - [Modular Monolith](#modular-monolith)
    - [Service Containers](#service-containers)
    - [API Layer](#api-layer)
- [Components](#components)
    - [Identity and Access Manager](#identity-and-access-manager)
    - [Profile Manager](#profile-manager)
    - [Connections Manager](#connections-manager)
    - [Rewards Manager](#rewards-manager)
    - [ETL Manager](#etl-manager)
    - [Reporting and Analytics Manager](#reporting-and-analytics-manager)
    - [Social Media API Manager](#social-media-api-manager)
- [Code](#code)
- [Deployment](#deployment)
- [Cost Analysis](#cost-analysis)
- [ADRs](#adrs)
- [References](#references)


## Introduction

Hey Blue! is an initiative by retired police officer and co-founder of non-profit [EcoSchool](https://www.verdiecoschool.org) Mr. John Verdi.
The Hey Blue! inititiative aims to bring police officers and the communities they serve closer by allowing them to interact and connect when they are close, either virtually or in person using their mobile devices. The initiative aims to incentivize these connections by rewarding the participants with points with an associated monetary value in the form of discounts or donations to charity. The mobile app developed as part of the initiative needs to convey the postive social impact of connections visually with animations, awarding virtual trophies (gamification), sharing cool quotes about social connections and community development.
These positive interactions/connections are recorded and shared on social media to reach a wider audience and grow the initiative.

## Business Case
### Requirements
The provided requirements document is available [here](https://docs.google.com/document/d/10o-4eEzFo005pqDt_ORCztzaQCQ_9FNWYrxFasou3Eo/edit)
### Technical Constraints
+ This is a greenfield technical project, the only assumed constraint is lack of existing infrastructure
### Business Constraints
+ Non-profit organization needs to build and support the system with revenue generated from an affiliate marketing model
+ Initial funding for the project comes from grants and sponsorships
### Assumptions
+ The system does not support financial transactions. The citizens can browse point based discount offers provided by retailers on the app. However the actual purchase happens on the retailer's own system or in store. The app is only responsible for generating verifiable vouchers and coupons when citizens redeem points.
+ Charities are allowed to redeem the monetary value of points donated . Retailers must provide a way for them to do that off-system

## Architecture Approach
+ We will begin by identifying the significant architecture characteristics relevant to our proposed solution, these will be the driving factors that impact our design and architecure decisions
+ We will use the C4 model to describe our solution initially treating the system as black box while identifying external actors and use cases. At each subsequent level we will zoom into the black box to identify containers components, dependencies and communcation mechanisms
+ We use the event storming process to determine the bounded contexts and aggregates within the proposed system.
+ We will compose the architecture as a set of cohesive microservices. We will desribe these microservices in a tool and technology agnostic manner, see [ADR1](ADRs/ADR01.md).

### Goals
**"Microservices are not the goal, you don't win by having microservices"** - Sam Newman

+ The architecture we describe here is not meant to be prescriptive. The Hey Blue! system could even be developed and deployed as a modular monolith, combining all services to run on a single container, which could then be scaled by deploying multiple instances of it. This could indeed be the approach taken initially for rapidly prototyping a minimum viable product at a reduced overall cost of development.
+ We apply the priciple of Hexagonal Architecture to model individual microservices. The internal components or "ports" within a microservice expose one or more interfaces or "adapters" that serve the needs of downstream consumers. For example, some component within a microservice may expose it's functionality over a REST, SOAP or RPC based interface. We may maintain multiple adapters for a single port at the same time or swap out the port if the need arises without the adapter having to change. Interface definition is thus pushed out to be an extrinsic configuration concern. Also see [ADR02](ADRs/ADR02.md)
+ We will attempt to keep our architectural description tool and framework agnostic. As mentioned before we want to avoid early lock in with specfic tools. To that end, all intra, inter, and extra service communication APIs will be described in language agnostic terms - REST over Http for Request/Response based communication, Pub/Sub pattern over Websockets protocol for message and event driven or duplex communication over a single channel. See [ADR03](ADRs/ADR03.md)

## Architecture Characteristics
The following section highlights the salient architecure characteristics we consider crucial to a successful implementation of the system.

### Driving Characteristics

| Top 3 | Characteristics | Rationale |
| ----------- | ----------- | ----------- |
| - [x] | Scalability | At scale the the system or certain components of it will need to serve millions of geographically distributed users. Building those components to be able to scale horizontally is vital to success |
| - [] | Portability | We need to avoid vendor or technology lock-in as the system scales to serve a growing user base |
| - [] | Upgradeability | Closely related to portability, we need to be able to enhance the system from a minimum viable product. This may require incorporating additional tools and frameworks, changes to continuous delivery pipelines, deploying on newer platforms etc |
| - [x] | Extensibility | Once the system is being used by a critical mass of users, and the affiliate marketing business model is successful, it is very likely we will need to implement new functionality |
| - [x] | Configurability | Given the amount of traffic the system is meant to handle, it is important for heavily used components to be configurable for load balancing, modification of rules and constraints etc.  |
| - [] | Availability | The system needs to be availiable at all times |

### Implicit Characteristics

Security
Usability
Performance

TODO: Expand

### Others Considered

Reliability
Recoverability

TODO: Provide Reasons, Cost of downtime is low

## Context
We start modelling the architecture of the system by envisioning the entire system as a black box. The Context model identifies all external actors and their interactions with the system.

### Actors and Use Cases
TODO: Add black box diagram

### Event Storming
The next step is zooming into the black box. The prerequisite to our goal of modelling the system as a set of independent microservices is to start with domain partitioning. To accomplish this we used the event storming process.
Event-storming begins with initially identifying "Domain Events". A Domain event is something that happens within the system. As per the process, we identify as many domain events as we can and put each one on an orange sticky note on the whiteboard.

![Ad-Hoc Domain Events](/Diagrams/domainEvents.png)

Subsequently we identify the commands that trigger these domain events. While a domain event is something that happens within the system, the command is the action that triggers a series of domain events. Commands invoked by external actors are explicitly identified. Certain commands do not have an associated actor, which implies that it was invoked internally within the system.
![adding commands](/Diagrams/domainEventsAndCommands.png)

Next, we identify the automation policies that invoke commands that do not have an associated actor to complete the picture.
![bounded contexts](/Diagrams/BoundedContexts.png)

The above diagram gives us the boundaries of our bounded contexts and the event driven connections between them

## Containers
### Modular Monolith
The event storming process described in the previous section allowed us to identify the bounded contexts of our system and the aggregates (components) within them. We now map each bounded context to be a module of our overall system. The resulting modular monolith is depicted in the following diagram.

![Modular Monolith](/Diagrams/modmono.png)

### Service Containers
The previous section described a single container, comprising of multiple modules. It did not give us any information about how the modules are coupled. The next step therefore is to segregate the container along the boundaries of our bounded contexts. The following diagram illustrates the the interactions between the various microservices and the external actors.

![Service Containers](/Diagrams/containers.png)

### API Layer
Finally we add an API layer to extract the publically accessible interface of the system. Instead of external users directly connecting to the individual services via a GUI, all external requests will be routed through the API layer. Also see [ADR04](/ADRs/ADR04.md)

![API Layer](/Diagrams/api-layer.png)

## Components
The following section describes the internal components of each microservice identified the previous section 
### Identity and Access Manager
The IAM service encapsulates the components that handle identity provisioning, account creation and session management. The implementation of a custom ad-hoc password based registration and authentication component is optional and not recommended.

The recommended approach is to register the service with a cloud based OpenID Connect provider and adapt an OIDC client library to provision an identity and then request it once the user authenticates. 

Hey Blue! may later implement it's own OpenID Connect system to replace the cloud based provider if it becomes cost prohibitive


The user will also be allowed to sign in using their social media accounts, in which case the service will request identity attributes from the social media identity provider.

All federated identity providers must be able to provide a common set of identity attributes (minimally name and email id) for the user.

An additional factor may be added to verify the user's phone number with a one time password to complete user self registration.

The final step in account creation is registering the user's phone number with a one time password verification. Use of an external SMS provider is recommended. The phone number will be use to map the user's account to a single mobile device.

Once all of the above steps an account will be created and the user will be asked to authenticate. Post authentication, the account provisioning component will create a session and issue a session token to the user which will only expire if the user explicitly logs out. Session storage will also store a refresh token

A valid session token gives the user access to all interfaces exposed by the API layer and should be part of the URI for all post authentication requests. 

![IAM components](/Diagrams/IAM.png)

### Profile Manager
The profile manager is responsible for creating and maintaining the public profile for all users. Once an account is provisioned and the user logs in, a default profile automatically created and the user is directed to update it.
The service encapsulates a query based CRUD component to make the profile changes requested by the user.
Besides this, we also apply need to make updates to the profile that are triggered within the system. CQRS [ADR05](/ADRs/ADR05.md)

![Profile Manager Components](/Diagrams/profile.png)

### Connections Manager
The connection manager is the most intricate part of the system. The need for it to be horizantally scalable is crucial given the amount of real time connections and requests it will handle at scale from all over the United States.
In our proposed architecture, each instance of the Connections manager service will serve requests from multiple zip codes. Initially, when the system is rolled out for a particular region, a single instance of the service should suffice. We will need to run stress tests on system to come up more with a precise estimate.
The service itself comprises of an Orchestrator component and a message processor. The message processor is a perpetually running async task that maintains a websocket connection pool with each connection in the pool mapped to a distinct url. The Orchestrator keeps track of free and busy websocket connections by getting notifications from the Message Processor (Observer design pattern)
The following sections describes the overall workflow, the differences between how the server and app handle citizen and officer requests should be noted

**Location Tracking**  
+ Officer turns on location services and makes themselves available for connection within a 15 minute window via the client app
+ App makes a GET request recieved by the orchestrator component on behalf of the officer. The request must include officer's current geolocation (Lat,Long),zipcode and sufficent information to uniquely identify the account and profile (AccountID, ProfileID). If the zipcode is not included in the request, the server will be responsible for mapping the geolocation to a zip code
+ Orchestrator determines the request is coming from an officer (separate endpoints for officer and citizen is one way of achieving this)
+ Orchestrator responds with the url of a free connection websocket connection
+ Orchestrator creates a record of a busy connection in it's local database of a tuple comprising the websocket url and the Officer's profile ID
+ App on the Officer's device establishes a websocket connection to the url provided in the previous step.
+ App sends the officer's location over the websocket connection whenever it detects a location change from the location services
+ Message processor stores and updates the the location of the officer in a geolocation clustered, in-memory graph database [ADR06](/ADRs/ADR06.md)
+ App on citizen device makes a GET request recieved by the orchestrator component when it detects a location change. The request must include citizen's current geolocation (Lat,Long),zipcode and sufficent information to uniquely identify the account and profile (AccountID, ProfileID). If the zipcode is not included in the request, the server will be responsible for mapping the geolocation to a zip code
+ Orchestrator determines the request is coming from an citizen (separate endpoints for officer and citizen is one way of achieving this)
+ Orchestrator responds with the url of a free connection websocket connection
+ Orchestrator creates a record of a busy connection in it's local database of a tuple comprising the websocket url and the Citizen's profile ID
+ App on the Citizen's device establishes a websocket connection to the url provided in the previous step.
+ App sends location updates to the server over the websocket connection, message processor looks up the location of the closest police officer from the in-memory graph store and sends it over the same connection, along with other relevant profile data.
+ The message processor is responsible for removing the officer's location from the in-memory cache 15 minutes after it added it. Once the 15 minute window expires the websocket connection is closed and the orchestrator is notified. Orchestrator removes the tuple it added earlier from it's local db.
**Proximity Detection**  
Proximity detection can happen in of two ways
1.    Bluetooth based proximity detection, similar to how Covid Tracking apps work
2.    Calculating proximity in the app or on the server

Bluetooth based proximity detection does not require a constantly sending and recieving location updates from the web server. APIs for background services exist on both Android and iOS, that can run even when the app is closed and send notifications to the user (See the next section about handling notifications). Such services can be programmed to periodically check for nearby bluetooth devices and obtain their bluetooth UUID. This UUID can then be sent to the server to determine if the device has the app installed and request the profile of the associated user. This will also require the user profiles or accounts on the server to be mapped to the UUIDs, and keep them updated, since UUIDs can be randomly generated.

The other method is to calculate proximity and then notify the user when they are within the threshold.

**Handling Notifications**  
Our architecture recommends using device triggered notifications instead of server pushed notifications [ADR07](/ADRs/ADR07.md). This is a significant decision as it is one less component to implement to will need to depend on one or more third party services (Apple APNs or Firebase cloud messaging). The architecture is however permissive enough to eventually incorporate server pushed notifications with minor changes to Connections Manager Service.  
The relevant classes for background services are subclasses of UNNotificationTrigger (swift) on iOS and the Service (java) class on Android.

**Establishing Connection**  
Once a citizen is notified they are close to an officer accepting connections, they can request a connection via the App UI, which triggers another Http request to the orchestrator. The orchestrator looks up the message processor that is currently handling an open connection to the officer and calls it to send a message that will trigger an in app notification on the officer's device along with the details of the requesting citizen. If the officer accepts, the app requests the Orchestrator to register a successful connection and put an event on the "Established connections" topic.

![Connections Manager](/Diagrams/connections.png)

### Rewards Manager
The Rewards manager is responsible handling point redemptions and donations by Citizens and Officers respectively.
The service intially serves the data for the views where the users can make these transactions. The Storefront offers, municipal schemes and Charity views are made available to users on the app, the users can then choose how they may use the points they have accrued by making connections.
Whenever a points transaction is recorded two representations of the event are put on outbound message channels to be consumed by the ProfileManager and the Reporting and Analytics service.]

TODO: Complete section

![Connections Manager](/Diagrams/rewards.png)
### ETL Manager
TODO: Add text
![ETL Components](/Diagrams/etl.png)
### Reporting and Analytics Manager

[ADR08](/ADRs/ADR08.md)

TODO: Add text
![ETL Components](/Diagrams/reporting.png)
### Social Media API Manager
TODO: Add text
![ETL Components](/Diagrams/socialmedia.png)
## Code
TODO: Add a working example of the connection workflow using involving 3 mobile devices running a flutter app and node.js web server accepting http requests and websocket connections.

## Deployment
TODO: Model a sample deployment on GCP

## Cost Analysis
TODO: add an estimated cost anaysis for 1000 active citizens and 10 officers

## ADRs
[ADR1 Microservices Architecture](/ADRs/ADR01.md)  
[ADR2 Hexagonal Microservices](/ADRs/ADR02.md)  
[ADR3 REST API over Http and Pub/Sub pattern over Websocket protocol](/ADRs/ADR03.md)  
[ADR4 Use of an API layer as the externally accessible interface to the system](/ADRs/ADR04.md)  
[ADR5 CQRS for message based Profile updates  ](/ADRs/ADR05.md)  
[ADR6 Use in memory graph store for caching and looking up Officer geolocation](/ADRs/ADR06.md)  
[ADR7 Device triggered notications with geofencing instead of server push ](/ADRs/ADR07.md)  
[ADR8 Event Sourcing](/ADRs/ADR08.md)  

Additional pending tasks: Add workflow diagrams for each microservice


## References
TODO: Fix references  
https://learning.oreilly.com/videos/oreilly-software-architecture/9781492050728/9781492050728-video328572/

https://alistair.cockburn.us/hexagonal-architecture/

https://openid.net/developers/certified/





