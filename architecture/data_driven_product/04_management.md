# Management

## Rule: Separate the product from the technical implementation

The product may be used in different contexts with the aim of optimizing different KPIs. 
As an owner of the product, you have to be on top of monitoring the performance of your system with respect to the goals in different contexts and carefully track the dependencies and risks.
There are only so many use cases that can be supported, subject to capacity, but it is never OK to sacrifice quality, so do not take on more use cases than can be properly managed.

Instead, consider exposing the algorithm outputs on which the product is built (e.g. as a stream), with clear semantics, so that other products may integrate the signal in their stack.
