# Infrastructure as a Service (IaaS)

> **Infrastructure as a Service (IaaS)** - rent virtual machines - **System-level Virtualization**;

- User controls the **OS**, storage, network, applications;
- User does not control the underlying infrastructure;
- e.g. **Amazon EC2 - Elastic Compute Cloud**.

There are several IaaS platforms available, such as **Amazon Web Services**, **Microsoft Azure**, **Google Cloud Platform**, and **OpenStack** (open-source).

| **Service**       | **AWS**        | **Azure**        | **GCP**               | **OpenStack** |
| ----------------- | -------------- | ---------------- | --------------------- | ------------- |
| **Compute**       | EC2            | Virtual Machines | Compute Engine        | Nova          |
| **Storage**       | S3             | Blob Storage     | Cloud Storage         | Swift         |
| **Database**      | RDS            | SQL Database     | Cloud SQL             | Trove         |
| **Networking**    | VPC            | Virtual Network  | Virtual Private Cloud | Neutron       |
| **Monitoring**    | CloudWatch     | Monitor          | Stackdriver           | Ceilometer    |
| **Orchestration** | CloudFormation | Resource Manager | Deployment Manager    | Heat          |

---

## Service Level Agreement (SLA)

- **Contract** between **service provider** and **customer**;
- Can be legally binding or informal;
- Specifies the services that the customer receives, rather than how the service is provided;
- Provide framework for understanding;
- Clear definition of classes and costs;
- Simplify complex issues;
- Reduce areas of conflict,
- Eliminate unrealistic expectations.

---

## Scaling Architectures

- The system have three main requirements:
  - Scale to a **very large size**;
  - Scale **quickly**;
  - Off-peak operation must be cheap (this means that the system must be able to **scale down** - **elasticity**);
- **Hyp. 1**: **Storage Service** - good for static content;
- **Hyp. 2**: **DNS Load Balancer**;
  - Rent several machines;
  - Add machines to the DNS record;
  - In general, the issue is the large number of clients, and not large number of requests from the same client;
- **Hyp. 3**: **HTTP Redirection**;
  - A server redirects the client request to a set of backend servers;
- **Hyp. 4**: **L4 or L7 Rerouting**;
  - Frontend server analyzes the request **source (L4)** or **content (L7)** and reroutes the request to the appropriate backend server;
  - Requires **high-performance server** or specialized hardware (switches);
  - Client **does not know** about the rerouting;
- **Hyp. 5**: hybrids of the previous architectures.

<p align="center">
  <img src="./imgs/scaling-archs.png" alt="Scaling Architectures" width="600">
</p>
