# Perception

> A mobile robot requires **perception** to gain information about the **environment** and **its own state**.

Perception is the process of **extracting information** from **sensor data**. This includes:

* **Object detection and recognition**;
* **Localization**;
* **Obstacule detection**;
* **Internal state** (e.g. battery level).

---
---

## Sensors

> **Sensors** are devices that **measure physical quantities** and **convert** them into **electrical signals**.

### Classification

Sensors can be classified by two criteria: **type of information** and **interaction with the environment**.

#### Type of information: Proprioceptive vs Exteroceptive

> **Proprioceptive** sensors measure **internal state** (e.g. battery level).

Examples:

* **Encoders** measure **wheel rotation**;
* **Potentiometers** measure **joint angles**;
* **Gyroscopes** measure **angular velocity**;
* **Accelerometers** measure **linear acceleration**.

> **Exteroceptive** sensors measure **external state** (e.g. distance to an obstacle).

Examples:

* **LIDAR (Light Detection and Ranging)** measures **distance** by emitting a **laser** and measuring the **time** it takes to **reflect**;
* **Sonar** measures **distance** by emitting **sound** and measuring the **time** it takes to **reflect**;
* **Camera** measures **distance** by **triangulation**.
* **Microphones** measure **sound**.

#### Interaction with the environment: Active vs Passive

> **Active** sensors **emit** a **signal** and **measure** the **response**.

Examples:

* **LIDAR** emits a **laser** and measures the **time** it takes to **reflect**;
* **Sonar** emits **sound** and measures the **time** it takes to **reflect**.

> **Passive** sensors **measure** the **signal** **emitted** by the **environment**.

Examples:

* Vision sensors;
* Stereo-vision sensors.

---

## Perception Issues

* Sensors are **noisy**;
* Data must be **processed** to extract relevant information;
* Sensor measurements can be imcomplete;
* The information from several sensors can be combined to improve accuracy.

---
---

## Potentiometers

> Potentiometers are **resistors** with a **variable resistance**;
* The **resistance** is **proportional** to the **angle** of the **shaft**;
* The **resistance** is **measured** by **passing** a **current** through the **potentiometer** and **measuring** the **voltage** drop.

---

## Encoders

> Encoders are **sensors** that **measure** the **rotation** of a **wheel**;
* Encoder = Light Beam + Light Detector + Rotating Disk with a radial grating on its surface;
* The **light beam** is **interrupted** by the **grating**;
* The **light detector** **measures** the **interruptions**;
* The **number** of **interruptions** is **proportional** to the **rotation** of the **wheel**.

There are two types of **shaft encoders**:

* **Absolute** encoders:
  * The output is a **binary number** that **represents** the **orientation** of the **shaft**;
  * Measures directly angular position and infers velocity;
* **Incremental** encoders:
  * The rate at which the **interruptions** occur is **proportional** to the **angular velocity** of the **shaft**;
  * Measures directly rotational velocity and infers relative position.

A incremental encoder, needs to know the initial absolute angle. So why not use an absolute encoder? Because they are more **expensive**.

---

## Odometers

> Odometers are **sensors** that **measure** the **distance** traveled by **vehicles**;

---

## Inertial Sensors (Gyroscopes, Accelerometers, ...)

> Inertial sensors are **sensors** that **measure** the **linear and angular motion** of a **robot**;
>  * Angular motion - **Gyroscopes**;
>  * Linear motion - **Accelerometers**.
* Proprioceptive sensors;
* Used to support **inertial navigation**: estimate the position and orientation of a robot by integrating the motion state.

### Gyroscopes

Types of gyroscopes according to the underlying physical principle:
* **Mechanical** gyroscopes - conservation of angular momentum;
* **Optical** gyroscopes - Sagnac effect;
* **MEMS** gyroscopes - vibrating structure.

Types of gyroscopes according to the **output**:
* **Rate gyroscopes** - measure angular velocity;
* **Rate integrating gyroscopes** - measure orientation.

### Accelerometers

...

### Inclinometers

> **Inclinometers** are **sensors** that **measure** the **angle** of an object reference axis with respect to a **horizontal axis**.

---

## Range Sensors (SONAR, LIDAR, ...)

> Range sensors are **sensors** that **measure** the **distance** to an **object**.

### SONAR - Sound Navigation and Ranging

> SONAR is a **range sensor** that **measures** the **distance** to an **object** by **emitting** a **sound** and **measuring** the **time** it takes to **reflect** - **Echolocation**.

Echolocation is the use of sound waves and echoes to determine where objects are in space. Dolphins and bats use this method to hunt prey and navigate.

### LIDAR - LIght Detection and Ranging

> LIDAR is a **range sensor** that **measures** the **distance** to an **object** by **emitting** a **laser** and **measuring** the **time** it takes to **reflect**.