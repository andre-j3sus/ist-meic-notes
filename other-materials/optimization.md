# Optimization

> **Optimization** is the process of adjusting the parameters of a model to minimize the error of the model on the training data.

## Minimizing a Function

* **Minimization** is the process of finding the input that results in the smallest value of the function;
* Given a function $f(x)$, the goal is to find the value of $x$ that minimizes $f$.
* **Global minimum** is the smallest value of the function over its entire domain: for any $x \in \real^n$, $f(x^*) \leq f(x)$;
* **Local minimum** is the smallest value of the function over some small region of the domain: for any $||x - x^*|| \leq \epsilon$, $f(x^*) \leq f(x)$.

## Convex Functions

* Function $f: \real^n \rightarrow \real$ is **convex** if the line segment between any two points on the graph of the function lies on or above the graph:

$$
f(\lambda x + (1 - \lambda)x') \leq \lambda f(x) + (1 - \lambda)f(x')
$$

* **Strictly convex** if the line segment lies strictly above the graph:

$$
f(\lambda x + (1 - \lambda)x') < \lambda f(x) + (1 - \lambda)f(x')
$$

* If $f$ is **convex**, then any **local minimum** is also a **global minimum**;
* If $f$ is **strictly convex**, then any **local minimum** is also the **unique global minimum**.

## Gradients and Minimization

* Given $f: \real^n \rightarrow \real$, the **gradient** of $f$ is the vector of partial derivatives:

$$
\nabla f(x) = \left[ \frac{\partial f(x)}{\partial x_1}, \frac{\partial f(x)}{\partial x_2}, \dots, \frac{\partial f(x)}{\partial x_n} \right]^T
$$

* $x^*$ is local minimizer $\implies \nabla f(x^*) = 0$.

## Hessians and Convexity

* Given $f: \real^n \rightarrow \real$, the **Hessian** of $f$ is the matrix of second partial derivatives:

$$
H_{ij} = \frac{\partial^2 f(x)}{\partial x_i \partial x_j}
$$

$$
\nabla^2 f(x) = \left[ \begin{matrix}
\frac{\partial^2 f(x)}{\partial x_1^2} & \frac{\partial^2 f(x)}{\partial x_1 \partial x_2} & \dots & \frac{\partial^2 f(x)}{\partial x_1 \partial x_n} \\
\frac{\partial^2 f(x)}{\partial x_2 \partial x_1} & \frac{\partial^2 f(x)}{\partial x_2^2} & \dots & \frac{\partial^2 f(x)}{\partial x_2 \partial x_n} \\
\vdots & \vdots & \ddots & \vdots \\
\frac{\partial^2 f(x)}{\partial x_n \partial x_1} & \frac{\partial^2 f(x)}{\partial x_n \partial x_2} & \dots & \frac{\partial^2 f(x)}{\partial x_n^2}
\end{matrix} \right]
$$

* **Positive semidefinite** Hessian $\iff$ $f$ is **convex**;
* **Positive definite** Hessian $\iff$ $f$ is **strictly convex**.

## More on Gradients

* Gradient of **quadratic form** $f(x) = x^T A x$: $\nabla f(x) = (A + A^T) x$;
* If $A$ is **symmetric**, then $\nabla f(x) = 2 A x$;
* Particular case: $f(x) = x^T x = ||x||^2$, then $\nabla f(x) = 2 x$.
* If $f(x) = x^T b = b^T x$, then $\nabla f(x) = b$.
* If $g(x) = f(Ax), then \nabla g(x) = A^T \nabla f(Ax)$.
* If $g(x) = f(a \cdot x)$, then $\nabla g(x) = a \cdot \nabla f(a \cdot x)$.

## Gradient Descent

> **Gradient descent** is an iterative algorithm that starts with an initial guess $x_0$ and repeatedly moves in the direction of the negative gradient $\nabla f(x)$ until convergence.

$$
x^{(t+1)} = x^{(t)} - \alpha \nabla f(x^{(t)})
$$

* $\alpha$ is the **step size** or **learning rate** - crucial for convergence and performance.