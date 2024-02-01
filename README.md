# Grabcab.co

This project is an ongoing attempt to demonstrate the technology behind ride hailing apps in the likes of Uber and Bolt but with a twist.

1. Driver Signups (Documents, Licensing, PCO Authorizations, Country/Region/Town Restrictions)
2. Dispatch Integrations (Connect to Worldwide suppliers of Cab Operators)

This project aims to provide the best of both worlds, dispatch integrations, and self-employed drivers on the platform.

There are oppertunities to break the entire platform into a Microservices oriented infrastructure with Amazon AWS powering the server tech.

![image](https://github.com/rachow/grabcab/assets/12745192/b66885c8-26b0-4aab-af19-a923247845d8)
![image](https://github.com/rachow/grabcab/assets/12745192/f80023fd-308f-45b7-9115-e47fc229db17)


## Roadmap (TLD;R)
The following are areas to explore and take things to the next level. Fasten your seat belts!

- SPA frontends for the drivers web app -> `drivers.grabcab.co/signup` hooking to NodeJS API which authenticates using JWT/Refresh Tokens.
- Corporate app for businesses to signup -> `corporate.grabcab.co/signup` will allow companies to book ride and manage employee spendings.
   - Consider SPA in future and connect to NodeJS.
   - NodeJS will connect to MySQL/Redis. Any Queues?
   - Features like Groups/Policies give you the edge to control the ride habbits of your employees.
- Delivery app for businesses that need instant delivery -> `delivery.grabcab.co/welcome` allowing companies to book a cab on demand!
   - Consider SPA in future and connect to NodeJS.
   - NodeJS will connect to MySQL (RDBMS) and Redis (in-memory cache) or Redis Clustered Pool.
- Platform must be scalable, all bookings to be shifted through a Mothership API.
   - There are other APIs that will be hooked to the Mothership API - These are micro-APIs that will involve breaking business functions into more manageable services.
     - **Notification API** (This allows us to Queue the task of sending a notification aka Email/SMS). We can also plug-in a Message Broker/AMQP (RabbitMQ) 
     - **Geo API** (This allows us to obtain traffic, route, telematics data, data will potentially be cached from vendors e.g. Google Maps, Mapbox, Here, etc)
     - **Logging API** (This allows us to consolidate logging from every app, APIs, SDKs, etc to a single place). The logging API is internally port mapped by Reverse-Proxy. We can also do the following to scale the infrastructure. Firstly consider adding a Message Broker to drip-feed high I/O (TCP) frequency. Secondly hook to a logging service which will allow you to visualise the data and search, you could consider ELK (Elastic-Logstash-Kibana).
     - **Dispatch API** (The dispatch API is responsible for many moving parts that involve for example Geo fencing, proximity ETA (lat/lng), S2 Geometry)
        - MongoDB (Allows us to store flat structured data - JSON - driver_id,lat,lng,reg, etc)
          - driver_id (UUID) / Sync to RDBMS (Primary/Foreign Key) ?
          - firstname 
          - lastname
          - vehicle_id (UUID) - Mapped to vehicle data stored in RDBMS.
          - lat (last recorded coordinate)
          - lng (last recorded coordinate)
          - ??
       - Can we scale MongoDB [x]-[x]-[x] Horizontal (not a fan of Vertical), what about any latency?   
     - **Payment API** - Money talks! So does depending on things like currency conversions, locale, PSP (Payment Service Provider) and what locale? 


- gRPC / Apache Thrift (Binary message encode/decode - Faster TCP Transmission)
   - Break this down in terms of how we can shift data at the speed of light between systems!
  
### Driver Signup

For Driver Signup there are a few obstacles to tackle for example Licensing Authority, Country based restrictions and so on.

- Stepped process (/driver-signup/xxxx-xxxx-xxxx/3)
   - RDBMS keeps track of the last step taken and the details captured in field `step`
   - Store driver UUID mapping on browser (localStorage API/Cookie) to allow them to continue, however secure by asking to confirm mobile no?
   - All documents to be stored on Amazon S3
   - Processes to wipe-out those that do not complete signup within [x] days including wiping any S3 objects - step dependent.
   - Once final step complete, we trigger other actions such as send notification to onboarding team to process and respond to the driver
   - AOB > ??

...
