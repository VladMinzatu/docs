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
- ad-hoc analysis (notebooks)
- stream processing
- serving
- monitoring
- AB testing
- load testing

### Infrastructure
- Avoid an overload of technologies. You want to use the right tools for the job, but maintaining a large number of different systems adds considerable overhead and makes the stack difficult to understand for new joiners. As a general rule, avoid having multiple systems that do the same thing. If multiple systems achieve similar things but with different performance characteristics, think if you really need the benefits of both.It may make strategic sense to add a new technology that does something you are already capable of doing, but better. In that case, incorporating the new technology should include moving the existing functionality to the new system and deprecating the old one.

- prefer automation and implementing extra security as code, to tedious repetitive error-prone manual tasks

### Serving
- serving should be flexible enough to apply arbitrary logic, but make it as light as possible
- push work to off-line as much as possible, as long as the latency allows 

### Monitoring
- push all metrics (including offline and streaming job metrics to a centralized system). Having a centralized system can allow for uniform and reusable solutions to tasks such as alerting, graphing and outlier detection.
- for batch (offline) jobs include checks (e.g. basic sanity checks, or even offline evaluation) as part of the DAG and fail the whole pipeline if something fails.


### Documentation
- keep a playbook of best practices for intervention in case of incidents
