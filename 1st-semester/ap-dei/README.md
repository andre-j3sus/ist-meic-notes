# [Deep Learning](https://fenix.tecnico.ulisboa.pt/disciplinas/AP-Dei/2023-2024/1-semestre)

> **Deep learning** is a subfield of machine learning that is concerned with algorithms inspired by the structure and function of the brain called artificial **neural networks**.

## Recap

* [Linear Algebra](../../other-materials/linear-algebra.md);
* [Probability and Statistics](../../other-materials/probability-and-statistics.md);
* [Optimization](../../other-materials/optimization.md);
* [Machine Learning](../../other-materials/machine-learning.md).

## Syllabus

1. [Linear Models](./01-linear-models.md);

*section in progress...*

## Key Concepts

### Linear Models

> **Linear models** are a class of **regression** and **classification** models in which the prediction is a linear function of the input variables.

* Also known as **linear classifiers**;
* **Linear regression** is a linear model for **regression**: $y = w^T x + b$;
  * $w$ is a $d$-dimensional vector of **weights**;
  * $b$ is a **bias** - usually included in $w$ as a constant feature $x_0 = 1$;
  * Given **training data** $D = \{(x_n, y_n)\}_{n=1}^N$, we want to find the **best** $w$ and $b$, so we use want to fit the model, i.e. find the best $w$ and $b$, **minimizing the loss function** - usually, the **square loss**: $L(w, b) = \sum_{n=1}^N (y_n - (w^T x_n + b))^2$;
* **MAP (Maximum a Posteriori) estimation** is a **Bayesian** approach to **estimation**;
  * **Bayes' rule**: $P(\theta | x) = \frac{P(x | \theta) P(\theta)}{P(x)}$;
  * $\hat{w}_{MAP} = argmax_{w} p(w | y) = argmax_{w} p(y | w) p(w) / p(y) = argmin_{w} \lambda ||w||^2_2 + \sum_{n=1}^N (y_n - w^T \phi(x_n))^2$;
  * The **prior** $||w||^2_2$ is the **regularization term** - regularizer;
  * The **likelihood** $\sum_{n=1}^N (y_n - w^T \phi(x_n))^2$ is the **loss function**;
  * The **regularization constant** is $\lambda = \frac{\sigma^2}{\tau^2}$;
* **Binary classification** is a **classification** task with **two classes**;
  * **Logistic regression** is a linear model for **binary classification**: $y = \sigma(w^T x + b)$;
  * The **perceptron algorithm** is a **linear model** for **binary classification**;
* **Multi-class classification** is a **classification** task with **more than two classes**, but there are several strategies to **reduce to binary classification**;
  * $\hat{y} = argmax_{y \in Y} (W \phi(x) + b)$, where $W \in \mathbb{R}^{d \times |Y|}$ is a **weight matrix** and $b \in \mathbb{R}^{|Y|}$ is a **bias vector**.