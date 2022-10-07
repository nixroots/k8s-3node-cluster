# AWS Elastic Network Interface
Networking plays an important role in connecting the components of infrastructure. AWS networking feature works with various types of workloads and provides security, availability, and manageability. Now as most of the IT companies are working on cloud environments for cost reduction, high availability, data security, we are getting some interesting networking features as services. We can manage these quite easily too. Among those services is Elastic Network Interface (ENI) which we get by default when we create an EC2 instance on AWS and can be seen while the instance is being created. It may surprise many of us that the security group is attached to this elastic network interface.
![image](https://user-images.githubusercontent.com/40743779/194473780-8c0b9086-c85c-4a54-a174-c0d6ddec5c39.png)

## What is a elastic network interface ?
An elastic network interface is a logical networking component that connects computers and servers over the internet without the conventional physical cabling and other hardware, it is a component in a VPC that represents a virtual network card. We can use ENIs to deploy high availability clusters and perform low-cost failover and fine-grained network management.

ENIs are further classified into two types:

```Primary ENIs```: Gets created with the instance. The lifecycle of a primary ENI is the same as of the instance to which this ENI is attached. We can not detach the primary ENI from its instance.
```Secondary ENIs```: Can be created separately. We can attach or detach the secondary ENI from an instance.

![image](https://user-images.githubusercontent.com/40743779/194473841-0fa11816-cc1d-4eac-a463-ef3293dfd3c0.png)


