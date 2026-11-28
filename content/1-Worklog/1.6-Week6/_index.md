
---
title: "Week 6 Worklog"
date: 2026-10-19
weight: 6
chapter: false
pre: " <b> 1.6. </b> "
---

### Week 6 Objectives:

- Understand the role and functionality of Elastic Load Balancing (ELB).
- Explore different types of Load Balancers, especially Application Load Balancer (ALB).
- Understand how Target Groups and Health Checks work.
- Learn the mechanisms for automatically scaling EC2 resources through EC2 Auto Scaling.
- Explore Launch Templates and Scaling Policies.
- Practice deploying an EC2 environment integrated with Load Balancing and Auto Scaling.

### Tasks to be completed this week:

| Day | Task | Start Date | Completion Date | References |
| --- | --- | --- | --- | --- |
| Monday | - Learn the fundamentals of Elastic Load Balancing (ELB).<br>- Explore the role of Load Balancers in distributing incoming traffic across multiple servers.<br>- Study the types of Load Balancers available on AWS and their characteristics.<br>- Distinguish between Application Load Balancer (ALB), Network Load Balancer (NLB), and Gateway Load Balancer (GWLB). | 19/10/2026 | 19/10/2026 | [AWS Auto Scaling & Load Balancing Workshop](https://000006.awsfcaj.com/) |
| Tuesday | - Understand the architecture and operation of Application Load Balancer (ALB).<br>- Explore Listeners, Listener Rules, and Target Groups.<br>- Learn how ALB distributes HTTP/HTTPS traffic to EC2 Instances.<br>- Practice configuring Target Groups and registering EC2 Instances as Targets. | 20/10/2026 | 20/10/2026 | [AWS Auto Scaling & Load Balancing Workshop](https://000006.awsfcaj.com/) |
| Wednesday | - Learn about Target Group Health Checks.<br>- Explore the health states of registered Targets.<br>- Understand how Load Balancers detect and handle unhealthy servers.<br>- Practice checking the Health Check status of EC2 Instances. | 21/10/2026 | 21/10/2026 | [AWS Auto Scaling & Load Balancing Workshop](https://000006.awsfcaj.com/) |
| Thursday | - Learn the fundamentals of EC2 Auto Scaling.<br>- Explore components such as Auto Scaling Groups, Minimum Capacity, Desired Capacity, and Maximum Capacity.<br>- Understand Launch Templates and their role in creating EC2 Instances.<br>- Analyze the relationship between Auto Scaling Groups and Load Balancers. | 22/10/2026 | 22/10/2026 | [AWS Auto Scaling & Load Balancing Workshop](https://000006.awsfcaj.com/) |
| Friday | - Explore different Scaling Policies and the conditions that trigger scaling activities.<br>- Study Target Tracking Scaling and related monitoring metrics.<br>- Practice configuring an Auto Scaling Group integrated with an Application Load Balancer.<br>- Verify the system's ability to scale out, scale in, and distribute incoming traffic. | 23/10/2026 | 23/10/2026 | [AWS Auto Scaling & Load Balancing Workshop](https://000006.awsfcaj.com/) |

### Week 6 Achievements:

During the sixth week, I focused on learning Elastic Load Balancing (ELB) and EC2 Auto Scaling, two important components for building systems that can distribute incoming traffic and automatically adjust resources on AWS.

First, I explored the role of Elastic Load Balancing in distributing incoming traffic across multiple servers or EC2 Instances. I studied the types of Load Balancers provided by AWS, including Application Load Balancer (ALB), Network Load Balancer (NLB), and Gateway Load Balancer (GWLB). Through this process, I understood the differences in their purposes and operational scopes.

Next, I focused on Application Load Balancer (ALB), particularly how it receives and distributes HTTP/HTTPS traffic to target servers. I studied the components Listener, Listener Rules, and Target Groups, and understood how EC2 Instances can be registered in a Target Group so that the Load Balancer can forward requests to appropriate servers.

Another important topic was Health Checks. I explored how Target Groups monitor the health of registered Targets through predefined health check requests. This helped me understand how a Load Balancer identifies healthy and unhealthy servers and avoids forwarding traffic to targets that cannot properly handle requests.

In addition to Load Balancing, I studied EC2 Auto Scaling and its components, including Auto Scaling Groups, Minimum Capacity, Desired Capacity, and Maximum Capacity. I learned how an Auto Scaling Group manages the number of EC2 Instances based on configured capacity settings and resource requirements. I also explored Launch Templates, which store the configuration information required to launch new EC2 Instances.

Finally, I studied Scaling Policies, especially Target Tracking Scaling, to understand how a system automatically adjusts the number of servers based on monitoring metrics. Through the practical learning activities, I explored how to integrate an Auto Scaling Group with an Application Load Balancer to distribute incoming traffic and maintain system availability when demand changes.

By the end of the sixth week, I had gained a clearer understanding of how to build load-balanced and automatically scalable systems on AWS. The knowledge of Load Balancers, Target Groups, Health Checks, and Auto Scaling provides an important foundation for deploying applications that can scale and maintain high availability.
