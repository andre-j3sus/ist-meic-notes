# Analysis of Parallel Performance

* **Performance** is a **critical issue** in parallel computing;
* **Objective** is to **reduce execution time** by **parallelizing** the execution of a program and understand barriers to performance;

### Speedup

* **Speedup** is the **ratio** of the **execution time** of the **sequential program** to the **execution time** of the **parallel program**;
  * $Speedup(n, p) = \frac{T_{serial}(n)}{T_{parallel}(n, p)}$;
  * $n$: Problem size;
  * $p$: Number of tasks (processors, threads, ...);
* $\sigma(n)$ are the inherently sequential computations;
* $\phi(n)$ are the completely **parallelizable** computations;
* $\kappa(n, p)$ is the **parallelization overhead**;
* $Speedup(n, p) = \frac{\sigma(n) + \phi(n)}{\sigma(n) + \frac{\phi(n)}{p} + \kappa(n, p)}$;

### Efficiency

* **Efficiency** is the **ratio** of the **speedup** to the **number of processors**;
  * $Efficiency(n, p) = \frac{Speedup(n, p)}{p} = \frac{\sigma(n) + \phi(n)}{p \cdot \sigma(n) + \phi(n) + p \cdot \kappa(n, p)}$;
  * **Ideal efficiency** is $1$;
  * $0 \leq Efficiency(n, p) \leq 1$;

### Amdahl's Law

* **Amdahl's Law** states that the **speedup** of a program using multiple processors in parallel computing is **limited** by the **time needed** for the **sequential fraction** of the program;
  * $f$: Fraction of operations in a serial computation that must be performed sequentially - $f(n) = \frac{\sigma(n)}{\sigma(n) + \phi(n)}$;
  * The **speedup** is **limited** by the **sequential fraction** of the program: $S(n, p) \leq \frac{1}{f(n) + \frac{1 - f(n)}{p}}$;
  * Shows how execution time **decreases** with **increasing parallelism**;
  * Measures **strong scalability** - **fixed problem size** and **decreasing execution time**;
  * Its **optimistic** because it does not consider the **parallel overhead**.

### Gustafson's Law

* **Gustafson's Law** states that the **speedup** of a program using multiple processors in parallel computing is **limited** by the **time needed** for the **parallel fraction** of the program;
  * $s$: Fraction of sequential operations that must be performed sequentially - $s(n) = \frac{\sigma(n)}{\sigma(n) + \frac{\phi(n)}{p}}$;
  * The **speedup** is **limited** by the **parallel fraction** of the program: $S(n, p) \leq p + (1 - p) \cdot s(n)$;
  * Measures **weak scalability** - **increasing problem size** but **fixed execution time** - **scaled speedup**;

| Law         | Scalability | Problem Size | Execution Time |
| ----------- | ----------- | ------------ | -------------- |
| Amdahl's    | Strong      | Fixed        | Decreasing     |
| Gustafson's | Weak        | Increasing   | Fixed          |

> **Note**: both ignore the **parallel overhead**.

### Karp-Flatt Metric

* Amdahl's Law and Gustafson's Law ignore the **communication overhead** ($\kappa(n, p)$) - **Karp-Flatt Metric** includes the **communication overhead**;
* **Experimentally determined serial fraction** $e$;  
  * $e(n, p) = \frac{\sigma(n) + \kappa(n, p)}{\sigma(n) + \phi(n)}$
  * $e(n, p) = \frac{1 / Speedup(n, p) - 1 / p}{1 - 1 / p}$;
  * Allows for the analysis of the source of **inefficiency** in parallel programs;
  * If `e` is constant, the primary reason for **inefficiency** is **large serial fraction**;
  * If `e` increases with `p`, the primary reason for **inefficiency** is **communication overhead**.
  
### Isoefficiency

* **Scalability** of a parallel system measures the ability to **increase performance as the number of processors increases**;
* A scalable system **maintains efficiency** as the number of processors increases;
* **Isoefficiency** is a way to **measure scalability**;
* **Total overhead**: $T0(n, p) = (p - 1) \cdot \sigma(n) + p \cdot \kappa(n, p)$;
* **Efficiency**: $\epsilon(n, p) = \frac{speedup(n, p)}{p}$;
* **Isoefficiency relation**: To **maintain the same level of efficiency**, as the **number of processors increases**, the **problem size must increase** such that: $T(n, 1) \geq T0(n, p) \cdot C(n, p)$;
  * $T(n, 1)$ is the **execution time** for a **single processor**;
  * $C(n, p) = \frac{\epsilon(n, p)}{1 - \epsilon(n, p)}$;
* **Scalability function** indicates how **memory usage per processor must increase** to **maintain the same level of efficiency**: $M(CT0(n, p)) / p$;
  * $M(n)$ is the **memory usage** for a **single processor**.
