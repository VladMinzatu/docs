# Data Driven Product Design Guidelines

### 4 general software design guidelines 
- high cohesion
- loose coupling
- DRY
- test and monitor

### The main architectural components
- message bus
- persistent storage
- batch processing
- stream processing
- serving
- monitoring

### The infrastructure
##### The platform technologies
Avoid an overload of technologies. You want to use the right tools for the job, but maintaining a large number of different systems adds considerable overhead and makes the stack difficult to understand for new joiners.
As a general rule, avoid having multiple systems that do the same thing. If multiple systems achieve similar things but with different performance characteristics, think if you really need the benefits of both.
It may make strategic sense to add a new technology that does something you are already capable of doing, but better. In that case, incorporating the new technology should include moving the existing functionality to the new system and deprecating the old one.

##### Centralized services
The main guideline is staying DRY - avoid duplicate work within teams. Examples:
- *monitoring* - you don't want to deploy systems for metrics collection, alerting and displaying graphs more than once. All components should use one stack for that

### Documentation
