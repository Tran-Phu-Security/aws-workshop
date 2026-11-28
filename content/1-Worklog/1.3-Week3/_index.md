
---
title: "Week 3 Worklog"
date: 2026-09-28
weight: 3
chapter: false
pre: " <b> 1.3. </b> "
---

### Week 3 Objectives:

- Understand the architecture and fundamental functions of Amazon Virtual Private Cloud (VPC).
- Learn about important networking components such as CIDR, Subnets, Route Tables, and Internet Gateways.
- Distinguish between Public Subnets and Private Subnets.
- Understand network traffic control mechanisms through Security Groups and Network ACLs (NACLs).
- Practice building and configuring a basic VPC network on AWS.

### Tasks to be completed this week:

| Day | Task | Start Date | Completion Date | References |
| --- | --- | --- | --- | --- |
| Monday | - Learn the fundamentals of Amazon Virtual Private Cloud (VPC).<br>- Explore the role of VPC in building isolated network environments on AWS.<br>- Understand CIDR notation and how to define an IP address range for a VPC. | 28/09/2026 | 28/09/2026 | [Amazon VPC Workshop](https://000003.awsfcaj.com/) |
| Tuesday | - Learn about Subnets and how to divide a VPC network into smaller networks.<br>- Distinguish between Public Subnets and Private Subnets.<br>- Explore how to select an Availability Zone when creating a Subnet. | 29/09/2026 | 29/09/2026 | [Amazon VPC Workshop](https://000003.awsfcaj.com/) |
| Wednesday | - Understand the function of Route Tables in directing network traffic.<br>- Explore Internet Gateways (IGWs) and how they connect a VPC to the Internet.<br>- Understand the relationship between Subnets, Route Tables, and Internet Gateways. | 30/09/2026 | 30/09/2026 | [Amazon VPC Workshop](https://000003.awsfcaj.com/) |
| Thursday | - Learn about the network access control mechanisms provided by Security Groups and Network ACLs (NACLs).<br>- Distinguish between Security Groups and NACLs in terms of scope, connection tracking, and traffic rule processing.<br>- Understand how to configure Inbound Rules and Outbound Rules. | 01/10/2026 | 01/10/2026 | [Amazon VPC Workshop](https://000003.awsfcaj.com/) |
| Friday | - Practice creating a VPC on AWS.<br>- Create Public Subnets and Private Subnets in appropriate Availability Zones.<br>- Configure Route Tables and an Internet Gateway.<br>- Set up Security Groups and Network ACLs.<br>- Verify the network configuration and connectivity between components within the VPC. | 02/10/2026 | 02/10/2026 | [Amazon VPC Workshop](https://000003.awsfcaj.com/) |

### Week 3 Achievements:

During the third week, I focused on learning Amazon Virtual Private Cloud (VPC), a service that allows users to build an isolated virtual network environment on AWS. Through this learning process, I understood the role of VPC in organizing resources, dividing networks, and controlling connectivity between components within a cloud environment.

First, I studied how to define an IP address range for a VPC using Classless Inter-Domain Routing (CIDR) notation. I learned the meaning of network addresses and subnet masks, as well as how to select appropriate IP address ranges to avoid address conflicts when designing a network.

Next, I explored the concept of Subnets and how to divide a VPC into multiple smaller networks. In particular, I learned to distinguish between Public Subnets and Private Subnets based on their routing configurations and Internet accessibility. I also studied the role of Availability Zones in distributing resources and improving system availability.

Another important topic was network traffic routing through Route Tables and Internet Gateways (IGWs). I understood that Route Tables determine the paths taken by network traffic, while Internet Gateways enable resources within a VPC to communicate with the Internet when configured appropriately. This helped me better understand the relationship between VPCs, Subnets, Route Tables, and Internet Gateways when designing a network architecture.

In addition, I studied two important network traffic control mechanisms: Security Groups and Network ACLs (NACLs). I learned that Security Groups operate at the resource level and are stateful, whereas Network ACLs operate at the Subnet level and are stateless. These concepts helped me better understand how to configure Inbound and Outbound rules to control network traffic.

Finally, I practiced building a basic VPC environment on AWS, including creating a VPC, dividing it into Subnets, and configuring Route Tables, an Internet Gateway, Security Groups, and Network ACLs. Through these practical exercises, I was able to connect theoretical knowledge with actual implementation and better understand how different networking components work together.

By the end of the third week, I had gained foundational knowledge of network design and management on AWS. This knowledge provides an important basis for building systems with well-organized, secure, and scalable network architectures in the following weeks.
