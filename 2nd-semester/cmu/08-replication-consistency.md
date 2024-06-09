# Replication and Consistency in Mobile Environments

## Definitions

- **Connectivity**: the ability to establish a connection between two devices;
  - **Intermittent connectivity**: the connection is not always available;
  - **Disconnection**: the connection is lost;
  - **Weak connectivity**: the connection is slow or unstable;
- **Item**: a piece of data that can be replicated - named by some form of global unique identifier;
- **Collection**: a set of items;
- **Replica**: a copy of an item;
  - **Full replica**: a replica that contains all items in the collection;
  - **Partial replica**: a replica that contains only some items in the collection;
- **CRUD operations**: create, read, update, delete.

| Requirement             | **Remote Access** | **Device-Master** | **Peer-to-Peer** | **Pub-Sub** |
| ----------------------- | ----------------- | ----------------- | ---------------- | ----------- |
| Continuous Connectivity | Yes               | No                | No               | No          |
| Update anywhere         | No                | Yes               | Yes              | No          |
| Consistency             | No                | Yes               | Yes              | Yes         |
| Topology Independence   | No                | No                | Yes              | No          |
| Conflict Handling       | No                | Yes               | Yes              | No          |
| Partial Replication     | No                | Yes               | Yes              | Yes         |

---

## Data Consistency

- **Strong consistency**: identical behavior as a non-replicated system;
  - A read operation returns the most recent write;
  - Requires **substantial coordination** between replicas, which is not compatible with intermittent connectivity;
- **Weak consistency**: allows replicas to diverge temporarily;
  - **Eventual consistency**: replicas will converge if no new updates are made;
  - **Causal consistency**: preserves causality between updates;
- **Session consistency**: provides a **user** with a view of the data that is consistent with the **user's updates**;
  - **Read your writes**: a user sees updates made by the user;
  - **Monotonic reads**: a user sees updates in the order they were made;
  - **Writes follow reads**: updates are made after reads on which they depend;
  - **Monotonic writes**: updates are made in the order they were requested;
