---
author: Christian Kaestner
title: "17-445: Versioning, Provenance, and Reproducability"
semester: Summer 2020
footer: "17-445 Software Engineering for AI-Enabled Systems, Christian Kaestner"
license: Creative Commons Attribution 4.0 International (CC BY 4.0)
---

# Versioning, Provenance, and Reproducability

Christian Kaestner

<!-- references -->

Required reading: 🗎 Halevy, Alon, Flip Korn, Natalya F. Noy, Christopher Olston, Neoklis Polyzotis, Sudip Roy, and Steven Euijong Whang. [Goods: Organizing google's datasets](http://research.google.com/pubs/archive/45390.pdf). In Proceedings of the 2016 International Conference on Management of Data, pp. 795-806. ACM, 2016. and 
🕮 Hulten, Geoff. "[Building Intelligent Systems: A Guide to Machine Learning Engineering.](https://www.buildingintelligentsystems.com/)" Apress, 2018, Chapter 21 (Organizing Intelligence).

---

# Learning Goals

* Judge the importance of data provenance, reproducibility and explainability for a given system
* Create documentation for data dependencies and provenance in a given system
* Propose versioning strategies for data and models
* Design and test systems for reproducibility

---

# Case Study: Credit Scoring

----
<div class="tweet" data-src="https://twitter.com/dhh/status/1192540900393705474"></div>

----

<div class="tweet" data-src="https://twitter.com/dhh/status/1192945019230945280"></div>

----
```mermaid
graph TD
  i(Customer Data) --> s[Scoring Model]
  h(Historic Data) ==> s
  i --> p[Purchase Analysis]
  p --> s
  s --> l[Credit Limit Model]
  c(Cost and Risk Function) --> l
  m(Market Conditions) --> l
  l --> o(Offer)
  i --> l
```



----

## Debugging?

What went wrong? Where? How to fix?

<!-- discussion -->

----

## Debugging Questions beyond Interpretability

* Can we reproduce the problem?
* What were the inputs to the model?
* Which exact model version was used?
* What data was the model trained with?
* What learning code (cleaning, feature extraction, ML algorithm) was the model trained with?
* Where does the data come from? How was it processed and extracted?
* Were other models involved? Which version? Based on which data?
* What parts of the input are responsible for the (wrong) answer? How can we fix the model?

---

# Data Provenance

Historical record of data and its origin

----

## Data Provenance

* Track origin of all data
    - Collected where?
    - Modified by whom, when, why?
    - Extracted from what other data or model or algorithm?
* ML models often based on data drived from many sources through many steps, including other models

----

## Tracking Data

* Document all data sources
* Model dependencies and flows
* Ideally model all data and processing code
* Avoid "visibility debt"
* 
* Advanced: Use infrastructure to automatically capture/infer dependencies and flows (e.g., [Goods](http://research.google.com/pubs/archive/45390.pdf) paper)

----
## Feature Provenance

* How are features extracted from raw data
    - during training
    - during inference
* Has feature extraction changed since the model was trained?

**Example?**

----
## Model Provenance

* How was the model trained?
* What data? What library? What hyperparameter? What code?
* Ensemble of multiple models?

----
```mermaid
graph TD
  i(Customer Data) --> s[Scoring Model]
  h(Historic Data) ==> s
  i --> p[Purchase Analysis]
  p --> s
  s --> l[Credit Limit Model]
  c(Cost and Risk Function) --> l
  m(Market Conditions) --> l
  l --> o(Offer)
  i --> l
```


----
## Recall: Model Chaining

*automatic meme generator*

```mermaid
graph LR
  i(Image) --> OD[Object Detection] 
  OD --> ST[Search Tweets]
  ST --> SA[Sentiment Analysis]
  SA --> OL[Overlay Tweet]
  i --> OL
```

<!-- references -->
Example adapted from Jon Peck. [Chaining machine learning models in production with Algorithmia](https://algorithmia.com/blog/chaining-machine-learning-models-in-production-with-algorithmia). Algorithmia blog, 2019

----
## Recall: ML Models for Feature Extraction

*self driving car*

```mermaid
graph LR
  L(Lidar) --> PD
  L --> LD
  i(Video) --> PD[Object Detection] 
  PD --> OT[Object Tracking]
  OT --> MT[Object Motion Prediction]
  MT --> Planning
  i --> TL[Traffic Light & Sign Recognition]
  TL --> Planning
  i --> LD[Lane Detection]
  LD --> Planning
  S(Speed) --> Planning
  LDe[Location Detector] --> Planning
```

<!-- references -->
Example: Zong, W., Zhang, C., Wang, Z., Zhu, J., & Chen, Q. (2018). [Architecture design and implementation of an autonomous vehicle](https://ieeexplore.ieee.org/stamp/stamp.jsp?tp=&arnumber=8340798). IEEE access, 6, 21956-21970.


----
## Summary: Provenance

* Data provenance
* Feature provenance
* Model provenance






---
# Practical Data and Model Versioning

----
## How to Version Large Datasets?

<!-- discussion -->

----
## Recall: Event Sourcing

* Append only databases
* Record edit events, never mutate data
* Compute current state from all past events, can reconstruct old state
* For efficiency, take state snapshots
* Similar to traditional database logs

```text
createUser(id=5, name="Christian", dpt="SCS")
updateUser(id=5, dpt="ISR")
deleteUser(id=5)
```

----
## Versioning Datasets 

* Store copies of entire datasets (like Git)
* Store deltas between datasets (like Mercurial)
* Offsets in append-only database (like Kafka offset)
* History of individual database records (e.g. S3 bucket versions)
    - some databases specifically track provenance (who has changed what entry when and how)
    - specialized data science tools eg [Hangar](https://github.com/tensorwerk/hangar-py) for tensor data
* Version pipeline to recreate derived datasets ("views", different formats)
    - e.g. version data before or after cleaning?
*
* Often in cloud storage, distributed
* Checksums often used to uniquely identify versions
* Version also metadata

----
## Versioning Models

<!-- discussion -->

----
## Versioning Models

* Usually no meaningful delta, versioning as binary objects
* Any system to track versions of blobs

----
## Versioning Pipelines

```mermaid
graph LR
  data --> pipeline
  hyperparameters --> pipeline
  pipeline --> model
```


----
## Versioning Dependencies

* Pipelines depend on many frameworks and libraries
* Ensure reproducable builds
    - Declare versioned dependencies from stable repository (e.g. requirements.txt + pip)
    - Optionally: commit all dependencies to repository ("vendoring")
* Optionally: Version entire environment (e.g. Docker container)
* Avoid floating versions
* Test build/pipeline on independent machine (container, CI server, ...)



----
## ML Versioning Tools (see MLOps)

* Tracking data, pipeline, and model versions
* Modeling pipelines: inputs and outputs and their versions
    - explicitly tracks how data is used and transformed
* Often tracking also metadata about versions
    - Accuracy
    - Training time
    - ...


----
## Example: DVC 

```sh
dvc add images
dvc run -d images -o model.p cnn.py
dvc remote add myrepo s3://mybucket
dvc push
```

* Tracks models and datasets, built on Git
* Splits learning into steps, incrementalization
* Orchestrates learning in cloud resources


https://dvc.org/

----
## Example: ModelDB

<iframe width="560" height="315" src="https://www.youtube.com/embed/gxBb4CjJcxQ" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

https://github.com/mitdbg/modeldb

----
## Example: MLFlow

* Instrument pipeline with *logging* statements
* Track individual runs, hyperparameters used, evaluation results, and model files

![MLflow UI](mlflow-web-ui.png)

<!-- references -->

Matei Zaharia. [Introducing MLflow: an Open Source Machine Learning Platform](https://databricks.com/blog/2018/06/05/introducing-mlflow-an-open-source-machine-learning-platform.html), 2018


----
## Aside: Versioning in Notebooks with Verdant

* Data scientists usually do not version notebooks frequently
* Exploratory workflow, copy paste, regular cleaning

<iframe width="560" height="315" src="https://www.youtube.com/embed/LFYmYT7HFSs?start=308" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

<!-- references -->
Further reading: 
Kery, M. B., John, B. E., O'Flaherty, P., Horvath, A., & Myers, B. A. (2019, May). [Towards effective foraging by data scientists to find past analysis choices](http://www.cs.cmu.edu/~marmalade/papers/paper092-Kery-CHI2019.pdf). In Proceedings of the 2019 CHI Conference on Human Factors in Computing Systems (pp. 1-13).


----
## From Model Versioning to Deployment

* Decide which model version to run where
    - automated deployment and rollback (cf. canary releases)
    - Kubernetis, Cortex, BentoML, ...
* Track which prediction has been performed with which model version (logging)



----

## Logging and Audit Traces

* Version everything
* Record every model evaluation with model version
* Append only, backed up


**Key goal: If a customer complains about an interaction, can we reproduce the prediction with the right model? Can we debug the model's pipeline and data? Can we reproduce the model?**


----
## Logging for Composed Models


```mermaid
graph LR
  i(Image) --> OD[Object Detection] 
  OD --> ST[Search Tweets]
  ST --> SA[Sentiment Analysis]
  SA --> OL[Overlay Tweet]
  i --> OL
```

*Ensure all predictions are logged*


----
## Discussion

What to do in movie recommendation and popularity prediction scenarios? And how?

<!-- discussion -->












---

# Fixing Models

<!-- references -->

See also Hulten. Building Intelligent Systems. Chapter 21

----

## Orchestrating Multiple Models

* Try different modeling approaches in parallel
* Pick one, voting, sequencing, metamodel, or responding with worst-case prediction

<!-- colstart -->
```mermaid
graph TD
input --> model1
model1 --> model2
model2 --> model3
model1 --> yes
model2 --> yes
model3 --> yes
model3 --> no
```
<!-- col -->
```mermaid
graph TD
input --> model1
input --> model2
input --> model3
model1 --> vote
model2 --> vote
model3 --> vote
vote --> yes/no
```
<!-- col -->
```mermaid
graph TD
input --> model1
input --> model2
input --> model3
model1 --> metamodel
model2 --> metamodel
model3 --> metamodel
metamodel --> yes/no
```
<!-- colend -->

----

## Chasing Bugs

* Update, clean, add, remove data
* Change modeling parameters
* Add regression tests
* Fixing one problem may lead to others, recognizable only later

----

## Partitioning Contexts

* Separate models for different subpopulations
* Potentially used to address fairness issues
* ML approaches typically partition internally already

<!-- split -->
```mermaid
graph TD
input --> decision[pick model]
decision --> model1
decision --> model2
decision --> model3
model1 --> yes/no
model2 --> yes/no
model3 --> yes/no
```

----

## Overrides

* Hardcoded heuristics (usually created and maintained by humans) for special cases
* Blocklists, guardrails
* Potential neverending attempt to fix special cases
<!-- split -->

```mermaid
graph TD
input --> blocklist
blocklist --> no
blocklist --> model
model --> guardrail 
guardrail --> yes
guardrail --> no
```
 






---
# Reproducability

----
## Definitions

* **Reproducibility:** the ability of an experiment to be repeated with minor differences from the original
experiment, while achieving the same qualitative result
* **Replicability:** ability to reproduce results exactly,
achieving the same quantitative result; requires determinism
* 
* In science, reproducing results under different conditions are valuable to gain confidence
    - "conceptual replication": evaluate same hypothesis with different experimental procedure or population
    - many different forms distinguished "_..._ replication" (e.g. close, direct, exact, independent, literal, nonexperiemental, partial, retest, sequential, statistical, varied, virtual)

<!-- references -->

Juristo, Natalia, and Omar S. Gómez. "[Replication of software engineering experiments](https://www.researchgate.net/profile/Omar_S_Gomez/publication/221051163_Replication_of_Software_Engineering_Experiments/links/5483c83c0cf25dbd59eb1038/Replication-of-Software-Engineering-Experiments.pdf)." In Empirical software engineering and verification, pp. 60-88. Springer, Berlin, Heidelberg, 2010.

----
## Practical Reproducability

* Ability to generate the same research results or predictions 
* Recreate model from data
* Requires versioning of data and pipeline (incl. hyperparameters and dependencies)



----
## Nondeterminism

* Some machine learning algorithms are nondeterministic
    - Recall: Neural networks initialized with random weights
    - Recall: Distributed learning
* Many notebooks and pipelines contain nondeterminism
  - Depend on snapshot of online data (e.g., stream)
  - Depend on current time
  - Initialize random seed
* Different library versions installed on the machine may affect results
* (Inference for a given model is usually deterministic)


----
## Recommendations for Reproducibility

* Version pipeline and data (see above)
* Document each step   
    - document intention and assumptions of the process (not just results)
    - e.g., document why data is cleaned a certain way
    - e.g., document why certain parameters chosen
* Ensure determinism of pipeline steps (-> test)
* Modularize and test the pipeline
* Containerize infrastructure -- see MLOps





---
# Summary

* Provenance is important for debugging and accountability
* Data provenance, feature provenance, model provenance
* Reproducability vs replicability
* Version everything
    - Strategies for data versioning at scale
    - Version the entire pipeline and dependencies
    - Adopt a pipeline view, modularize, automate
    - Containers and MLOps, many tools
* Strategies to fix models


