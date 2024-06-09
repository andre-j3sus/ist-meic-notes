# Parallel Numerical Algorithms

## Methods for Solving Linear Systems

### Direct Methods

Direct methods are based on the factorization of the matrix of the system. The most common direct method is the **Gaussian Elimination**: consists of two steps:

- **Forward Elimination**: the matrix $Ax = b$ is transformed into an upper **triangular matrix** $Tx = c$ - **LU (Lower-Upper) Factorization**. The matrix $A$ is factorized into the product of a lower triangular matrix $L$ and an upper triangular matrix $U$ ($A = LU$).
- **Backward Substitution**: the solution is obtained by solving the system of equations $Tx = c$.

### Iterative Methods

Iterative methods consist on generating an **initial guess** and then iteratively improving it until a certain **convergence criterion** is met. The most common iterative method is the **Jacobi Method**:

- The Jacobi method does not always converge, but it is **guaranteed to converge** under conditions that are often satisfied, though convergence rate may be very slow;
- Faster convergence can be achieved by using **Gauss-Seidel Method**, by using each new component value as soon as it has been computed rather than waiting until next iteration;
  - The Gauss-Seidel method does not always converge, but it is **guaranteed to converge** under conditions somewhat **weaker** than those for the Jacobi method - **converges about twice as fast as Jacobi method**;

---

## Linear Second-order Partial Differential Equations (PDEs)

- Partial Differential Equations (PDEs) are used to model physical phenomena that depend on more than one independent variable;
- **Second-order PDEs** are the most common type of PDEs in science and engineering;

#### Ghost Points

- **Ghost points** are memory locations used to store redundant copies of data, held by neighboring processors, to facilitate communication between processors;
- This **simplifies** parallel algorithms;
