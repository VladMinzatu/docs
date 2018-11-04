# The application logic

The product may have to manage different **models** which may potentially be applied in different **contexts**. What typically comes to mind when thinking of a 'data-driven product' is a machine learning model in production. But it's more subtle than that. For example the product may not use machine learning at all (actually advisable in the beginning, in many cases). And some domain-scpecific constraints may need to be enforced on top of any production model. 

I would break down the types of application logic in the following:
1. **model logic**: This is typically a machine learning model trained on the set of features that we feed it, but can include heuristics on top as long as their goal is to improve performance with respect to our target KPIs.
2. **domain rules**: these need to be applied on top of all models (e.g. don't recommend deleted content)
3. **business rules**: these are applicable on top of models, but potentially not in all contexts, so it should be configurable.

## Rule: Model logic should not spill outside the model abstraction
Everything aimed at optimizing our KPIs (i.e. all model logic, according to the classification above) should be contained within a specific model and not spill over into the gateway.

The gateway should be kept as simple as possible, to enable effective monitoring of the performance of the different models in different contexts. So the number of concepts it manages should be kept low. All model logic should be grouped under one model which the gateway uniquely identifies. The gateway then needs to enable monitoring of the performance of different models in different contexts and also how the model predictions interact with the domain rules and business rules implemented inside it. 

Always think of how you'll be monitoring the health of the system.

## Rule: Push all logic as early as possible in the pipeline
To the extent that the data freshness allows, all kinds of application logic should be applied as early as possible in the pipeline. Integrating different data sources at query time increases latency and leaves room for more things to go wrong where there isn't much time to recover.

## Rule: Fail fast
The rule above also allows us to fail fast. Assumptions about the data need to be checked early and strictly and pipelines should fail if issues are detected. This is preferable to diagnosing side effects once the model is live.

## Rule: Be diligent in cleaning upa and keep the stack maintainable
As the system grows, look for opportunities to remove duplicate efforts and unify data pipelines.

Also, as you implement new models, take the time to AB test them in different contexts and look for opportunities to completely deprecate old models. Don't forget to clean up stacks and pipeline steps afterwards.
