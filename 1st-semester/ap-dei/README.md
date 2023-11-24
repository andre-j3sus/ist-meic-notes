# [Deep Learning](https://fenix.tecnico.ulisboa.pt/disciplinas/AP-Dei/2023-2024/1-semestre)

> **Deep learning** is a subfield of machine learning that is concerned with algorithms inspired by the structure and function of the brain called artificial **neural networks**.

## Recap

* [Linear Algebra](../../other-materials/linear-algebra.md);
* [Probability and Statistics](../../other-materials/probability-and-statistics.md);
* [Optimization](../../other-materials/optimization.md);
* [Machine Learning](../../other-materials/machine-learning.md).

## Syllabus

1. [Linear Models](./01-linear-models.md) - linear regression, perceptron, logistic regression, regularization;
2. [Neural Networks](./02-neural-networks.md);

*section in progress...*

---
---

The following is a summary of the course's contents.

### Linear Models

> **Linear models** are a class of **regression** and **classification** models in which the prediction is a linear function of the input variables.

#### Linear Regression: $y = w^T x + b$

* $w$ is a $d$-dimensional vector of **weights**;
* $b$ is a **bias** - usually included in $w$ as a constant feature $x_0 = 1$;
* Given **training data** $D = \{(x_n, y_n)\}_{n=1}^N$, we want to find the **best** $w$ and $b$, so we use want to fit the model, i.e. find the best $w$ and $b$, **minimizing the loss function** - usually, the **square loss**: $L(w, b) = \sum_{n=1}^N (y_n - (w^T x_n + b))^2$;
* **Closed-form solution**: $w = (X^T X)^{-1} X^T y$, where $X = \left[ \begin{matrix} x_1^T \\ x_2^T \\ \vdots \\ x_N^T \end{matrix} \right]$ and $y = \left[ \begin{matrix} y_1 \\ y_2 \\ \vdots \\ y_N \end{matrix} \right]$;
* **Regularization** is a technique used to **reduce overfitting** by **constraining** the **weights** of the model;
  * **Ridge regression** is a **linear regression** model with **regularization**;
    * $L(w, b) = \sum_{n=1}^N (y_n - (w^T x_n + b))^2 + \lambda ||w||^2_2$;
    * **Closed-form solution**: $\hat{w_{ridge}} = (X^T X + \lambda I)^{-1} X^T y$;

#### Maximum A Posteriori (MAP) Estimation

* MAP estimation is a **Bayesian** approach to **estimation**, used to **estimate** the **parameters** of a **distribution** - used in **regularization**;
* **Bayes' rule**: $P(\theta | x) = \frac{P(x | \theta) P(\theta)}{P(x)}$;
  * $\hat{w}_{MAP} = argmax_{w} p(w | y) = argmax_{w} p(y | w) p(w) / p(y) = argmin_{w} \lambda ||w||^2_2 + \sum_{n=1}^N (y_n - w^T \phi(x_n))^2$;
  * The **prior** $||w||^2_2$ is the **regularization term** - regularizer;
  * The **likelihood** $\sum_{n=1}^N (y_n - w^T \phi(x_n))^2$ is the **loss function**;
  * The **regularization constant** is $\lambda = \frac{\sigma^2}{\tau^2}$;
* **Maximum likelihood estimation (MLE)** is a **Bayesian** approach to **estimation**;
  * $\hat{w}_{MLE} = argmax_{w} p(y | w) = argmin_{w} \sum_{n=1}^N (y_n - w^T \phi(x_n))^2$;
  * The **likelihood** $\sum_{n=1}^N (y_n - w^T \phi(x_n))^2$ is the **loss function**;
  * **MLE** is a **special case** of **MAP** estimation, where $\lambda = 0$;

#### Binary Classification -> Perceptron: $y = sign(w^T x + b)$

* **Perceptron** is a **linear model** for **binary classification**;
* Usually, the **bias** $b$ is included in $w$ as a constant feature $x_0 = 1$, and the $w$ vector is **augmented** with $b$;
* The $x$ vector can be represented as $\phi(x)$, where $\phi$ is a **feature map**;
* Algorithm:
  1. Initialize $w$ to zero: $w_0 = 0$;
  2. While not converged, for each $(x_n, y_n)$ in $D$:
     1. Predict: $\hat{y} = sign(w^T x_n)$;
     2. If $\hat{y} \neq y_n$:
        1. Update: $w_{t+1} = w_t + y_n x_n$;
        2. Go to step 2;
* **Perceptron convergence theorem**: if the **training data** is **linearly separable**, the **perceptron algorithm** will **converge** in a **finite number of steps**:
* It **cannot** be used for **non-linearly separable data** - XOR problem;

#### Binary Classification -> Logistic Regression: $y = \sigma(w^T x + b)$

* **Logistic regression** is a **linear model** for **binary classification** - differs from perceptron, because it uses a **sigmoid function (continuous)** instead of a **sign function (discrete)**;
* $P_{W}(y | x) = \frac{exp(w_y^T x + b_y)}{\sum_{y' \in Y} exp(w_{y'}^T x + b_{y'})}$, where $W = \{w_y, b_y\}_{y \in Y}$;
    * Set weights to maximize conditional log-likelihood: $L(W) = \sum_{n=1}^N log P_{W}(y_n | x_n)$;
* No closed-form solution, so we use **stochastic gradient descent**;
* Update rule: $w_{y_n}^{k+1} = w_{y_n}^k + \eta x_n - \eta \sum_{y' \in Y} P_{W^k}(y' | x_n) x_n$;
  
#### Multi-class Classification

* **Multi-class classification** is a **classification** task with **more than two classes**, but there are several strategies to **reduce to binary classification**;
* Parametrized by a **weight matrix** $W \in \mathbb{R}^{d \times |Y|}$ and a **bias vector** $b \in \mathbb{R}^{|Y|}$;
* $\hat{y} = argmax_{y \in Y} (W \phi(x) + b)$;
* Multi-class perceptron algorithm:
  1. Initialize $W$ to zero: $W_0 = 0$;
  2. While not converged, for each $(x_n, y_n)$ in $D$:
     1. Predict: $\hat{y} = argmax_{y \in Y} (W \phi(x_n))$;
     2. If $\hat{y_n} \neq y_n$:
        1. Update: $W_{y_n}^{k+1} = W_{y_n}^k + \phi(x_n)$
        2. Update: $W_{\hat{y_n}}^{k+1} = W_{\hat{y_n}}^k - \phi(x_n)$;
        3. Go to step 2;

---

### Neural Networks

* **Neural networks** are a class of **non-linear models** that are **inspired by the brain**;
* Consist of **neurons** that are **connected** to each other;
* **Pre-activation**: $z(x) = w^T x + b = \sum_{i=1}^d w_i x_i + b$;
* **Activation**: $h(x) = g(z(x))$; $g$ can be:
  * **Linear**: $g(z) = z$ - linear regression;
  * **Sigmoid**: $g(z) = \frac{1}{1 + e^{-z}}$ - logistic regression;
  * **Rectified Linear Unit (ReLU)**: $g(z) = max(0, z)$;
  * **Hyperbolic Tangent (tanh)**: $g(z) = \frac{e^z - e^{-z}}{e^z + e^{-z}}$;
  * **Softmax**: $g(z) = \frac{e^{z_i}}{\sum_{j=1}^k e^{z_j}}$;
  * Others;
* A **feed-forward neural network** is a **neural network** where the **neurons** are **organized in layers** - there are **hidden layers** between the **input layer** and the **output layer**;
  * **Input layer**: $x$ - vector of features;
  * **Hidden layers**: $h^{(1)}, h^{(2)}, \dots, h^{(L)}$;
  * **Output layer**: $y$ - vector of predictions;
  * **Weights**: $W^{(1)}, W^{(2)}, \dots, W^{(L)}$ - each weight matrix is between two layers;
  * **Biases**: $b^{(1)}, b^{(2)}, \dots, b^{(L)}$ - each bias vector is between two layers;
  * **Hidden layer pre-activation**: $z^{(l)}(x) = W^{(l)} h^{(l-1)}(x) + b^{(l)}$;
  * **Hidden layer activation**: $h^{(l)}(x) = g(z^{(l)}(x))$;
  * **Output layer activation**: $f(x) = h^{(L)}(x)$;
* **Universal approximation theorem**: a **feed-forward neural network** with a **single hidden layer** can **approximate any function** - given enough **neurons**;
* Training consists of **finding the best parameters $\theta$** - weights and biases, that **minimize the loss function**: $L(\theta) := \lambda \omega(\theta) + \frac{1}{N} \sum_{n=1}^N l(f(x_n; \theta), y_n)$;
  * $\lambda$ is the **regularization constant**;
  * $\omega(\theta)$ is the **regularization term**;
  * $l(f(x_n; \theta), y_n)$ is the **loss function**;
  * We use **stochastic gradient descent** to **minimize the loss function**: $\nabla_{\theta} L(\theta) = \lambda \nabla_{\theta} \omega(\theta) + \frac{1}{N} \sum_{n=1}^N \nabla_{\theta} l(f(x_n; \theta), y_n)$.