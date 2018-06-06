This is WIP and early stages at that. Reading on at this point is ill-advised.

# Data Product Architecture
The aim of this document is to outline the main considerations that go into designing the architecture of a data-driven/data-intensive software product. I attempt to keep the description abstract, avoiding implementation details such as languages, frameworks, software tools or deployment platform as far as possible. I also don't address a very specific type of application. The only property that I presume of the application is that it delivers value by consuming and processing data. 

Having said that, the architecture is quite specific and incorporates certain design decisions where alternatives would certainly be possible. Again, the goal is to highlight the requirements and constraints that one needs consider when designing such applications.

### Guiding principles
- use the right tools for the job, but avoid technology overload. You may want to use Spark and tensorflow side-by-side, but your old Hadoop jobs maybe should be migrated to Spark as well. Prefer building competence in the technologies you use rather than choosing a 4th language for the 3rd service you're implementing.
- reduce the number of services/moving parts (e.g. rely on the message bus for moving data around for any purpose, group functionality into a small set of services/components, but avoid building monoliths, while maintaining high-cohesion and loose coupling)

### The high-level architecture
The following main components form the architecture:
- The messaging bus is the backbone for moving data around between the services that need it. Everything that is written to the messaging bus is persisted in the data lake.
- Batch processing components
- Stream processing componenets
- Model storage
- Function repository and API
- Configuration, Simulation and monitoring Front-End

### Batch processing components
For ad-hoc analysis document the results along with the code in a central location.


### Stream processing componenets
### Model storage
### Function repository and API
Request-response component (low-latency API).
Implement arbitrary logic on top of integrating with the different model storages and external APIs.

### Configuration, Simulation and monitoring Front-End
Combine different functions (e.g. fallback logic).
Document the busines cases close to the configuration, referencing tasks.


### Overarching concerns
#### Monitoring
Both performance monitoring and quality monitoring should rely on events pushed to the messaging bus.
These events should have standard formats to be processed by the centralized monitoring service and have the different services push events of those types.

Push data for ad-hoc analysis later.
Implement alerting at a high level and have finer-grained metrics in place for deep-dives.

#### AB Testing

#### Documentation
Have a high level spec with a diagram (similar to above) and link to it repositories and technologies used.
Be clear about what needs to be documented and make absolutely sure that it is always done! Documentation is worthless if it's not maintained and maintaining it takes a **lot** of disciplin. My guideline: it's worth being disciplined with the documentation from the start, but keep it high-level enough that it doesn't become too much of a burden. 


#### Offline evaluation 
Triggered on new model being built (before serving) and for ad-hoc tasks
