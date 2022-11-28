# ADR 6: Use in memory graph store for caching and looking up Officer geolocation

## Status
Proposed

## Rationale 
This ADR makes two separate assertions applicable to the Message Processing component of Connections manager service.
1) Using an in memory database
2) The in memory database needs to support graph data stuctures

We need to use an in memory db because a single message processor could have 1000s of active socket connections constantly sending and recieving location and related data. The message processor needs to look up the closest available officer periodically for every citizen that is also sending their location. Disk based I/O would be too slow for this to be implemented. Geolocation clustering by zip code within the db can be used to reduce the time of the lookup. Memcached, Redis are two popular in memory database that could be considered

In order to lookup the closest officer when provided a geolocation, we propose implementing a graph data structure. The node attributes of the graph identify the geeolocation and Id of the officer, while the edge attributes identify the distance between them. The search algorithm would need to find the node whose connected vertices have a larger haversine distance than the distance between the provided citizen location and the location of the node itself.

Out of the box solutions that fulfill both of the above requirements may exist. RedisGraph is a possible tool but it will need to be evaluated for speed against custom implementations.


## Decision 
Will we use an in memory cache and/or a graph data structure to find the closest available police officer for a citizen wanting to make a connectionx

## Consequences
Positive  
+ Reduce latency, faster lookups  
Negative   
+ Increased hardware/memory requirements
+ The graph based approach reduces the cost of lookup at the expense of higher algorithmic complexity when adding or removing vertices
