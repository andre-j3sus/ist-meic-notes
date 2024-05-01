# Mobile and Ubiquitous Computing

* **Where do we stand?**
  * We have **near-constant access** to computing power and online services - **near-ubiquitous computing**;
  * Thanks to **cellular and Wi-Fi networks**, we can be **online** almost everywhere;
  
* **Where are we going to?**
  * **Calm computing** - computing that **moves into the background** of our lives, doing its job without requiring our full attention;
  * However, today's technology is more likely **jittery technology**:
    * Constant beepings, vibrations, and interruptions;
    * Phantom vibrations - feeling your phone vibrating when it's not.

* **Pros**:
  * **Convenience** - access to information and services anywhere, anytime;
  * **Cheap** access;
  *  Increasing **productivity**;
* **Cons**:
  * **Privacy** concerns;
  * **Security** concerns;
  * **Distraction**.

---

## Definitions

* **Mobile computing**:
  * Deals with **computing and communication** software and hardware aspects related to the use of **mobile devices**;
  * **Mobility**: devices can be easily moved from one place to another;
* **Ubiquitous computing**:
  * Deals with **computing and communication** software and hardware aspects related to the use of **ubiquitous devices**;
  * **Ubiquity**: devices are **everywhere** and **always on**.
  * **Ubiquitous devices** are devices that are **everywhere** and **always on** - most of these devices are mobile, but we can also have an **ubiquitous system** with devices that are not mobile;
  * E.g. **smart homes**, **smart cities**, **smart cars**.
* **Pervasive computing**:
  * **Pervasiveness**: stupid devices with embedded computing.
  * In some literature, there is a distinction between ubiquitous and pervasive computing, where the latter implies the **embedding of computing devices into everyday analog objects**.
* **Localized scalability**:
  * Large number of devices may co-exist in a small space;
  * A system must be able to handle a growing amount of devices and the resulting interaction;
  * **Smart-space**: a space where devices are interconnected and can interact with each other.

---

## Challenges

### Location

* Triangulation:
  * Lateration - distance to known points;
  * Angulation - angle to known points;
* Proximity: nearness to known points;
* Scene analysis: recognizing landmarks;
* **Challenges**: privacy, scale, cost, accuracy and precision.

### Support Variability

* **Context-awareness**:
  * System needs to be **pro-active** and **correct**;
  * Gather information about the user's context, and **predict** what the user will need next;
  * **Context information** can be: location, time, user activity, temperature, etc.
* **Adaptability**:
  * System must be capable of dealing with the variability of the environment;
  * Adapting the environment while remaining **transparent** to the user;

### Deal with Resource Constraints

* **Resource Discovery**: protocols to discover nearby resources;
* **Cyber foraging**: offloading computation to nearby resources;
  * E.g. lack of network connectivity, can be solved with a shared hotspot;

### Provide Constant Access to Devices

* **Replication** and **Consistency**: maintaining multiple consistent copies of data fro accessability and fault tolerance;

* **Offloading**: moving computation to a more powerful device; 
  * Implies deciding if **it is worth** the cost of moving the computation;

* **Energy**: find where battery is being wasted and optimize it;
  * **Challenges**: battery life, energy consumption, energy harvesting. 