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
  * $Efficiency(n, p) = \frac{Speedup(n, p)}{p}$;
  * $Efficiency(n, p) = \frac{\sigma(n) + \phi(n)}{p \cdot \sigma(n) + \phi(n) + p \cdot \kappa(n, p)}$;
  * **Ideal efficiency** is $1$;
  * $0 \leq Efficiency(n, p) \leq 1$;

### Amdahl's Law

* **Amdahl's Law** states that the **speedup** of a program using multiple processors in parallel computing is **limited** by the **time needed** for the **sequential fraction** of the program;
  * $f$: Fraction of the program that is sequential - $f(n) = \frac{\sigma(n)}{\sigma(n) + \phi(n)}$;
  * $p$: Number of processors;
  * The **speedup** is **limited** by the **sequential fraction** of the program;
  * **Fixed problem size** $n$;
  * Measures **strong scalability**.

### Gustafson's Law

* **Gustafson's Law** states that the **speedup** of a program using multiple processors in parallel computing is **limited** by the **time needed** for the **parallel fraction** of the program;
  * $s$: Fraction of the program that is parallel - $s(n) = \frac{\sigma(n)}{\sigma(n) + \frac{\phi(n)}{p}}$;
  * $p$: Number of processors;
  * The **speedup** is **limited** by the **parallel fraction** of the program;
  * Fixed **execution time** $T$;
  * Measures **weak scalability**.

### Karp-Flatt Metric

* Amdahl's Law and Gustafson's Law ignore the **communication overhead** ($\kappa(n, p)$);
* **Karp-Flatt Metric** includes the **communication overhead**;
* **Experimentally determined serial fraction** $e$;  
  * $e(n, p) = \frac{\sigma(n) + \kappa(n, p)}{\sigma(n) + \phi(n)}$;
  * $f$: Fraction of the program that is sequential;
  * $p$: Number of processors;
  * Measures **strong scalability**.

### Isoefficiency

* **Scalability** of a parallel system measures the ability to **increase performance as the number of processors increases**;
* A scalable system **maintains efficiency** as the number of processors increases;
* **Isoefficiency** is a way to **measure scalability**;
  * **Isoefficiency** is the **problem size** that can be solved in a **fixed time**;
  * $Isoefficiency(n, p) = \frac{n}{p^2 \cdot T_{parallel}(n, p)}$;
  * $n$: Problem size;
  * $p$: Number of processors;
  * $T_{parallel}(n, p)$: Execution time of the parallel program;
  * **Isoefficiency** is the **problem size** that can be solved in a **fixed time**.