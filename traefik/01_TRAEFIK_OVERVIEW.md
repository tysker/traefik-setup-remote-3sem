# Træfic

## What is Træfic? https://doc.traefik.io/traefik/

- Træfik is an open-source Edge Router*
- It automatically discovers the right configuration for each service
- Træfik inspects the infrastructure, where it finds relevant information and discovers which service serves which request.
- no restarts, no connection interruptions
- Træfik is based on the concept of EntryPoints, Routers, Middlewares and Services.


1. **EntryPoints:** EntryPoints are the network entry points into Træfik. They define the port which will receive the packets, and whether to listen for TCP or UDP.
2. **Routers:** A router is in charge of connecting incoming requests to the services that can handle them.
3. **Middlewares:** Attached to the routers, middlewares can modify the requests or responses before they are sent to your service
4. **Services:** Services are responsible for configuring how to reach the actual services that will eventually handle the incoming requests.

## Edge Router*

Træfik is an Edge Router, it means that it's the door to our platform, and that it intercepts and routes every
incoming request: it knows all the logic and every rule that determine which services handle which requests
(based on the path, the host, headers, etc.).

## Auto Service Discovery
Where traditionally edge routers (or reverse proxies) need a configuration file that contains every possible route to
your services, Træfik gets them from the services themselves.

It means that when a service is deployed, Træfik detects it immediately and updates the routing rules in real time.
Similarly, when a service is removed from the infrastructure, the corresponding route is deleted accordingly.


## Traefik Providers

Træfik supports multiple providers, which are responsible for providing the list of services that Træfik should handle.

Some examples of providers that Traefik can interface with include:

 - Docker: Traefik can be configured to automatically discover services that are being managed by Docker and create routes to them. 
 - Kubernetes: Traefik can act as a Kubernetes ingress controller, managing traffic to services within a Kubernetes cluster. 
 - File: Traefik can read a static file to set up its routes. 
 - Consul, etcd, ZooKeeper: These are examples of Key-Value pair based stores that Traefik can use to dynamically update its configuration.

Træfik detects the provider to use by looking at the prefix of the configuration keys. For example, if the key starts 
with docker, Træfik will use the Docker provider.

Each provider has its own set of configuration settings that you can use to control how Traefik interacts with it. 
The specifics of these settings will depend on the provider.

In general, the provider abstraction allows Traefik to be flexible and able to work in a wide variety of environments, 
from simple static file-based configurations to complex dynamic environments like Kubernetes or Docker Swarm.


## Træfik Dashboard

Træfik comes with a built-in web UI that lists the services that are registered in Træfik. It is available at /dashboard on the Træfik entrypoint. http://localhost:8080/dashboard/
It's useful to check that Træfik is correctly configured and that the services are correctly registered. It also provides a way to check the health of the services.
https://doc.traefik.io/traefik/operations/dashboard/


## Træfik Load Balancing

Træfik can load balance requests to multiple instances of a service. It can use different load balancing algorithms, such as round-robin, least connections, or IP hash.
https://doc.traefik.io/traefik/routing/providers/docker/#load-balancing