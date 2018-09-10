# Data Driven Product Design Guidelines

### 4 general software design guidelines 
- high cohesion
- loose coupling
- DRY
- test and monitor

### The main architectural components
We can split the main areas of the architecture into:
- Infrastructure - the underlying tech layer and central services used by all the different components
- Data Engineering - the components used for collecting, storing and processing data
- Backend Engineering - the components used to serve live traffic

### Infrastructure
- Avoid an overload of technologies. You want to use the right tools for the job, but maintaining a large number of different systems adds considerable overhead and makes the stack difficult to understand for new joiners. As a general rule, avoid having multiple systems that do the same thing. If multiple systems achieve similar things but with different performance characteristics, think if you really need the benefits of both.It may make strategic sense to add a new technology that does something you are already capable of doing, but better. In that case, incorporating the new technology should include moving the existing functionality to the new system and deprecating the old one.

- Prefer automation and implementing extra security as code, to tedious repetitive error-prone manual tasks

- Stay DRY and avoid duplication of efforts, increased costs and maintenance overhead by building centralized services around common tasks, such as CI/CD, load testing, monitoring, AB Testing, data storage and transfer, data processing and job scheduling.

##### Monitoring
- push all metrics (including offline and streaming job metrics to a centralized system). Having a centralized system can allow for uniform and reusable solutions to tasks such as alerting, graphing and outlier detection.

- for batch (offline) jobs include checks (e.g. basic sanity checks, or even offline evaluation) as part of the DAG and fail the whole pipeline if something fails.

### Data Engineering

##### Data Lake 

##### Data Processing
- The purposes: ad-hoc analysis, batch jobs, streaming jobs, AB test analysis
- Beneficial to have a unified interface for the tasks, like Databricks or Netflix' approach with parameterized notebooks + scheduling. Thus, create a clear boundary around the data access and processing => good for compliance and keeps you from creating a mess, because everything is done in a few standard ways and easy to track.
- The scheduling itself can be handled externally and take care

### Serving
- serving should be flexible enough to apply arbitrary logic, but make it as light as possible
- push work to off-line as much as possible, as long as the latency allows 

### Documentation
- keep a playbook of best practices for intervention in case of incidents
