# Linear Algebra

This is a summary of the linear algebra concepts used in the course:

- [Linear Algebra](#linear-algebra)
  - [Matrices and Vectors](#matrices-and-vectors)
  - [Matrix Transpose and Matrix Product](#matrix-transpose-and-matrix-product)
    - [Properties](#properties)
  - [Norms](#norms)
  - [Special Kinds of Matrices and Vectors](#special-kinds-of-matrices-and-vectors)
  - [Eigendecomposition](#eigendecomposition)
  - [Matrix Inverse](#matrix-inverse)
  - [Quadratic Form and Positive Semidefinite Matrices](#quadratic-form-and-positive-semidefinite-matrices)

## Matrices and Vectors

* $A \in \mathbb{R}^{m \times n}$: **matrix** with $m$ rows and $n$ columns:

$$
A = \begin{bmatrix}
    a_{11} & a_{12} & \dots & a_{1n} \\
    a_{21} & a_{22} & \dots & a_{2n} \\
    \vdots & \vdots & \ddots & \vdots \\
    a_{m1} & a_{m2} & \dots & a_{mn}
\end{bmatrix}
$$

* $x \in \mathbb{R}^{n}$: **(column) vector** with $n$ rows and $1$ column:

$$
x = \begin{bmatrix}
    x_{1} \\
    x_{2} \\
    \vdots \\
    x_{n}
\end{bmatrix}
$$

* A **row vector** is a matrix with $1$ row and $n$ columns;
* A **scalar** is a matrix with $1$ row and $1$ column (i.e. a number).

---

## Matrix Transpose and Matrix Product

* **Transpose** of a matrix $A \in \mathbb{R}^{m \times n}$ is the matrix $A^T \in \mathbb{R}^{n \times m}$ such that $A^T_{ij} = A_{ji}$;
* Matrix $A$ is **symmetric** if $A = A^T$;
* The **product** of two matrices $A \in \mathbb{R}^{m \times n}$ and $B \in \mathbb{R}^{n \times p}$ is the matrix $C \in \mathbb{R}^{m \times p}$:

$$
C = AB \quad \text{where} \quad C_{ij} = \sum_{k=1}^n A_{ik} B_{kj}
$$

* The **inner product** (or **dot product**) of two vectors $x, y \in \mathbb{R}^n$ is the scalar $x^T y \in \mathbb{R}$:

$$
<x, y> = x^T y = y^T x = \sum_{i=1}^n x_i y_i
$$

* The **outer product** of two vectors $x, y \in \mathbb{R}^n$ is the matrix $xy^T \in \mathbb{R}^{n \times n}$:

$$
xy^T = \begin{bmatrix}
    x_1 y_1 & x_1 y_2 & \dots & x_1 y_n \\
    x_2 y_1 & x_2 y_2 & \dots & x_2 y_n \\
    \vdots & \vdots & \ddots & \vdots \\
    x_n y_1 & x_n y_2 & \dots & x_n y_n
\end{bmatrix}
$$

* The **Hadamard/Schur product** of two vectors $x, y \in \mathbb{R}^n$ is the vector $x \circ y \in \mathbb{R}^n$ such that $(x \circ y)_i = x_i y_i$:

$$
x \circ y = \begin{bmatrix}
    x_1 y_1 \\
    x_2 y_2 \\
    \vdots \\
    x_n y_n
\end{bmatrix}
$$

### Properties

* **Associativity**: $(AB)C = A(BC)$;
* It is **not commutative**: $AB \neq BA$ - unless $A$ or $B$ is a scalar;
* **Transpose of a product**: $(AB)^T = B^T A^T$;
* **Transpose of sum**: $(A + B)^T = A^T + B^T$.

---

## Norms

* The **norm** of a vector is its **length** or **magnitude**;
* The euclidean norm (or $l_2$ norm) of a vector $x \in \mathbb{R}^n$ is:

$$
\|x\|_2 = \sqrt{\sum_{i=1}^n x_i^2} = \sqrt{x^T x} = \sqrt{<x, x>}
$$

* More generally, the $l_p$ norm of a vector $x \in \mathbb{R}^n$ is:

$$
\|x\|_p = \left(\sum_{i=1}^n |x_i|^p\right)^{1/p}
$$

* The $l_1$ norm of a vector $x \in \mathbb{R}^n$ is: $\|x\|_1 = \sum_{i=1}^n |x_i|$;
* The $l_\infty$ norm of a vector $x \in \mathbb{R}^n$ is: $\|x\|_\infty = \max_i |x_i|$.

---

## Special Kinds of Matrices and Vectors

* **Diagonal matrix**: a matrix $D \in \mathbb{R}^{n \times n}$ such that $D_{ij} = 0$ for $i \neq j$;
* **Identity matrix**: a diagonal matrix $I \in \mathbb{R}^{n \times n}$ such that $I_{ii} = 1$ for all $i$;
  * $AI = IA = A$ for any matrix $A \in \mathbb{R}^{m \times n}$ - **neutral element** of matrix multiplication;
* **Upper triangular matrix**: a matrix $U \in \mathbb{R}^{n \times n}$ such that $U_{ij} = 0$ for $i > j$;
* **Lower triangular matrix**: a matrix $L \in \mathbb{R}^{n \times n}$ such that $L_{ij} = 0$ for $i < j$.

---

## [Eigendecomposition](https://www.youtube.com/watch?v=PFDu9oVAE-g)

* **Eigenvector** of a matrix $A \in \mathbb{R}^{n \times n}$ is a nonzero vector $\vec{v} \in \mathbb{R}^n$ such that $A \vec{v} = \lambda \vec{v}$ for some scalar $\lambda \in \mathbb{R}$;

$$
Ax = \lambda \vec{v} \quad \Leftrightarrow \quad (A - \lambda I) \vec{v} = 0
$$

* The eigenvalues of a diagonal matrix are its diagonal elements;
* The matrix **trace** is the sum of its diagonal elements: $\text{tr}(A) = \sum_{i=1}^n A_{ii} = \sum_{i=1}^n \lambda_i$;
* The matrix **determinant** is the product of its eigenvalues: $\text{det}(A) = |A| = \prod_{i=1}^n \lambda_i$;
  * $|A| = |A^T|$;
  * $|AB| = |A| |B|$;
  * $|\alpha A| = \alpha^n |A|$.

---

## Matrix Inverse

* A matrix is **invertible** if there exists a matrix $B$ such that $AB = BA = I$;
* The **inverse** of a matrix $A \in \mathbb{R}^{n \times n}$ is the matrix $A^{-1} \in \mathbb{R}^{n \times n}$ such that $AA^{-1} = A^{-1}A = I$;
* $det(A) \neq 0 \quad \Leftrightarrow \quad A \text{ is invertible}$;
* $det(A^{-1}) = \frac{1}{det(A)}$;
* If $A$ is invertible, then $Ax = b$ has a unique solution $x = A^{-1} b$ for any $b$;
* Computational cost of inverting a matrix is $O(n^3)$.

Properties:

* $(A^{-1})^{-1} = A$;
* $(AB)^{-1} = B^{-1} A^{-1}$;
* $(A^T)^{-1} = (A^{-1})^T$.

---

## [Quadratic Form](https://www.youtube.com/watch?v=0yEiCV-xEWQ) and Positive Semidefinite Matrices

Given a matrix $A \in \mathbb{R}^{n \times n}$ and a vector $x \in \mathbb{R}^n$, the **quadratic form** is:

$$
x^T A x = \sum_{i=1}^n \sum_{j=1}^n A_{ij} x_i x_j \quad \text{where} \quad A \in \mathbb{R}^{n \times n}
$$

This can be written as:

$$
\begin{bmatrix}
    x_1 & x_2 & \dots & x_n
\end{bmatrix}
\begin{bmatrix}
    A_{11} & A_{12} & \dots & A_{1n} \\
    A_{21} & A_{22} & \dots & A_{2n} \\
    \vdots & \vdots & \ddots & \vdots \\
    A_{n1} & A_{n2} & \dots & A_{nn}
\end{bmatrix}
\begin{bmatrix}
    x_1 \\
    x_2 \\
    \vdots \\
    x_n
\end{bmatrix}
$$

* A **positive semidefinite matrix** is a symmetric matrix $A \in \mathbb{R}^{n \times n}$ such that $x^T A x \geq 0$ for all $x \in \mathbb{R}^n$;
  * $A$ is positive semidefinite $\quad \Leftrightarrow \quad \lambda_i(A) \geq 0$ for all $i$;
* A **positive definite matrix** is a symmetric matrix $A \in \mathbb{R}^{n \times n}$ such that $x^T A x > 0$ for all $x \in \mathbb{R}^n$;
  * $A$ is positive definite $\quad \Leftrightarrow \quad \lambda_i(A) > 0$ for all $i$.