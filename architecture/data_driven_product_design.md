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

- Prefer automation and implementing extra security as code, to tedious repetitive error-prone manual tasks. Build automation and tooling so as to avoid having processes that require sequences of manual steps and iteracting with different tools as part of one workflow.

- Stay DRY and avoid duplication of efforts, increased costs and maintenance overhead by building centralized services around common tasks, such as CI/CD, load testing, monitoring, AB Testing, data storage and transfer, data processing and job scheduling.

##### Monitoring
- push all metrics (including offline and streaming job metrics to a centralized system). Having a centralized system can allow for uniform and reusable solutions to tasks such as alerting, graphing and outlier detection.

- for batch (offline) jobs include checks (e.g. basic sanity checks, or even offline evaluation) as part of the DAG and fail the whole pipeline if something fails.

### Data Engineering

##### Data Processing
- The purposes: ad-hoc analysis, batch jobs, streaming jobs, AB test analysis
- Beneficial to have a unified interface for the tasks, like Databricks or Netflix' approach with parameterized notebooks + scheduling. Thus, create a clear boundary around the data access and processing => good for compliance and keeps you from creating a mess, because everything is done in a few standard ways and easy to track.
- The scheduling itself can be handled externally and monitoring should be integrated with the centralized monitoring solution.

### Serving
- Serving has to be fast, so do the miniumum amount of work possible on each request. So push work to off-line as much as possible, as long as the latency for computing the result is acceptable.

- Serving should be flexible enough to apply arbitrary logic so abstractions for very flexible models are necessary to allow for algorithmic innovation.

- However, qualitative measurement of the service's performance is crucial (in addition to the technical health). A small set of clear metrics need to be defined and monitored and expectations set for each use case and model (e.g. percentage of requests for which a prediction can be made by the model, or percentage of requests for which at least X recommendations are provided by the model). 

- If you need to support business rules, make sure you only reserve that capability for hard and fixed requirements. For example, if you're recommending videos to a user, you may have a strict requirement (in some contexts) to not recommend videos  if the user has already seen them. This is a concern that can be implemented as a business rule on top of the model abstraction, as it can be applicable to multiple models+contexts. You will then have to monitor what effect this business rule has on the performance of the model: does it often leave you with no recommendations at serving time? Then the model may need to be tweaked to play better with this non-negociable rule. However, what you don't want is to abuse the business rule capability for tweaks that can improve the model's performance (e.g. querying some other data source at run-time and re-ranking results). It may be tempting to do so, because it is very easy and conventient to implement, but don't underestimate the cost of then monitoring the impact of this business rule on different models. It is pointless to build capabilities to configure and tweak models if you don't run AB tests to then optimize the configurations in different contexts. But that takes a long time, so it's best to make a deliberate decision to try and incorporate new signals into your models and follow the typical workflow of validating offline and doing AB testing. **Just because something can be done faster is no excuse for not doing it right**. This is why it's important to restrict the business rules capabilities on top of models to the non-negotiable rules: because it removes the need to test and tweak the interaction between models and business rules - instead, you just test and compare models. So essentially, the domain would have two entities: a **context** and a **model**. The context has associated business rules and the model has an associated prediction logic. AB tests then compare different models in a given context. Ideally, models and contexts are immutable in their configurations, but if mutation is allowed, it should only be possible to do that when there are no AB tests running involving said model and context.


### Documentation
- keep a playbook of best practices for intervention in case of incidents
