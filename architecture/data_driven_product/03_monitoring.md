# Monitoring

## Rule: Define quality metrics

Qualitative measurement of the service's performance is crucial (in addition to the technical health), because for a data-driven service, not all HTTP 200 responses may be the same. And checking the model offline is not sufficient because other things can go wrong at serving time: external data sources fail, models are not updated properly, etc.

Define a small set of clear metrics and monitor them for each context, as a measure of how well the service performs. (e.g. percentage of requests for which a prediction can be made by the model, or percentage of requests for which at least X recommendations are provided by the model).

General monitroing tips apply: not too many metrics and make them comparable across models and contexts and in across time intervals and make them actionable. Higher level metrics can catch more types of errors, but only in the case of big differences. Decide on one or two such metrics, if any. Finer grained metrics are more actionable, but you shouldn't create too many of them, because they come with a maintenance cost.

The monitoring should ideally be based on events that are logged for each invocation of the service. The events can be more verbose and allow for deep-dives (ad-hoc analysis, by e.g. indexing the events directly in an ElasticSearch+Kibana) in case the metrics indicate a potential issue. For example, they should contain the effect of applying domain rules and business rules on top of the model predictions. The events should still be published at the gateway level, but can allow arbitrary metadata to be injected by the models (through the returned responses) to facilitate analysis. Live dashboards and alerting should not rely on this, so structure can be kept fairly loose.

This monitoring should of course be integrated with the general monitoring infrastructure.

## Rule: Don't ignore the data pipelines

The same monitoring recommendations apply to data pipelines. Have quality metrics, like coverage and offline evaluation scores and push metrics from batch and streaming jobs to the centralized monitoring system to take advantage of the standard tooling around alerting, graphing and outlier detection.

For batch jobs, include sanity checks and offline evaluation as part of the DAG and fail the whole pipeline if something fails.

## Rule: Use CI/CD and immutable deployments throughout the stack (including data pipelines)
With a typical production service, you create a new artifact and deploy automatically after a merge to master. This should apply to the model services, their data and the trained models themselves: when a DAG succeeds, this shoudl trigger a redeployment, with self-contained references to immutable model versions and service builds. 
