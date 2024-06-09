# Search, Monte Carlo and Sorting Algorithms

- **Decision problems** -determine if there is a solution based on a set of constraints;
- **Optimization problems** - find a solution that minimizes or maximizes a given cost function, under a set of constraints.

## Parallel Search

- **Backtrack search** - use a **depth-first search** to explore the solution space. The search is **guided by a heuristic function** that estimates the cost of extending a partial solution to a complete solution;
- **Branch and bound** - also performs a **depth-first search**, but is able to **prune** the search tree by **bounding** the cost of extending a partial solution;
  - Sequential algorithm uses a **priority queue** to store partial solutions;
  - Parallel algorithm uses **multiple priority queues**, each processor has its own queue and can **exchange** partial solutions with other processors.

---

## Monte Carlo Methods

- Solve problems using **random sampling**;

---

## Parallel Sorting

- **Parallel quicksort** has **poor balancing** of list sizes;
- **Hyperquicksort** - sort elements before broadcasting the pivot;
  - However, as the number of processors increases, each processors share of list size decreases, and lists become **unbalanced**;
- **Parallel Sorting by Regular Sampling (PSRS)**: get sample values from all processes before choosing median;
- **Odd-Even Transposition Sort** is based on the bubble sort algorithm.
