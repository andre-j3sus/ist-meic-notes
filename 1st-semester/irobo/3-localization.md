# Localization

> _**Localization** is the process of determining **where a robot is in the world**. Localization is one of the most fundamental problems in robotics. It is the basis for being able to solve other problems such as path planning and navigation._

<p align="center">
    <img src="./imgs/localization.webp" width="400px" alt="Localization"/>
</p>

* Determine the **robot posture (position and orientation)** in the world, at each time instant;
* **Structured** environments (industry, hospitals, etc.);
* **Unstructured** environments (outdoors, space, etc.).

## Localization Approaches

* **Relative Localization** (localization with relative measurements) - **dead reckoning**:
    * **Odometry** - through **wheel motion**;
    * **Inertial navigation** - through **accelerometers** and **gyroscopes** (velocities and accelerations);

> **Dead reckoning** is the process of estimating the current position of a robot by using **sensors** to measure its **movement** and then **using** **starting position** and **direction** of travel as **references**.

* **Absolute Localization**:
    * **Active beacons** - compute absolute location from **distance measurements** to 3 or more active beacons;
    * **Artificial/natural landmarks** - landmarks with **known positions**;
    * **Model matching** - match the **sensor data** with a **map** of the environment.
  
* **Hybrid Localization** - combines **relative** and **absolute** localization:
    * **Kalman filter** - combines **odometry** and **beacon** measurements;
    * **Particle filter** - combines **odometry** and **landmark** measurements.

---

## Coordinate Systems and Transformations

> **Coordinate systems** are used to **represent** the **position** and **orientation** of an object in space. In robotics, they are represented by **frames**. An object can be the robot itself, a sensor, a landmark, etc.

* Coordinate systems are used to transform between **vectors** and **points** (sets of numbers);
* **Frames** are **attached** to **objects**.

<p align="center">
    <img src="./imgs/frames.png" width="500px" alt="Coordinate Systems"/>
</p>

In the image above, we have:

* Two **coordinate systems** (frames) - `{A}` and `{B}`;
* `xA`, `yA`, `zA` - **unit vectors** of `{A}`;
* `xB`, `yB`, `zB` - **unit vectors** of `{B}`;
* `pA` is a **point** in `{A}`.

We can make **transformations** between frames (`{A}` and `{B}`) using **translation** and **rotation**.

### Translation

To translate frame `{B}` wrt (with respect to) frame `{A}`, we need to know the **position** of `{B}` wrt `{A}`. This is done by using a **vector** `pA/Borg` that **points** from `{A}` to `{B}`:

<p align="center">
    <img src="./imgs/translation.png" width="500px" alt="Translation"/>
</p>

Then, we can **translate** a **point** `pB` in `{B}` to `{A}` by adding the **vector** `pA/Borg` to `pB`: `pA = pB + pA/Borg`.

### Rotation

To rotate frame `{B}` wrt frame `{A}`, we need to know the **orientation** of `{B}` wrt `{A}`. This is done by using a **rotation matrix** `R` that **rotates** `{B}` wrt `{A}`: `RA/B`:

<p align="center">
    <img src="./imgs/rotation.png" width="500px" alt="Rotation"/>
</p>

Then, we can **rotate** a **point** `pB` in `{B}` to `{A}` by multiplying `pB` by `RA/B`: `pA = RA/B * pB`.

> **Note:** `RA/B` is orthonormal: `(RA/B)^-1 = (RA/B)^T`.

### Rotation + Translation

If we want to **rotate** and **translate** frame `{B}` wrt frame `{A}`, we can do it by **combining** the **translation** and **rotation** matrices:

<p align="center">
    <img src="./imgs/rotation-translation.png" width="500px" alt="Rotation + Translation"/>
</p>

Then, we can **rotate** and **translate** a **point** `pB` in `{B}` to `{A}` by multiplying `pB` by `RA/B` and adding `pA/Borg`: `pA = RA/B * pB + pA/Borg`.

### Homogeneous Coordinates and Transformations

> Homogeneous Coordinates and Transformations are a **convenient** way to **represent** **points** and **transformations**.

Following the example above, we can represent the rotation and translation matrices as:

`TB/A = [RA/B pA/Borg; 0 0 0 1]`

Another example: Imagine we have 4 frames: `{W}` (world), `{B}` (body of the robot), `{C}` (camera) and `{B}` (ball). We can represent the **position** of the **ball** in the **world** frame as: `pW/ball = TW/B * TB/C * TC/B * pC/ball`.

---

## Relative Localization: Odometry

> **Odometry** is the use of **data** from **encoders** and **gyroscopes** to **estimate** the **robot position** and **orientation**.

* Uses encoders to measure the **distance traveled by each wheel**;
* The **absolute position estimation** is calculated by integrating the **relative translation and orientation** between two encoder readings;
* The performance of odometry is a funcion of the vehicle **kinematics**.

### Kinematics  

> **Kinematics** is the study of **motion** without considering the **forces** that cause it.

Consider the following **kinematic model** of a **differential drive** robot:

<p align="center">
    <img src="./imgs/odometry.png" width="500px" alt="Odometry"/>
</p>

* `{W}` is the **world frame** and `{R}` is the **robot frame**;
* `vr(t)` and `vl(t)` are the **linear velocities** of the **right** and **left wheels**;
* `L` is the **distance** between the **wheels** (wheel base);
* Each drive wheel has an **encoder**, that measures the **distance** traveled by the wheel, in a given time interval `ΔT`;
* `θ(t)` is the **orientation** of the robot wrt the world frame;
* `x(t)` and `y(t)` are the **position** of the robot wrt the world frame.

The **kinematic model** of the robot is:

<p align="center">
    <img src="./imgs/kinematics-model.png" width="200px" alt="Kinematics model"/>
</p>

The kinematic model is used to **estimate** the **robot position** and **orientation** by **integrating** the **linear velocities** of the **wheels**.

### Discretization of the Kinematic Model

> **Discretization** is the process of **approximating** a **continuous** function or **signal** by a **finite** set of **discrete** values. In the case of the kinematic model, we want to **approximate** the **continuous** **linear velocities** of the **wheels** by a **finite** set of **discrete** values.

... <!--Discretization of the kinematis model-->
(_section in construction_)

---

## Absolute Localization Systems: Active Beacon and Landmarks

> **Active beacons** and **landmarks** are used to **estimate** the **robot position** and **orientation**.

* **Active beacons**:
  * Provide very **accurate** **distance** measurements;
  * **Require** **infrastructure** (beacons);

* **Landmarks**:
  * **Natural**: no installation required; recognition may be difficult;
  * **Artificial**: recognition is easier; require installation.

There are two types of absolute localization systems:

* **Trilateration** - uses **distance measurements** to **known beacon sources**;
* **Triangulation** - uses **angle measurements** to **known beacon sources**.

### Trilateration

> **Trilateration** is the process of **determining** the **position** of a **point** by **measuring** its **distance** to **known beacon sources**.

<p align="center">
    <img src="./imgs/trilateration.png" width="350px" alt="Trilateration"/>
</p>

Usual configurations:

* **Active beacons**: 3 or more transmitters mounted at known locations and one receiver on board the robot; e.g. GPS;

* **Landmarks**: one transmitter on board the robot and receivers/transponders/retroreflectors mounted on the environment; e.g. LIDAR.


> ### GPS (Global Positioning System)
>
> **GPS (Global Positioning System)** is a worldwide radio-navigation system formed from a constellation of 24 satellites and their ground stations.
>
> Each satellite transmits data via high frequency radio waves back to Earth that can be received by anyone with a GPS receiver.
>
> **Position of GPS receiver** determined by **trilateration** based on TOF (Time Of Flight) of radio signals from 3 or more satellites.
>
> **Position**: latitude, longitude, altitude.
>
> **DGPS (Differential GPS)**: uses a **fixed reference station** to **improve accuracy**.
  
### Triangulation

> **Triangulation** is the process of **determining** the **position** of a **point** by **measuring** the **angles** to it from **known points** at either end of a fixed baseline, rather than measuring distances to the point directly.

<p align="center">
    <img src="./imgs/triangulation.png" width="350px" alt="Triangulation"/>
</p>

---

## Model Matching

> **Model matching** is the process of **matching** the **sensor data** with a **map** of the environment.

* Correlates **laser scans** with a **metric map** of the robot surroundings, to determine **translation and rotation** of the laser scan wrt the world map;
* A known algorithm is **Iterative Closest Point (ICP)**.

### Iterative Closest Point (ICP)

> **Iterative Closest Point (ICP)** is an **algorithm** employed to **minimize** the **difference** between two **point clouds**.
>
> **Point clouds** are sets of **points** in some **coordinate system**.

<p align="center">
    <img src="./imgs/icp.jpg" width="350px" alt="Iterative Closest Point"/>
</p>

#### Input
* Reference and source **point clouds**;
* Initial estimation of the transformation to align the source point cloud to the reference point cloud;
* Criteria to stop the algorithm.

#### Output
* Refinement of the transformation.

#### Algorithm
1. Find the **closest point** in the **reference point cloud** for each point in the **source point cloud**;
2. Estimate the combination of rotation and translation that **minimizes** the **distance** between the **source point cloud** and the **reference point cloud**;
3. Apply the transformation to the **source point cloud**, using the obtains transformation;
4. If the **criteria** to stop the algorithm is **not met**, go to step 1.

---
---

## Fundamentals of Probabilistic Robotics

> **Probabilistic Robotics** is a new and growing area in robotics, concerned with **perception** and **action** in the face of **uncertainty**.
>
> **Perception** - state estimation (localization, mapping, etc.);
> **Action** - utility optimization (planning, control, etc.).

### Axioms of Probability

* `Pr(A)` - probability that proposition `A` is true;
* `Pr(A) ∈ [0, 1]`;
* `Pr(True) = 1`;
* `Pr(False) = 0`;
* `Pr(A ∨ B) = Pr(A) + Pr(B) - Pr(A ∧ B)`.

### Discrete Random Variables

* `X` denotes a **random variable**;
* `X ∈ {x1, x2, ..., xn}` - `X` can take on one of the values `x1, x2, ..., xn`;
* `P(X = xi)` - probability that `X` takes on the value `xi`;
* `P()` is the **probability mass function** (PMF) of `X`.

### Continuous Random Variables

* `X` takes on values in the **continuum**;
* `p(X = x)` or `p(x)` is the **probability density function** (PDF) of `X`.

### Joint and Conditional Probability

* `P(x, y)` - **joint probability** of `x` and `y` - `P(X = x ∧ Y = y)`;
* If `P(x, y) = P(x)P(y)`, then `x` and `y` are **independent**;
* `P(x | y)` - **conditional probability** of `x` given `y` - `P(X = x | Y = y)`;
* `P(x | y) = P(x, y) / P(y)`;
* If `P(x | y) = P(x)`, then `x` and `y` are **independent**.

### Law of Total Probability

> **Law of Total Probability** is a fundamental rule relating **marginal probabilities** to **conditional probabilities**.

|          Discrete          |          Continuous           |
| :------------------------: | :---------------------------: |
|       `∑x P(x) = 1`        |       `∫x p(x) dx = 1`        |
|    `P(x) = ∑y P(x, y)`     |    `p(x) = ∫y p(x, y) dy`     |
| `P(x) = ∑y P(x \| y) P(y)` | `p(x) = ∫y p(x \| y) p(y) dy` |

### Bayes' Rule

`P(x | y) = P(y | x) P(x) / P(y) = likelihood * prior / evidence`

### Normalization

... _section in construction_

### Conditioning

Law of Total Probability:

* `P(x) = ∫P(x, z) dz`;
* `P(x) = ∫P(x | z) P(z) dz`
* `P(x | y) = ∫P(x | y, z) P(z | y) dz`

### Bayes' Rule for Background Knowledge

`P(x | y, z) = P(y | x, z) P(x | z) / P(y | z)`

### Conditional Independence

* `P(x, y | z) = P(x | z) P(y | z)` - `x` and `y` are **conditionally independent** given `z`;
* `P(x | z) = P(x|y, z)` - `x` and `y` are **conditionally independent** given `z`;
* `P(y | z) = P(y|z, x)` - `x` and `y` are **conditionally independent** given `z`.

---
---

## State Estimation

* Suppose a robot scanning a door;
* The robot obtains measurement `z`;
* What is `P(open | z)`?
  * `open` - door is open;
  * `z` - measurement (e.g. vision sensor - image of the door).

### Casual vs Diagnostic Reasoning

* `P(open | z)` is **diagnostic** - given `z`, what is the probability that the door is open?
* `P(z | open)` is **causal** - given that the door is open, what is the probability of obtaining measurement `z`?
  * Casual knowledge is **easier** to obtain than diagnostic knowledge.
  
Bayes' Rule allows us to use **casual knowledge** to obtain **diagnostic knowledge**:

`P(open | z) = P(z | open) P(open) / P(z)`

### Combining Evidence

* Suppose the robot obtains two measurements `z1` and `z2`;
* What is `P(open | z1, z2)`? or `P(open | z1 ... zn)`?
* Bayes' Rule:

`P(x | z1, ..., zn) = P(zn | x, z1, ..., zn-1) P(x | z1, ..., zn-1) / P(zn | z1, ..., zn-1)`

The **Markov Assumption** states that: `z1, ..., zn` are **conditionally independent** given `x`:

```
P(x | z1, ..., zn) = P(zn | x) P(x | z1, ..., zn-1) / P(zn | z1, ..., zn-1)
                   = hP(zn | x) P(x | z1, ..., zn-1)
                   = ...
```

---
---

## Actions

* Often, the world is **dynamic** since:
  * actions are **performed** by the robot;
  * actions are **performed** by other agents;
  * the world **changes** over time.

* Actions are **never carried out with absolute certainty**;
* In contrast to measurements, **actions generally increase the uncertainty** about the state of the world.

### Modeling Actions

* **Actions** are **probabilistic**;
* To incorporate the outcome of an action `u` into the current belief, we use the conditional pdf(Probability Density Function) `P(x' | u, x)`, where `x'` is the **new state** and `x` is the **previous state**;
* "Executing action `u` in state `x` results in state `x'` with probability `P(x' | u, x)`".

### Integrating the Outcome of Actions

* Continuous case:

`P(x') = ∫P(x' | u, x) P(x) dx`

* Discrete case:

`P(x') = ∑P(x' | u, x) P(x)`

This means that the belief `P(x)` is calculated by **integrating** the **previous belief** `P(x)` with the **outcome of the action** `P(x' | u, x)`.

---

### [Bayes Filters](https://www.youtube.com/watch?v=qDvd5lu80bA)

> **Bayes Filters** are a **family of probabilistic filters** that estimate the **state of a dynamic system** given a **history** of **observations** and **actions**.

* Given:
  * Stream of observations `z` and action data `u`: `dt = {z1, u1, z2, u2, ..., zt, ut}`;
  * **Sensor model** `P(z | x)`;
  * **Action model** `P(x' | u, x)`;
  * **Prior probability** `P(x)` of the state of the system at time `t = 0`;

* Wanted:
  * Estimate of the state `X` of a **dynamic system** at time `t` given the **history** of **observations** and **actions**;
  * The posterior of the state is also called the **belief** `Bel(xt) = P(xt | dt)` - Probability of the state `x` at time `t` given the history of observations and actions - "belief of where you are in the world".

> #### Markov Assumption
>
> The **Markov Assumption** states that the **future** is **independent** of the **past** given the **present** - "if you know your current state, you don't need to know the past states to predict the future state".
>
> * Static world: `P(xt | dt) = P(xt | xt-1, dt-1)`;
> * Independent noise;
> * Perfect model - no approximation errors.
>
> Markov assumption allows us to **recursively** compute the **belief**.

**Bayes Filters**: `Bel(xt) = nP(zt | xt) ∫ P(xt | ut, xt-1) Bel(xt-1) dx`

* `n` - normalization constant;
* `P(zt | xt)` - **sensor model** - probability of obtaining measurement `zt` given the state `xt`;
* `P(xt | ut, xt-1)` - **action model** - probability of obtaining state `xt` given the action `ut` and the previous state `xt-1`;
* `Bel(xt-1)` - **prior belief** - probability of the state `xt-1` at time `t-1` - integrates the previous belief with the outcome of the action.

```
BayesFilter(¬Bel(x), z, u):
    n = 0
    for x in X:
        Bel'(x) = P(z | x) ¬Bel(x)
        n = n + Bel'(x)
    for x in X:
        Bel(x) = Bel'(x) / n    // Update
    for x' in X:
        Bel'(x') = ∫ P(x' | u, x) Bel(x) dx
    return ¬Bel'(x')           // Prediction
```

* **Kalman Filters** are **Bayes Filters** that assume **linear Gaussian** models;
* **Particle Filters** are **Bayes Filters** that use **samples** to represent the **belief**.
* Hidden Markov Models (HMMs) are a special case of **Bayes Filters**.
* Dynamic Bayesian Networks (DBNs) are a generalization of **Bayes Filters**;
* Partially Observable Markov Decision Processes (POMDPs) are a generalization of **Bayes Filters**.

---

### Kalman Filter

> **Kalman Filter** is a **Bayes Filter** that assumes **linear Gaussian** models.

* **Highly efficient** recursive filter;
* **Optimal** for **linear Gaussian** models, but most robotic problems are **non-linear**.

#### Discrete Kalman Filter

* Estimates the state `x` of a discrete-time controlled process that is governed by the linear stochastic difference equation:

`xt = At xt-1 + Bt ut + Et`

with the measurement: `zt = Ct xt + Dt`

* `xt` - state vector;
* `ut` - control vector;
* `zt` - measurement vector;
* `At` - state transition matrix (nxn) - describes how the state evolves from `t-1` to `t`, without controls or noise;
* `Bt` - control matrix (nxl) - describes how the control `ut` changes the state from `t-1` to `t`;
* `Ct` - measurement matrix (kxn) - describes how to map the state `xt` to an observation `zt`;
* `Et` - process noise;
* `Dt` - measurement noise.

#### KF Initialization

* Initial belief is normally distributed: `Bel(x0) = N(x0; µ0, Σ0)`;
  * `µ0` - mean of the initial state;
  * `Σ0` - covariance of the initial state;
  * `N(x; µ, Σ)` - Gaussian distribution with mean `µ` and covariance `Σ`.

#### Non-Linear Dynamic Systems

* `xt = g(ut, xt-1)` - non-linear dynamic system;
* `zt = h(xt)` - non-linear measurement model;

_to be continued..._

---

### Particle Filter

> **Particle Filter** is a **Bayes Filter** that uses **samples** to represent the **belief**.

* **Non-parametric** filter - does not assume a parametric form for the belief;
* Represents belief by **random samples** (**particles**);
* Estimation of **non-Gaussian and non-linear** systems;

_to be continued..._