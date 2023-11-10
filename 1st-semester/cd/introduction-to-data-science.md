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

---

## Data Profiling

> _**Data Profiling** is the process of examining the data available from an existing information source (e.g. a database or a file) and collecting statistics or informative summaries about that data._

#### Analysis

Analysis can be classified into two categories:

* **Univariate** - analysis of a single variable;
* **Multivariate** - analysis of multiple variables.

We usually consider four **perspectives** of analysis:

* **Granularity** - the level of detail of the data and precision;
  * e.g. if the data was collected daily, or hourly, per city or per country;
* **Distribution** - the distribution of the data;
  * e.g. normal, uniform, skewed, etc;
* **Sparsity** - the percentage of missing values.
* **Dimensionality** - the number of variables.

### Data Granularity

> _**Data Granularity** is the level of detail of the data._

* The **finer the granularity**, the **more detailed** the data;
* Data at a finer granularity can be **aggregated** to a coarser granularity; **Aggregation** are made through:
  * **Discretization and composition** for numeric data;
  * **Concept hierarchies** for symbolic data - **taxonomies**.

### Data Distribution

> _**Data Distribution** is the distribution of the data._

* Understand data **centrality** and **dispersion**;
* Identify **missing values** and **outliers**;
* **Central Tendency** - mean, median, mode;
* **Histogram** - a graphical representation of the distribution of the data;
* **Discrete Distributions** - Uniform, Bernoulli, Binomial, Poisson and Hypergeometric;
* **Continuous Distributions** - **Normal**, Exponential, Lognormal, Chi-Square;
* **Outliers** - values that are far from the rest of the data;
  * `X` is outlier if `X < μ - nσ` or `X > μ + nσ`, where:
    * `μ` - mean;
    * `σ` - standard deviation (square root of the variance);
    * `n` - number of standard deviations.
* **Measuring Dispersion** - variance (`σ^2`), standard deviation (`σ`), interquartile range (`IQR = Q3 - Q1`), **5-number summary** (`min, Q1, median, Q3, max`) - **boxplot** is a graphical representation of the 5-number summary.

### Data Sparsity

> _**Data Sparsity** is the percentage of missing values._

* Only **present** values are considered in the analysis;
* **Scatter Plot** - a graphical representation of the data - allow the identification of subspaces of the data domain;
  * Allow the identification of dispersion and outliers;
  * Allow the identification of **correlation** between variables;
* **Heat Maps** - graphical representation of matrices, which each cell is colored according to its value;
  * Always **symmetric**, since the correlation between `X` and `Y` is the same as the correlation between `Y` and `X`;
  * The **diagonal** is always **1**, since the correlation between `X` and `X` is always `1`;

### Data Dimensionality

> _**Data Dimensionality** is the number of variables._

* **Extrinsic Dimensionality** - the number of variables in the data - `dim(D) = d`;
* **Intrinsic Dimensionality** - the number of variables that are relevant to the analysis - `k` (`k < d`);
* `n << d` - the **number of records** is much smaller than the **dimensionality** - data tends to be **highly sparse**;
* **Curse of Dimensionality** - the **number of records** required to **cover** the **data domain** **increases exponentially** with the **dimensionality**;
* **Hughes Phenomenon** - the **accuracy** of the **model** **decreases** with the **dimensionality**.