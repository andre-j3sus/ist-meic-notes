# Cloud Datacenters Design and Operation

* **Scale matters**: in a company with **1000 CPUs**, the **largest cost is personnel**, not hardware;
  * In a company with **50000 CPUs**, the **largest cost is hardware and cooling**, not personnel;
* **Energy costs** are a major concern;
  * Power conversion;
  * Networking;
  * Computation;
  * Cooling;
* Energy also impacts the **DC location**:
  * Volumes of cheap energy;
  * Green energy;
  * Proximity to rivers and lakes - usage of water for cooling;
  * Large areas of land - more privacy and security;
  * Distance to other DCs - for redundancy.

---

## Energy Efficiency

* There are two common metrics:
  * **PUE** (Power Usage Effectiveness) - the ratio of total facility power to IT equipment power; $PUE = \frac{Total Facility Power}{IT Equipment Power}$;
  * **DCiE** (Data Center Infrastructure Efficiency) - the ratio of IT equipment power to total facility power; $DCiE = \frac{IT Equipment Power}{Total Facility Power}$;
* **tPUE** (Total PUE) - the ratio of total facility power to IT equipment power, cooling, and lighting; $tPUE = \frac{Total Facility Power}{IT Equipment Power + Cooling + Lighting}$;
* Lower PUE values are better;
* **Chiller** - a device that removes heat from a liquid via a vapor-compression or absorption refrigeration cycle; e.g., air conditioning units;
  * **Chiller-less DCs** - DCs that use outside air for cooling.

---

## Optimizing Servers

* In small installations, aim is **consolidation** (work/CPU);
  * **Fast CPUs are used**;
* In large cloud DCs, goal is to **optimize for energy efficiency**;
  * A fast, expensive and hot CPU is not the best choice;
  * **CEMS (Cooperative Expendable Micro-Slice Servers)** - servers with low-power CPUs and no local storage;
    * Optimizes work per joule and work per dollar;
    * Servers are fully partitionable;
    * Software waste significant in OS load.

---

## Load Handling

* Load varies during the day;
  * **Replicate data** to other DCs during **low load**;
  * Perform **jobs without deadlines** during low load;
  * Differentiate prices based on urgency;
  * Suspend machines during low load;
* AWS EC2 has **spot instances** - prices vary based on demand;
  * When spot price becomes higher than that paid by the user, or the AWS capacity is no longer available, the instance is **interruption**;
  * Users can specify instance **interruption behavior**:
    * **Terminate** - CPU and storage are lost;
    * **Stop** - only CPU is freed, instance can be restarted;
    * **Hibernate** - CPU is freed, instance can be restarted without need of full boot;