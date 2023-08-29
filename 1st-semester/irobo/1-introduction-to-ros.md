# Introduction to [ROS (Robot Operating System)](https://www.ros.org/)

<p align="center">
    <img src="./imgs/ros.png" width="200px" alt="ROS"/>
</p>

## What is ROS?

* Framework for **robot software development**, providing operating system-like functionality on a heterogeneous computer cluster;
* Works well in Linux Ubuntu, but there are bindings to Java, C#, and can be tunneled via web sockets;
* Large user community.

---

## Basic Concepts

### Node

* Modularization in ROS is achieved by separated OS processes called **nodes**;
* **Node is a process that uses ROS framework**;
* Nodes may reside in different machines transparently;
* Nodes get to know each other via [`roscore`](http://wiki.ros.org/roscore), that acts as a **name server**;
* `ROS_MASTER_URI` environment variable defines the `roscore` location.

<p align="center">
    <img src="./imgs/roscore.png" width="300px" alt="roscore"/>
</p>

### Topic

* The **communication between nodes** is done via **topics**;
* Publish/subscribe pattern;
* **Publishers** send messages to a **topic**;
* Published messages are **broadcasted** to all **subscribers**.

### Service

* **Service** is a **request/response** pattern;
* A node can **request a service** from another node, which will **respond** to the request;
* Similar to **RPC** (Remote Procedure Call).

### Message

* **Message** is a **data structure** used for communication between nodes;
* All messages are defined in text files with the extension `.msg`;
* Messages have a **fixed structure**:
  * **Header** - metadata about the message (e.g. timestamp);
  * **Data** - the actual message data (e.g. sensor readings). 

---

## Development Environment

* Two major languages are used in ROS: **Python** and **C++**;
* ROS provides a portable **build system** that can be used to compile code in both languages - [**Catkin**](http://wiki.ros.org/catkin);
* **Package** is a encapsulation of sources, data files and building files;
* A large variety of packages are available on the web, and can be installed via `apt-get` or `rosdep`. Examples: sensor drivers, simulators, SLAM, image processing, etc.

---

## Command Line Tools

### `rosnode`

Prints information about ROS **nodes**.

```bash
rosnode ping <node_name>    # Checks if a node is running
rosnode list                # Lists active nodes
rosnode info <node_name>    # Prints information about a node
rosnode kill <node_name>    # Kills a node
rosnode cleanup             # Kills all nodes
rosnode machine <node_name> # Prints the machine where a node is running
```

### `rostopic`

Prints information about ROS **topics**.

```bash
rostopic list -v            # Lists active topics
rostopic info <topic_name>  # Prints information about a topic
rostopic echo <topic_name>  # Prints messages from a topic
rostopic pub <topic_name> <msg_type> <msg_data>  # Publishes a message to a topic
rostopic hz <topic_name>    # Prints the publishing rate of a topic
rostopic bw <topic_name>    # Prints the bandwidth used by a topic
rostopic find <msg_type>    # Finds topics that use a message type
rostopic type <topic_name>  # Prints the type of a topic
```

### `rosservice`

Prints information about ROS **services**.

```bash
rosservice list                         # Lists active services
rosservice info <service_name>          # Prints information about a service
rosservice call <service_name> <args>   # Calls a service
rosservice type <service_name>          # Prints the type of a service
rosservice find <service_type>          # Finds services that use a service type
rosservice uri <service_name>           # Prints the URI of a service
rosservice args <service_name>          # Prints the arguments of a service
```

### `rosbag`

Manipulates ROS **bag** files (recorded messages).

```bash
rosbag record <topic_name>             # Records messages from a topic
rosbag play <bag_file>                 # Plays a bag file
rosbag info <bag_file>                 # Prints information about a bag file
# ...
```

---

## Useful ROS Facilities

### Parameters

* **Parameters** are used to **configure nodes**;
* Defined in a `.yaml` file;
* `rosparam` command line tool is used to **set** and **get** parameters.

### Launch Files

* **Launch files** are used to **start multiple nodes** at once;
* XML format;
* `roslaunch` command line tool is used to **start** and **stop** launch files.

### TF (Transforms)

* **TF** is a **framework** that keeps track of **multiple coordinate frames** over time;
* Represents geometric transformations in 3D, position and orientation.

### RViz

* **RViz** is a **3D visualization tool** for ROS;
* Displays **sensor data** and **state information** from ROS nodes;
* Displays **TF** frames.

### Gazebo

* **Gazebo** is a **3D simulator** for ROS;
* Simulates **sensors** and **actuators**;
* Simulates **physics**.
* Can be used to **simulate robots**.

### Actionlib

* **Actionlib** is a **framework** for **asynchronous communication**;
* State-full scheme to manage action execution.

### SMACH

* **SMACH** is a **framework** for **state machines**;
* State machines are used to **model complex behavior**;
* States, transitions, outcomes.

Other packages:

* GMapping - SLAM (Simultaneous Localization and Mapping);
* AMCL - Adaptive Monte Carlo Localization;
* MoveIt! - Motion Planning;
* MoveBase - Navigation;
* OctoMap - 3D Mapping;
* ROSPlan - Planning.