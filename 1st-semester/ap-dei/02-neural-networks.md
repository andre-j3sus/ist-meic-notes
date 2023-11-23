# Neural Networks

> **Neural networks** are a class of **machine learning** models inspired by the **brain**. They are non-linear models that can be used for **regression** and **classification**.

## Definition

* Inspired by biological **neurons**;
* An **artificial neuron** is a **function** $f: \mathbb{R}^d \rightarrow \mathbb{R}$;
  * It receives a **vector** of **inputs** $x \in \mathbb{R}^d$, **weights** $w \in \mathbb{R}^d$ and a **bias** $b \in \mathbb{R}$:

$$
z(x) = w^T x + b = \sum_{i=1}^d w_i x_i + b
$$

The **activation function** $g$ is a function that **transforms** the **output** of the **neuron**:

$$
h(x) = g(z(x)) = g(w^T x + b), where g: \mathbb{R} \rightarrow \mathbb{R}
$$

---

## Activation Functions

The typical **activation functions** are:

* **Linear**: $g(z) = z$;
  * No **squashing**;
* **Sigmoid**: $g(z) = \sigma(z) = \frac{1}{1 + e^{-z}}$;
  * Squashes $z$ to $[0, 1]$;
  * Output can be interpreted as a **probability**;
  * Positive, bounded and strictly increasing;
* **Hyperbolic tangent**: $g(z) = tanh(z) = \frac{e^z - e^{-z}}{e^z + e^{-z}}$;
  * Squashes $z$ to $[-1, 1]$;
  * Bounded and strictly increasing;
* **Rectified Linear Unit (ReLU)**: $g(z) = relu(z) = max(0, z)$;
  * Non-negative, increasing but **not upper bounded**;
  * Not differentiable at $z = 0$;
  * Leads to neurons with **sparse activities**-

Later, we will see:

* **Softmax**: $g(z) = \frac{e^{z_i}}{\sum_{j=1}^d e^{z_j}}$;
* **Sparsemax**: $g(z) = argmin_{p \in \Delta_d} ||p - z||_2^2$;
* **Max-pooling**: $g(z) = max(z)$.

---

## Feedforward Neural Networks

* To solve non-linear problems, we can **stack** several **neurons**;
* **Multi-layer neural networks**: use intermediate layers between the input and the output layers;
* Each hidden layer computes a representation of the input and propagates it to the next layer - **feedforward neural networks**.

### Single Hidden Layer

* Consider a task that involves several **inputs** $x \in \mathbb{R}^d$ and a **single output** $y \in {0, 1}$;
* Include an **intermediate layer** of $K$ **hidden units** ($h \in \mathbb{R}^K$) between the input and the output layers;
* **Hidden layer pre-activation**: $z(x) = W^(1) x + b^(1)$, with $W^(1) \in \mathbb{R}^{K \times d}$ and $b^(1) \in \mathbb{R}^K$;
* **Hidden layer activation**: $h(x) = g(z(x))$, with $g$ being the **activation function** $g: \mathbb{R^K} \rightarrow \mathbb{R^K}$;
* **Output layer activation**: $y(x) = \sigma(w^(2) h(x) + b^(2))$, with $w^(2) \in \mathbb{R^K}$ and $b^(2) \in \mathbb{R}$.

### Multiple Classes

* **Multiple output units**, one for each class;
* Each output estimates the conditional probability of the input belonging to that class - **softmax** activation function:

$$
o(z) = softmax(z) = \begin{bmatrix} \frac{e^{z_1}}{\sum_{j=1}^d e^{z_j}} \\ \vdots \\ \frac{e^{z_d}}{\sum_{j=1}^d e^{z_j}} \end{bmatrix}
$$

* **Hidden layer pre-activation**: $z^(l)(x) = W^(l) h^(l-1)(x) + b^(l)$, with $W^(l) \in \mathbb{R}^{K^(l) \times K^(l-1)}$ and $b^(l) \in \mathbb{R}^{K^(l)}$;
* **Hidden layer activation**: $h^(l)(x) = g(z^(l)(x))$, with $g$ being the **activation function** $g: \mathbb{R^{K^(l)}} \rightarrow \mathbb{R^{K^(l)}}$;
* **Output layer activation**: $y(x) = softmax(w^(L) h^(L-1)(x) + b^(L))$, with $w^(L) \in \mathbb{R^{K^(L)}}$ and $b^(L) \in \mathbb{R}$.

---

## Universal Approximation Theorem

> _A Neural Network with a **single hidden layer** and a **linear output layer** can **approximate any continuous function** to **arbitrary accuracy** if the **hidden layer has enough units**._ - Cybenko, 1989

* **Deeper networks** (with more hidden layers) can **approximate functions more efficiently**;
* The number of linear regions carved out by a deep neural network with $D$ inputs, depth $L$ and $K$ hidden units per layer is $O((\frac{K}{D})^{D(L-1)} K^D)$;
* So neural networks can **approximate any function** with a **single hidden layer**: we only need to find the **right parameters** - **training**.

---
---

## Training Neural Networks

Training a neural network means **finding the right parameters** for the **weights** and **biases** of the **neurons**, learning them from data - samples of **inputs** and **outputs**.

### Empirical Risk Minimization

* Goal: choose parameters $\theta := {(W^{(l)}, b^{(l)})}_{l=1}^{L+1}$ that **minimize the empirical risk**:

$$
\mathcal{L}(\theta) := \lambda \omega(\theta) + \frac{1}{N} \sum_{n=1}^N L(f(x_i; \theta), y_i)
$$

* ${x_i, y_i}_{i=1}^N$ is the **training set**;
* $L(f(x_i; \theta), y_i)$ is the **loss function**;
* $\omega(\theta)$ is the **regularization term**;
* $\lambda$ is the **regularization constant**.

* **Gradient descent** is **too slow**, because it requires a full pass over the data to update the weights;
* **Stochastic gradient descent** is **faster**, because it updates the weights after each sample;
* **Mini-batch stochastic gradient descent** is **even faster**, because it updates the weights after each mini-batch of samples;
  * A **mini-batch** is a **subset** of the **training set**, ${j_1, \dots, j_B}$, with $B$ samples, $B \ll N$:

$$
\nabla_{\theta} \mathcal{L}(\theta) = \lambda \nabla_{\theta} \omega(\theta) + \frac{1}{B} \sum_{i=1}^B \nabla_{\theta} L(f(x_{j_i}; \theta), y_{j_i})
$$

The weights are updated as follows:

$$
\theta^{(t+1)} = \theta^{(t)} - \eta \frac{1}{B} \sum_{i=1}^B \nabla_{\theta} L_{j_i}(\theta^{(t)})
$$

* **Loss function** $L$ should match as possible what we want to **optimize**;
  * Should be well-behaved - continuous and smooth;
  * **Squared loss** is a good choice for **regression**;
  * **Cross-entropy** loss is a good choice for **multi-class classification**: $L(f(x; \theta), y) = - log((softmax(f(x; \theta)))_y)$;
  * **Sparsemax** loss is a good choice for **multi-class and multi-label classification**.

---

### Backpropagation

We need to find a **procedure** to **compute the gradients** of the **loss function** with respect to the **weights** and **biases** of the **neurons**: $\nabla_{\theta} L(f(x; \theta), y)$.

* The **gradient backpropagation algorithm** is a **recursive algorithm** that computes the **gradients** of the **loss function** with respect to the **weights** and **biases** of the **neurons**;
* It is based on the **chain rule** of **calculus**:

$$
h(x) = f(g(x)) \Rightarrow \frac{dh}{dx} = \frac{df}{dg} \frac{dg}{dx}
$$
