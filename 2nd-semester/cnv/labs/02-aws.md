# Amazon Web Services

> **Note**: this file only contains notes about the labs. For the theory, check the [here](../02-1-amazon-web-services.md).

We can interact with AWS using:

- [AWS Management Console](https://eu-west-3.console.aws.amazon.com/console/home) - remember to select the **region**;
- [AWS CLI](https://aws.amazon.com/cli/) - a unified tool to manage your AWS services;
  - Useful to create **scripts** to automate tasks;
- [AWS SDKs](https://aws.amazon.com/tools/) - for programming languages like Python, Java, Ruby, etc.
  - We will use the [Java SDK](https://docs.aws.amazon.com/sdk-for-java/v1/developer-guide/welcome.html) in the labs.

## EC2

### Launching an Instance

1. Choose **name**, **OS**, **AMI** and **architecture**;
2. Select the **instance type** (e.g., t2.micro);
3. Create a **key pair** (`.pem` file) - used to connect to the instance;
4. Configure the **security group** (firewall rules) - you can create a new one or use an existing one;
5. In Advanced Details, you can enable **Detailed Monitoring** (CloudWatch).

Now you can launch the instance and connect to it using SSH:

```bash
ssh -i "key.pem" ec2-user@<public-ip>
```

### Load Balancing and Auto Scaling

#### Create VM Image

1. Using the instance you created, install the necessary software;
2. Create an **AMI** from the instance, selecting a name.

#### Create Load Balancer

1. Create a **Classic Load Balancer**;
2. Select a name and the **availability zones**;
3. Select the **security group**;
4. Configure the **health check**.

#### Create Launch Template (similar to the creation of an instance)

1. Create a **Launch Template**;
2. Select the name and the **AMI** you created;
3. Select the **instance type** and the **key pair**;
4. Configure the **security group**.

#### Create Auto Scaling Group

1. Create an **Auto Scaling Group**;
2. Give it a name and select the **launch template** you created;
3. Select the **availability zones** - ⚠️ **must be the same as the load balancer**;
4. In the Advanced options, **Attach to an existing load balancer**, and choose the one you created;
5. Configure the health checks (**Turn on Elastic Balancing health checks**, grace period of **60s**, and enable **CloudWatch** at the additional settings);
6. Configure the **desired capacity** and the **minimum and maximum size**;
7. Configure the **scaling policies** (you can create **CloudWatch alarms** and attach them to the scaling policies).
