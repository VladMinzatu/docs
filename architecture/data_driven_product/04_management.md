# Management

## Rule: Separate the product from the technical implementation

The product may be used in different contexts with the aim of optimizing different KPIs. 
As an owner of the product, you have to be on top of monitoring the performance of your system with respect to the goals in different contexts and carefully track the dependencies and risks.
There are only so many use cases that can be supported, subject to capacity, but it is never OK to sacrifice quality, so do not take on more use cases than can be properly managed.

Instead, consider exposing the algorithm outputs on which the product is built (e.g. as a stream), with clear semantics, so that other products may integrate the signal in their stack. This applies to stable solutions that you can commit to maintaining given the demand, of course.

## Rule: Documentation

High level documentation should be kept up to date, with enough detail so that it is useful for new joiners. 
The main points that should be covered:
- Overall architecture (the main components, e.g. batch jobs, streaming jobs, etc.).
- External data sources, derived data (where it is stored, what the semantics are)
- Infrastructure (what technologies are used and how they are used for the different components, monitoring)
- Ways of working (e.g. how different stages of development and prototyping are performed)
- Separate sections that go into more detail on the live components of each type and what purpose they serve
- Log of main architectural decisions (e.g. why some technologies were chosen over others)

Maintaining comprehensive documentation is also a good way to spot processes that can be made more efficient.

Documentation should include a playbook of best practices for intervention in case of incidents.
