# Infrastructure

In this document, I want to mention some rules that I have found useful regarding the infrastructure on which a data-driven product is built.

## Rule: Avoid an overload of technologies

You want to use the right tools for the job, but maintaining a large number of different systems adds considerable overhead and makes the stack difficult to understand for new joiners. As a guideling, avoid having multiple systems that do the same thing. If multiple systems achieve similar things but with different performance characteristics, think if you really need the benefits of both.

It may sometimes make strategic sense to add a new technology that does something you are already capable of doing, but better. In that case, incorporating the new technology should include moving the existing functionality to the new system and deprecating the old one. 

# Rule: Strive for automation and platform thinking. Within reason

Prefer automation and implementing extra security as code, to tedious repetitive error-prone manual tasks. Don't forget about cohesion, though: build automation and tooling so as to avoid having processes that require sequences of manual steps and iteracting with different tools as part of one workflow.

Stay DRY and avoid duplication of efforts, increased costs and maintenance overhead by building centralized services around common tasks, such as CI/CD, load testing, monitoring, AB Testing, data storage and transfer, data processing and job scheduling.

However, while platform thinking is a great thing, creating central services introduces a tradeoff between flexibility and convenience. Offering central services for basic infrastructure tasks (deploying and monitoring), plus documentation on how to deploy different kinds of applications may be preferable to investing in building fully managed central services for specific kinds of applications. These can be very expensive to develop and may end up being too restrictive. Also, staying DRY needn't be done at all costs. All central services need to be supported in the long run. If that cannot be guaranteed, some duplication of efforts across different teams is probably preferable to begin with.

# Rule: Document the use of the infrastructure centrally

Have central documentation for using the infrastructure. Maintaining comprehensive documentation is also a good way to spot processes that can be made more efficient.

Documentation should include a playbook of best practices for intervention in case of incidents.

# Case Study

In the following, I want to give an example tech stack. The example is meant to capture the rules above, but also goes beyond that, mentioning very specific choices of technologies. Countless (and potentially arguably better) other options are available. I will not justify my choices.

* **Underlying compute infrastructure**: *AWS* and *Kubernetes*. Specifically, using AWS managed services for all stateful services (i.e. data storage, including the message bus) and Kubernetes for all stateless services (including batch and streaming jobs).
* **Message bus**: *Kinesis*, *Kafka*
* **Data Lake (persistent storage)**: S3
* **Stream processing**: *Spark* (Scala), *Akka Streams*
* **Batch processing**: *Spark* (Scala), *Tensorflow*, *Python* (single-host tools) and *Airflow* for scheduling and monitoring (but not execution)
* **Ad-hoc analysis**: *Jupyter* with single host or backed by Spark or Tensorflow cluster
* **Fast storage**: *Redis*, *DynamoDB*, *ElasticSearch*
* **Serving/Gateway**: *Scala* + *Finagle*
