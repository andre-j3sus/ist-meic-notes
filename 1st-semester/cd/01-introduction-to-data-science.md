# Introduction to Data Science

> _**Data Science** is the **nontrivial** extraction of **implicit**, previously **unknown**, and potentially **useful** **information from data**._ (William J. Frawley, AI Magazine, 1995)

* Data Science is an application of **Machine Learning**, one of the major subfields of **Artificial Intelligence**;
* Similar terms: **analytics**, **data mining**, **KDD** (Knowledge Discovery in Databases), etc;
* **nontrivial** - we need more or less complex algorithms to extract the information;
* **implicit** - the information is not explicitly present in the data;
* **unknown** - we do not know the information beforehand;
* **useful** - the information is useful for some purpose.

> ### Artificial Intelligence
>
> _**Artificial Intelligence** is branch of computer science concerned with making computers behave like humans._ (John McCarthy, 1956)
>
> AI Fields:
> * Search;
> * Reasoning;
> * Natural Language Processing;
> * **Machine Learning**;
> * Interaction.

The main subfields of of **Data Science** are:

* **Statistics** - tools for understanding data distribution, sparsity and correlation;
* **Machine Learning** - algorithms for discovering the models behind the data;
* **Databases** - efficiently deal with large amounts of data.

### From Data Engineer to Data Scientist

* **Data Engineer** - deals with the data collection, storage and processing;
* **Data Analyst** - deals with the data exploration and preparation;
* **Data Scientist** - deals with the data modeling and evaluation;
* **?** - deals with prediction and decision making.

---

## Basic Concepts

* **Records** - instances, data objects or observations;
  * Tuples of values described by a set of **variables**;
* **Variables** - attributes, fields, features, dimensions;
  * **Numeric** - real-valued, interval based, ratio;
  * **Symbolic** - binary, nominal, ordinal.
* **Tabular data** - data organized in a table/matrix `n x d`, where:
  * **n** - number of records;
  * **d** - number of variables - **dimensionality** of the data.

> _**Information** is the set of patterns or expectations that underlie the data._

---

## KDD (Knowledge Discovery in Databases) Process
 
1. Define the **goal**;
2. **Data Collection**;
3. **Data Profiling** - characterize the data under analysis with respect to its **distribution**, **sparsity**, **granularity** and **dimensionality**;
4. **Data Preparation** - **Selection** and **Transformation** (integration, cleansing and feature engineering) of the data to be used in the modeling phase;
5. **Modeling**;
   * **Supervised Learning** - classification, forecasting;
   * **Unsupervised Learning** - clustering, pattern mining;
6. **Evaluation** - assess the quality of the model;
   * Evaluates **simplicity**, **utility** (**coverage** and **novelty**) and **certainty** of the model.
