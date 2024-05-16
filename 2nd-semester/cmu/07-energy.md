# Energy in Mobile and Ubiquitous Computing

The main problem is to match:

* Batteries - progress in battery technology is slow;
* Devices - more powerful devices consume more energy;
* Applications;
* Users.

## What is Consuming Energy?

* **CPU** - highly variable as a power drain;
* **Storage** - power costs of accessing persistent storage are small;
* **Network** - networking can be a significant source of power consumption, specially because it also involves the CPU; 
* **Screen** - the screen is a major power drain;

## How to Optimize Energy Consumption?

* **Energy-aware OSs**: main idea is to reduce energy consumption by unifying resource and energy management, and by using collaboration between the OS and applications;
  * Disable unused hardware components;
  * Provide fairness between applications using credit systems;
  * Use power forecasting to do scheduling.

### Cinder - Energy-Aware OS

* **Cinder** is an energy-aware OS build on top of the HiStart exokernel that exploits device-level accounting and power modelling;
* Cinder allocates energy to applications using two abstractions:
  * **Reserves** (batteries) - a pool of energy that can be used by applications;
  * **Taps** - special-purpose threads whose only role is to transfer energy between reserves;
* When an application consumes a resource, the Cinder kernel **reduces the values of the corresponding reserve** and its **scheduler only allows threads to run if they have enough reserves**.

### PowerScope - Power Modeling

* Map energy to code procedures;
* **PowerScope** is a tool that identifies applications behaving as energy sinks;
* Uses statistical sampling to collect traces.