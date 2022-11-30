[Back](/Readme.md)

# ADR 7: Device triggered notications with geofencing 
Describe here the forces that influence the design decision, including technological, cost-related, and project local. 

## Status
Proposed 

## Rationale 
The Hey Blue! app needs to notify it's users whenver a possible connection is nearby, or when a connection is requested by another user. For server pushed notifications we need to detect proximity on the server side, or update the server when connecteable users are detected by the app. The server then calls a notification service with the relevant device id and notification payload to send a targeted notification.
We propose using device triggered notifications along with server pushed notifications, managed by a  background component that runs even when the app itself is closed. APIs for background services exist for Android and iOS that require the user to grant specific permissions besides the use of location services and bluetooth. 
We will also need to implement a "geofencing" component to the background service to alert the citizen when they are close to an Officer accepting connections.   
There need to be two sets of backgroud services implementing the geofencing workflow. A beacon service running on devices that are used by officers, and a listening service
Geofencing APIs are also available on both Android and iOS and require the use of location services.

## Decision 
We will use device triggered notifications and geofencing APIs on background services installed with the Hey Blue! app

## Consequences
Postive:
+ Allows triggering proximity notifications without server intervention 
+ Lowers processing overhead on the server

Negative:  
+ Background services on mobile devices may affect performance, battery life
+ Will not be able to show nearby users on map UI, lowers usability. Server based proximity detection will still need to be used as a fallback