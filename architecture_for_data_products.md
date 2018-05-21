This is WIP and early stages at that. Reading on at this point is ill-advised.

# Data Product Architecture
The aim of this document is to outline the main considerations that go into designing the architecture of a data-driven/data-intensive software product. I attempt to keep the description abstract, avoiding implementation details such as languages, frameworks, software tools or deployment platform as far as possible. I also don't address a very specific type of application. The only property that I presume of the application is that it delivers value by consuming and processing data. 

Having said that, the architecture is quite specific and incorporates certain design decisions where alternatives would certainly be possible. Again, the goal is to highlight the requirements and constraints that one needs consider when designing such applications.

### The architecture diagram

### Guiding principles
- use the right tools for the job, but avoid technology overload. You may want to use Spark and tensorflow side-by-side, but your old Hadoop jobs maybe should be migrated to Spark as well. Prefer building competence in the technologies you use rather than choosing a 4th language for the 3rd service you're implementing.
- reduce the number of services/moving parts (e.g. rely on the message bus for moving data around for any purpose, group functionality into a small set of services/components, but avoid building monoliths, while maintaining high-cohesion and loose coupling)

### Documentation
Have a high level spec with a diagram (similar to above) and link to it repositories and technologies used.
Be clear about what needs to be documented and make absolutely sure that it is always done! Documentation is worthless if it's not maintained and maintaining it takes a **lot** of disciplin. My guideline: it's worth being disciplined with the documentation from the start, but keep it high-level enough that it doesn't become too much of a burden. 

### The three modes of computation
Move things offline as far as possible
The architectural components:
- batch jobs - output queriable
- streaming jobs - output queriable
- API/FE: registry of functions which have access to the queriable output and implement arbitrary logic.

### Quality monitoring
Logging on request level => live monitoring of performance and KPIs and data for ad-hoc analysis later.
Offline evaluation results
Ad-hoc analysis - make sure you have a way to reference it all
Move all metrics over the message bus (offline eval, online kpis)
Implement alerting at a high level and have finer-grained metrics in place for deep-dives.

### Business use-cases
That was the fun part. Now that we have a good infrastructure in place, product can use it to drive business use cases.
- write down use cases and link them to implementation (in the front-end?)
