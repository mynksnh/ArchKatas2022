[Back](/Readme.md)

# ADR 6: Use in memory graph store for caching and looking up Officer geolocation

## Status
Proposed

## Rationale 
This ADR makes two separate assertions applicable to the Message Processing component of Connections manager service.
1) Using an in memory database
2) The in memory database needs to support graph data stuctures

We need to use an in memory db because a single message processor could have 1000s of active socket connections constantly sending and recieving location and related data. The message processor needs to look up the closest available officer periodically for every citizen that is also sending their location. Disk based I/O would be too slow for this to be implemented. Geolocation clustering by zip code within the db can be used to reduce the time of the lookup. Memcached, Redis are two popular in memory database that could be considered

In order to lookup the closest officer when provided a geolocation, we propose implementing a graph data structure. The primary reason for this is that the algorithmic cost of searching for the closest geolocation from a given point, given a set of officer geolocations in a tabular data structure would be linear O(1) in the best case. We can improve this to a logarithmic cost O(logn) by maintaining a graph of officer geolocations, where each node is connected to the other closest nodes.
The node attributes of the graph identify the geolocation and Id of the officer, whether two nodes are connected is determined by calculating the haversine distance (geographical distance between a pair of latitude and longitudes) between them. The search algorithm would need to find the node whose connected vertices have a larger haversine distance than the distance between the provided citizen location and the location of the node itself.

Out of the box solutions that fulfill both of the above requirements may exist. RedisGraph is a possible tool but it will need to be evaluated for speed against custom implementations.


## Decision 
Will we use an in memory cache and/or a graph data structure to find the closest available police officer for a citizen wanting to make a connection

## Consequences
Positive  
+ Reduce latency, faster lookups and proximity detection  
Negative   
+ Increased hardware/memory requirements
+ The graph based approach reduces the cost of lookup at the expense of higher algorithmic cost when adding  vertices compared to a tabular approach
