# Rules for Data-Driven Product Design

This document is a collection of guidelines on the design of data-driven software products, which I have collected from my experience working on such software projects.

## 4 general software design guidelines 
Before diving into more specific rules, I would like to highlight 4 simple design principles that generally apply to the design of software systems, from small components to company-wide architectures. I'm not sure if the rules appear in this form elsewhere. There is some resemblence to Kent Beck's 4 rules of simple system design, but the inspiration for this exact set is spread across a number of hard to trace sources. Anyway, the rules are as follows:
* high cohesion - keep similar concerns grouped together
* loose coupling - reduce dependencies among 
* DRY - don't do the same work more than once
* test and monitor - well, this should be clear.

## The architectural components
In this section I will outline the main architectural components (and subcomponents) of a data-driven software system. This categorization will give the structure of the rest of the document, as we will cover rules per component.

The 3 components are:
* **Infrastructure**: this is the foundation layer for the other two components and is concerned with the compute infrastructure and services needed for the development of data-driven applications. Subcomponents would include:
  - Compute infrastructure
  - CI/CD
  - Monitoring
  - Load Testing
  - Documentation repo
* **Data Pipelines**: are concerned with the storage and processing of data. Subcomponents include:
  - Central Message Bus
  - Persistent data storage
  - Streaming jobs
  - Batch jobs
  - Job Scheduling
  - AB Testing
  - Interactive notebooks for ad-hoc analysis
* **Serving**: the services that stakeholders interact with. The subcomponents here are:
  - Storage for fast retrieval - backing the model. This would be fed by data pipelines jobs or directly as a sink for the message bus.
  - Model services - making the actual predictions
  - API Gateway - interface for the outside world to interact with the different models.
  
## Rules for Infrastructure

#### Avoid an overload of technologies

Try to keep some focus. You want to use the right tools for the job, but maintaining a large number of different systems adds considerable overhead and makes the stack difficult to understand for new joiners. As a guideling, avoid having multiple systems that do the same thing. If multiple systems achieve similar things but with different performance characteristics, think if you really need the benefits of both.

It may sometimes make strategic sense to add a new technology that does something you are already capable of doing, but better. In that case, incorporating the new technology should include moving the existing functionality to the new system and deprecating the old one.
Centralized services -> Cohesion - shouldn't have to interact with multiple systems in one workflow, as far as possible.
Documentation - centralized - 

#### Strive for automation and platform thinking. Within reason
Prefer automation and implementing extra security as code, to tedious repetitive error-prone manual tasks. Don't forget about cohesion, though: build automation and tooling so as to avoid having processes that require sequences of manual steps and iteracting with different tools as part of one workflow.

Stay DRY and avoid duplication of efforts, increased costs and maintenance overhead by building centralized services around common tasks, such as CI/CD, load testing, monitoring, AB Testing, data storage and transfer, data processing and job scheduling. 

However, while platform thinking is a great thing, creating central services introduces a tradeoff between flexibility and convenience. Offering central services for basic infrastructure tasks (deploying and monitoring), plus documentation on how to deploy different kinds of applications may be preferable to investing in building fully managed central services for specific kinds of applications. These can be very expensive to develop and may end up being too restrictive. Also, staying DRY needn't be done at all costs. All central services need to be supported in the long run. If that cannot be guaranteed, some duplication of efforts across different teams is probably preferable to begin with.

#### Document the use of the infrastructure centrally
Have central documentation for using the infrastructure. Maintaining comprehensive documentation is also a good way to spot processes that can be made more efficient.

Documentation should include a playbook of best practices for intervention in case of incidents.

## Rules for Data Pipelines

#### Don't ignore monitoring 

Push metrics from batch and streaming jobs to the centralized monitoring system to take advantage of the standard tooling around alerting, graphing and outlier detection.

#### Add basic checks and offline evaluation in your pipeline
For batch jobs, include sanity checks and offline evaluation as part of the DAG and fail the whole pipeline if something fails.

## Rules for Data Pipelines
#### Speed matters here
Serving has to be fast, so do the miniumum amount of work possible on each request. Push work to off-line as much as possible, as long as the latency for computing the result is acceptable.

#### Keep serving flexible
Serving should be flexible enough to apply arbitrary logic so abstractions for very flexible models are necessary to allow for algorithmic innovation. This is why the model services are separated from the API Gateway and they should be minimally constrained (maybe have a fixed API, but implementation language/framework should not be constrained).

#### Monitor the quality of your (gateway) service's responses in each context
Qualitative measurement of the service's performance is crucial (in addition to the technical health), because for a data-driven service, not all HTTP 200 responses may be the same. And checking the model offline is not sufficient because other things can go wrong at serving time: external data sources fail, models are not updated properly, etc.

Define a small set of clear metrics and monitor them for each context, as a measure of how well the service performs. (e.g. percentage of requests for which a prediction can be made by the model, or percentage of requests for which at least X recommendations are provided by the model). 

General monitroing tips apply: not too many metrics and make them comparable across models and contexts and in across time intervals and make them actionable. Higher level metrics can catch more types of errors, but only in the case of big differences. Decide on one or two such metrics, if any. Finer grained metrics are more actionable, but you shouldn't create too many of them, because they come with a maintenance cost. 

This monitoring should of course be integrated with the general monitoring infrastructure.

#### Keep the gateway service simple
Because you want to have very good visibility into the quality of what you are providing to your customers (see above), you should keep things simple and avoid an overload of concepts in the gateway service. You'll probably need the concept of a **context** if your service has multiple stakeholders. And an internal concept of a **model** to iterate on. Try to keep it at that!

If you need to also support business rules, you may want to make this configurable per context, but make sure you only reserve that capability for hard and fixed requirements. For example, if you're recommending videos to a user, you may have a strict requirement (in some contexts) to not recommend videos  if the user has already seen them. This is a concern that can be implemented as a business rule on top of all models. You will then have to monitor what effect this business rule has on the performance of the model: does it often leave you with no recommendations at serving time? Then the model may need to be tweaked to play better with this non-negociable rule. 

However, what you don't want is to abuse the business rule capability for tweaks that can improve the model's performance (e.g. querying some other data source at run-time and re-ranking results). It may be tempting to do so, because it is very easy and conventient to implement, but don't underestimate the cost of then monitoring the impact of this business rule on different models. It is pointless to build capabilities to configure and tweak models if you don't run AB tests to then optimize the configurations in different contexts. But that takes a long time, so it's best to make a deliberate decision to try and incorporate new signals into your models and follow the typical workflow of validating offline and doing AB testing. This is why it's important to restrict the business rules capabilities on top of models to the non-negotiable rules: because it removes the need to test and tweak the interaction between models and business rules - instead, you just test and compare models. 

So essentially, the domain would have two entities: a **context** and a **model** (because the business rules are part of the context). The context has associated business rules and the model has an associated prediction logic. AB tests then compare different models in a given context. Ideally, models and contexts are immutable in their configurations, but if mutation is allowed, it should only be possible to do that when there are no AB tests running involving said model and context.

Again, keep configuration to a minimum, because it makes it very hard to keep track of quality metrics. For example, instead of implementing dynamic filtering capabilities, prefer creating separate contexts for which you get monitoring out of the box. The filtering will still need to be done, but push things back from the serving, for improvements in both performance and clarity.

