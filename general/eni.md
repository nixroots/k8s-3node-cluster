# AWS Elastic Network Interface
Networking plays an important role in connecting the components of infrastructure. AWS networking feature works with various types of workloads and provides security, availability, and manageability. Now as most of the IT companies are working on cloud environments for cost reduction, high availability, data security, we are getting some interesting networking features as services. We can manage these quite easily too. Among those services is Elastic Network Interface (ENI) which we get by default when we create an EC2 instance on AWS and can be seen while the instance is being created. It may surprise many of us that the security group is attached to this elastic network interface.
![image](https://user-images.githubusercontent.com/40743779/194473780-8c0b9086-c85c-4a54-a174-c0d6ddec5c39.png)

## What is a elastic network interface ?
An elastic network interface is a logical networking component that connects computers and servers over the internet without the conventional physical cabling and other hardware, it is a component in a VPC that represents a virtual network card. We can use ENIs to deploy high availability clusters and perform low-cost failover and fine-grained network management.

ENIs are further classified into two types:

```Primary ENIs```: Gets created with the instance. The lifecycle of a primary ENI is the same as of the instance to which this ENI is attached. We can not detach the primary ENI from its instance.
```Secondary ENIs```: Can be created separately. We can attach or detach the secondary ENI from an instance.

![image](https://user-images.githubusercontent.com/40743779/194473841-0fa11816-cc1d-4eac-a463-ef3293dfd3c0.png)


c Network Interface
Networking plays an important role in connecting the components of infrastructure. AWS networking feature works with various types of workloads and provides security, availability, and manageability. Now as most of the IT companies are working on cloud environments for cost reduction, high availability, data security, we are getting some interesting networking features as services. We can manage these quite easily too. Among those services is Elastic Network Interface (ENI) which we get by default when we create an EC2 instance on AWS and can be seen while the instance is being created. It may surprise many of us that the security group is attached to this elastic network interface.


What is a elastic network interface ?
An elastic network interface is a logical networking component that connects computers and servers over the internet without the conventional physical cabling and other hardware, it is a component in a VPC that represents a virtual network card. We can use ENIs to deploy high availability clusters and perform low-cost failover and fine-grained network management.

ENIs are further classified into two types:

Primary ENIs: Gets created with the instance. The lifecycle of a primary ENI is the same as of the instance to which this ENI is attached. We can not detach the primary ENI from its instance.
Secondary ENIs: Can be created separately. We can attach or detach the secondary ENI from an instance.

## ENI CONSISTS OF THE FOLLOWING
  A primary private IP address.
  One or more secondary private IP addresses.
  One elastic IP address per private IP addresses.
  One public IP address, which can be auto-assigned only to the primary elastic network interface eth0.
  One or more security groups.
  A MAC address.

## Features of ENIs

In addition to the primary ENI that gets created with the instance, we can also attach multiple secondary ENIs to the instance. But the secondary ENI must reside within the same zone and VPC, though it can belong to different security groups.
When we detach a secondary ENI from an instance and attach it to another instance, the attributes of the ENI remain unchanged and the network traffic is redirected to the new instance.
ENIs can be migrated among instances. When we attach a secondary ENI to an instance, services on the instance are not affected, and we do not need to restart the instance.

## Use Scenarios

Low-cost failover: We can detach ENI from a failed instance and attach it to a backup instance, traffic will be redirected to the backup instance. This allows quick recovery of services.
Network management: We can configure multiple ENIs for an instance. For example, one ENI can be used for internal management and another ENI for internet business access to isolate management data from business data. Specific Security group rules can be configured for each ENI based on the source IP addresses, protocols, and ports. For example, Primary ENI eth0 handles the backend with more restrictive control and Secondary ENI eth1 handles the public-facing traffic.
Configuration of multiple private IP addresses for a single instance: If multiple applications are managed on an instance, we can assign multiple secondary private IP addresses to an ENI so that each application will have an independent IP address.

READ: https://blog.opstree.com/2022/03/01/aws-elastic-network-interface/
