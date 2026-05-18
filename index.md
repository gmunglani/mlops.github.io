---
layout: page
title: Machine Learning Operations
permalink: /
---

# Practical guide from dev -> prod

Generating impact from your Data Science & Engineering teams requires two very different types of roles. The first role, which we call Data Science (DS), but is some combination of Data Science, Data Analytics and a bit of ML engineering need to understand where modelling & analytics can help the Organization (be it in Operations, Marketing, R&D) and create POC's to showcase how much usecases can be optimized. Once the POC has been shown to work, it usually needs to be automated and integrated into existing operational systems to truly bring in the ROI. This is where the second role comes in, which we will call Machine Learning Engineering (MLE), but is some combination of Data Engineering, ML Engineering, MLOps and DevOPs, take whatever logic/model was created for the POC, ensure that correct data is fed to it regularly and feed it's output to whatever downstream systems consume it. This process is then automated an continuously monitored both for operational performance as well as business KPI's to ensure that the system is running reliably and bringing the returns promised in the POC.

This is a very high level abstraction on the roles which ofcourse can be separated once more. In truth, there are constructs in which these two roles can be fulfilled by the same people in the same team and others where they fall into different teams in different departments. 


Broadly speaking, the two roles require two different mindsets: The DS roles requires an analytical bent where solving analytical problems takes precedence (R&D), while the MLE roles require a systems builder mentality more concerned with running systems as efficiently as possible is the goal (Production). Generally, the more isolated the roles are from each other in an organization, the longer the solution lifecycle time. The reason for this is at their core, their incentives don't align as the Data Scientist role is usually time-constrained to deliver the POC quickly while the ML engineering role is more concerned with operational performance and reducing maintenence overhead. Therefore, If the Data Scientists builds the POC with ad-hoc packages and tooling, the Engineering team will have to essentially rebuild the entire pipeline from scratch in the production environment, which is highly inefficient. Furthermore, once the solution is in production, if the Data Scientists are not involved in monitoring key metrics and debugging, the solution is unlikely to deliver on the promise of the POC.

The way to solve this issue is to have shared responsibility of both roles in every project ab initio, so that design and tooling decisions are made jointly ensuring that the balance between speed and long-term operability is maintained.

In order to achieve this, this usually means that the two roles jointly develop a standardized development environment which gives the Data Scientist's the flexibility to quickly build and test models, while also allowing the ML Engineers to quickly deploy and monitor models in production with very low overhead.

## Why take on the operational overhead

There are 3 reasons why standardized operation model is useful from the Data Science perspective:
* To allow a Data Scientist to track the development of their own experiment in time. An emphasis on speed makes it likely that mistakes are made, and if a data scientist can trace back every model version they've run, it allows for much faster iteration.
* To ensure inter-usability of the solution between a team of Data Scientists, the development environment needs to be standardized so that any one Data Scientist can be replaced on a specific project, if needed.
* to allow knowledge sharing between Data Scienstists, so that algorithms and methods built for one project can easily be implemented in other projects for rapid protoyping due to a shared environment.

There are 3 reasons why it's useful from a MLE perspective:
* To allow an MLE to easily re-deploy end-to-end solutions (data transformation, modelling, business logic) in a standardized manner with only configuration changes between models
* To allow the MLE to set common security and authentication standards in the spirit of the principal of least privileged access to data, without having to spin up additional costly services. 
* Common logging and monitoring systems across all models allows for standardized KPI's to track with easily defined alerting systems for data/model drift.

There are, however, drawbacks to this approach. The first is the initial time to set up the infrastructure and process to enable the DS and Engineering team to deliver something in a reasonable amount of time. 
The second potential drawback is that implementing new methods in the base code takes longer than if everything we done ad-hoc as it needs to go through the software engineering review process and meet certain standards as well as pass tests


## 1. MLOps Project Cycle

Putting a model into production involves automating the core work of the DS. This involves three core services. The first is running multiple tasks or nodes sequentially on a given schedule. The second is tracking the results of each run to ensure that the model ML models that are created achieve a meet performance requirements and are traceable and reproducible. The final is a monitoring service to monitor all logs and metrics that are created by the pipeline and send notifications when unexpected changes occur. 

### Orchestration
The key aspect to any automation is to schedule pipelines containing a series of tasks in a specific order. Orchestrators can vary in complexity from simply scheduling notebook runs with cron to complex ML-workflow platforms like Kubeflow. Personally, I've found Airflow on Kubernetes to be a middle ground with it's strong orchestration capabilities while leaving the developer to the flexibility to build the ELT and ML logic. It's also backed by a large community and is relatively easy to use, but it should be mentioned that orchestrators such as Prefect and Dagster would work just as well. 

Generally speaking, it's quite easy to spin up a development environment for Airflow, but a production environment is more involved as data persistence, network security and authentication require additional cloud infrastructure. Airflow can also be used a managed service by providers such as Azure, AWS and Astronomer, though this comes with a hefty pricetag.

ML pipelines usually contain some combination of data ingestion, transformation, validation, followed by model training, evaluation and calibration, and ending with embedding business logic and pushing the scores to an end-system or API. The tasks can be split into multiple Airflow DAGS running asynchronously, as some tasks like data ingestion might take place every day while model training occurs on-demand.

There are two ways to effectively design a DAG. The first is fine-grained version, where every node performs a very specific job, resulting in complex pipelines which encode the process logic. The second version is more high-level, wherein each node is more like a task group encoding an entire subprocess like data transformations or model development. In the first case, Airflow shines as a true orchestrator for implementing parallelism and dynamic task generation, while in the latter case, Airflow acts more as a scheduler running sequential jobs while the tasks themselves encode the logic. Monitoring and debugging is easier in the first case as notifications immediately pinpoint where the failure occurred, but it comes with the compute overhead of starting and ending many small tasks rather than focusing on a few large ones. 

 Generally speaking, different task groups are created by developers with different roles (DS, MLE, etc) with different python environments. For example, we use dbt for transformations, scikit-learn for modelling and Evidently for evaluation, which would be performed by MLE's and DS's respectively, using independent code repositories, each with their own unit testing and releases. Each of these repositories would have it's own CI process yielding a versioned docker image for each new release thats pushed to a container registry like ACR. Each node in the Airflow DAG consists of a manifest to pull a container dedicated to that task group and run it with a given configuration that's mounted in at runtime. This is achieved by using the KubernetedPodOperator, which means that during the DAG runthrough, every node cleans up after it has been run, ensuring complete isolation between task groups and no dependency conflicts between unrelated packages.
 

### Tracking
Tracking ML experiments can be as simple as saving th
### Monitoring

## 2. Pipelines

## 2. Services

### Security

### Authentication

## 3. Code development
* **Email:** [your-data-privacy-email@example.com](mailto:your-data-privacy-email@example.com)
* **GitHub:** [Your GitHub Profile URL]

***

## 4. Model lifecycle


### Site Navigation

You can find more information about our other projects here:

* [About Us](/about/)
* [Projects](/projects/)

*(Note: The default Minima theme will not display a list of blog posts with this configuration.)*
