# Classification and Analogizers

...

## Similarity Measures

> **Similarity measures** concern with **quantifying how alike two records are**. They show **higher values for more similar records**.

* **Euclidean Distance** - the most common distance measure. It is the **square root of the sum of the squared differences** between the values of the attributes.
  * $d(x,y) = \sqrt{\sum_{i=1}^{n}(x_i - y_i)^2}$
* **Manhattan Distance** - the sum of the absolute differences between the values of the attributes. Also known as **block distance** or **Minkowski distance**.
  * Interesting for **categorical data**.
  * $d(x,y) = \sum_{i=1}^{n}|x_i - y_i|$
* **Chebyshev Distance** - the maximum absolute difference between the values of the attributes.
  * Do not weight the attributes differently.
  * $d(x,y) = \max_{i=1}^{n}|x_i - y_i|$
* **Cosine Similarity** - the cosine of the angle between the two vectors.
  * Adequate when the **magnitude of the vectors is not important**.
  * $sim(x,y) = \frac{\sum_{i=1}^{n}x_iy_i}{\sqrt{\sum_{i=1}^{n}x_i^2}\sqrt{\sum_{i=1}^{n}y_i^2}}$
* **Contingency Table** - a table that shows the **joint frequency distribution** of two binary variables;

|  | $y$ | $\neg y$ |
| --- | --- | --- |
| $x$ | $\alpha$ | $\epsilon_1$ |
| $\neg x$ | $\epsilon_0$ | $\beta$ |

* $sim(x,y) = \frac{\alpha}{\alpha + \epsilon_0 + \epsilon_1}$
* $d(x,y) = \frac{\epsilon_0 + \epsilon_1}{\alpha + \epsilon_0 + \epsilon_1}$

* **Jaccard Similarity** - the ratio between the size of the intersection and the size of the union of two sets;
  * More useful in presence of **asymmetric (unbalanced) variables**.
  * $sim(x,y) = \frac{|x \cap y|}{|x \cup y|}$