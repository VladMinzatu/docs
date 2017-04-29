# Microservices Quick Reference

## Basics

### Single Responsibility Principle in the Services world
Gather together those things that change for the same reason and separate the things that change for different reasons.

### How small is small enough?

Possible guidelines:
  * Codebase is not too big to be handled by a small team.
  * Small enough that it can be re-written in 2 weeks.

### Benefits of a microservices architecture
  * Services can be changed and deployed independently - smaller cycles, less likely something will go wrong in one deployment.
  * Communication through technology-agnostic APIs - decoupling
  * Technology heterogeneity - services can use different technologies that are best suited for what they are trying to achieve (in terms of functionality and performance). Adopting new technologies is also easier.
  * Resilience - handle failures of certain services without bringing down everything.
  * Scaling - different services can be scaled independently - we don’t need to scale everything together => cost savings
  * Smaller teams on small and isolated code bases are more productive.
  * Microservices are cheap to replace, as opposed to changing a problematic module in a monilithic project.

### Microservices vs. SOA
Microservices can be seen as a specific approach to doing SOA.
SOA was underspecified and driven by vendors, whereas Microservices emerged from real-world experience with large scale systems.

### Drawbacks to using shared libraries for communicating between services.
  * They don’t provide true technology heterogeneity. 
  * Reduces the ability to deploy changes in isolation.
  * Reduces the ability to scale parts of the system independently. 
  * For client libraries (for a service API) there is a danger that logic that should be on the service side starts leaking into the client.

### Splitting the Monolith into modules as an alternative?
  * Still limited in the ability to use new technologies.
  * Limited in the ability to scale independently.
  * In practice, modules tend to become tightly coupled with the rest of the code. (modular separation doesn’t deliver in the real world)

### Drawbacks of microservices?
A technology stack that is too diverse can make it hard to hire people and move people between teams. Also, letting everyone choose their own storage solution hinders the capability to build the knowledge of how to run these storage systems at scale.


## Evolutionary Architecture

### Main concern
Be worried about what happens between the boxes and liberal in what happens inside (use few protocols for communication between services).

### A principled approach to defining the architecture in a microservice system
Strategic goals of the company => Architectural principles => Design and delivery practices
  * Strategic goals: high level, maybe not technology specific (e.g. scalable business - self-service for customers, support expanding to new markets)
  * Architectural principles: rules defined to align to a higher goal (e.g. having consistent interfaces between services and data flows)
  * Design and delivery practices: e.g. use REST, eliminate integration databases.

### What should be constant from system to system? (as opposed to allowing autonomy)
  * All services should emit health and general monitoring-related metrics in the same way (either pulled from or pushed to a central location)
  * Logging should also be centralized.
  * Interface technologies (for integration) should be limited: 1-2 accepted standards (e.g. REST). This also includes standards beyond picking the technology itself (how will you handle pagination or versioning)

### What should be centralized in a Microservice Architecture?
  * Monitoring
  * Log/event collection
  * A/B Testing
  
## Modeling microservices

### The main principles guiding the modeling of microservices.
  * Loose coupling: services can be changed and deployed independently of each other. A loosely coupled service knows as little as it needs to about the services with which it collaborates. This also means there shouldn’t be too many calls from one service to another.
  * High cohesion: related behavior should sit together and unrelated behavior should sit elsewhere. We want to be able to make a change in one place and release the change as soon as possible.
It is also less costly to start with broader scopes than needed and then splitting the monolith than to decompose prematurely and create boundaries that should not be there.
Think in terms of business capabilities instead of data: ask first “what does this service do?” and then “what data does it need to do that?”

Microservices should be obtained by making a vertical, business-focused slice through a monolithic system, not a horizontal slice.
A horizontal slice leads to the same changes having to be applied to both systems.

## Integration

### Considerations for choosing integration technologies
Avoid breaking changes as often as possible: e.g. consumers should not be impacted by the addition of new fields.
Keep the APIs used for communication between services technology-agnostic: avoid integration technologies that dictate what technology stack is used to implement the services.

### Why database integration is bad
Introduces coupling between services that use it: if one service needs to change something, it affects other services.
It breaks cohesion too: behavior related to changing how the DB is accessed needs to be updated in all services using it at the same time.

### Synchronous vs. Asynchronous communication among services
This is the single most important consideration when defining the integration policy between services.
  * Synchronous: a call is made, which blocks until the operation completes.
  * Asynchronous: don’t know, don’t care if the call succeeded; fire and forget
These two styles of communication enable two different styles of collaboration:
  * request/response
  * Event-based (don’t tell other services what to do; just fire events that they may react to)

### Orchestration vs. Choreography for communication
  * Orchestration: there is a central brain making all necessary requests on an action (e.g. customer signs up, then request the sending of a welcome email, request the sending by mail of a welcome pack, etc.)
  * Choreography: an event is fired (async) and other services know what they have to do when the event happens. This is more decoupled, but logic is more implicit. This also requires more involved monitoring to make sure everything works properly.
We can, of course, mix and match!

### Examples of request/response technologies:
  * RPC: making a remote call look like a local call. Some require the same technology (e.g. Java RMI), while others don’t, but require a shared definition. RMI or protocol buffers are binary, while SOAP uses XML. Downsides: technology coupling (to a greater or lesser degree). They also may hide too much: networks fail and communication over them should be treated differently.
  * REST: communicates representations of resources, usually over HTTP.

### Implementing asynchronous event-based collaboration
Typically through message brokers. Main consideration: keep this middleware dumb and keep the smarts in the endpoints.

### How to do versioning of an API
  * Defer breaking changes as far as possible (easier with integration technologies such as REST, very hard for database integration). Make sure Postel’s Law is observed.
  * Use semantic versioning or /vx API versioning
  * Coexist different versions of the API so as to not force consumers to upgrade in lock-step and still be able to deploy services independently.
  * Coexist different versions of the service (only if you absolutely must!). Some major drawbacks, though: bugs have to be addressed in two codebases which have to stay in sync (also in terms of state that they manage).

## Splitting the Monolith

### Main drivers in deciding how to split a monolith.
Aim to increase cohesion and loose coupling: a monolith keeps things together that should not be together and any change needs to be deployed along with everything else and may impact anything else.
It’s all about finding seams that can become service boundaries. 

## Deployment

### How Continuous Integration works
There is a CI server that detects whenever new code is checked in, it checks it out, builds it and makes sure tests pass. 
As a result of this process, an artifact is built, which can then be deployed. The CI server itself can store and keep track of these artifacts in some repository.
The best approach is to have separate repositories for each microservice, to avoid building everything together every time and having to figure out what actually changed from one build to another.

## What a build pipeline does
Separates the build into stages. For example, we may want to have a stage for fast tests and one for slow tests (which we don’t need to run if the fast tests fail).
The idea is that the artifact is built and then it is run through the stages in the pipeline. The process stops as soon as any stage fails.
This is the key to CD. A CD tool can model the entire path to production through stages for our software.

## Monitoring

### Monitor in a microservice architecture
  * Monitor the small things and aggregate centrally to see the big picture:
  * Monitor host health: CPU, used memory, 
  * Collect application logs centrally
  * Monitor application metrics: e.g. response times, number of errors of certain types.

### Tracing calls propagating downstream to other services
Via Correlation IDs: when a call is made to the initial service, generate a UID and send it to all services that you call directly. When you receive such a UID, just pass it along.

## Microservices at scale

### Handling failure at scale
Failure is inevitable. Don't just focus on trying to prevent things from failing, focus on being able to recover quickly.


## References
 * Sam Newman - Building Microservices (2015) (https://www.amazon.com/Building-Microservices-Designing-Fine-Grained-Systems/dp/1491950358)
