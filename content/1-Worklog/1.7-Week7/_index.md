
---
title: "Week 7 Worklog"
date: 2026-10-26
weight: 7
chapter: false
pre: " <b> 1.7. </b> "
---

### Week 7 Objectives:

- Understand the concept and role of Infrastructure as Code (IaC).
- Explore the functionality of AWS CloudFormation in automating infrastructure deployment.
- Understand the structure and main components of CloudFormation Templates.
- Become familiar with YAML and JSON syntax for describing AWS resources.
- Learn the process of creating, updating, and managing CloudFormation Stacks.
- Practice deploying AWS resources using CloudFormation.

### Tasks to be completed this week:

| Day | Task | Start Date | Completion Date | References |
| --- | --- | --- | --- | --- |
| Monday | - Learn the fundamentals of Infrastructure as Code (IaC).<br>- Explore the benefits of managing infrastructure through code instead of manual configuration.<br>- Understand the role of AWS CloudFormation in automating AWS resource deployment.<br>- Compare manual resource deployment with the IaC approach. | 26/10/2026 | 26/10/2026 | [AWS CloudFormation Workshop](https://000037.awsfcaj.com/) |
| Tuesday | - Explore the basic structure of a CloudFormation Template.<br>- Study the Parameters, Resources, Outputs, and Mappings sections.<br>- Learn YAML and JSON syntax used in Templates.<br>- Analyze how AWS resources are declared in the Resources section. | 27/10/2026 | 27/10/2026 | [AWS CloudFormation Workshop](https://000037.awsfcaj.com/) |
| Wednesday | - Learn how to declare AWS resources in a CloudFormation Template.<br>- Explore how resources reference each other using Ref and Fn::GetAtt.<br>- Understand how to use Parameters to provide input values.<br>- Study Outputs for displaying information after Stack deployment. | 28/10/2026 | 28/10/2026 | [AWS CloudFormation Workshop](https://000037.awsfcaj.com/) |
| Thursday | - Learn the process of creating and managing CloudFormation Stacks.<br>- Explore different Stack statuses.<br>- Understand how to update infrastructure using a new Template.<br>- Study Change Sets to preview modifications before applying them. | 29/10/2026 | 29/10/2026 | [AWS CloudFormation Workshop](https://000037.awsfcaj.com/) |
| Friday | - Practice building a CloudFormation Template using YAML.<br>- Deploy selected AWS resources through CloudFormation.<br>- Practice creating Stacks, checking deployment statuses, and updating configurations.<br>- Verify created resources and explore how to troubleshoot deployment errors.<br>- Learn how to delete Stacks and manage resources after completing practical exercises. | 30/10/2026 | 30/10/2026 | [AWS CloudFormation Workshop](https://000037.awsfcaj.com/) |

### Week 7 Achievements:

During the seventh week, I focused on learning Infrastructure as Code (IaC) and AWS CloudFormation, a service that helps automate the provisioning and management of AWS resources through Templates.

First, I explored the concept of Infrastructure as Code and its benefits in infrastructure management. I understood that instead of manually configuring individual resources through a management interface, IaC allows infrastructure to be described through code. This approach helps make deployment more consistent, easier to control, and more convenient when configurations need to be reused.

Next, I studied the structure of AWS CloudFormation Templates and their important sections, including Parameters, Resources, Outputs, and Mappings. I learned that Resources define the AWS resources to be deployed, Parameters allow input values to be provided, and Outputs display important information after a Stack has been successfully created. I also became familiar with YAML and JSON syntax when building Templates.

Another important topic was how resources within a Template reference one another. I explored intrinsic functions such as Ref and Fn::GetAtt to retrieve values or attributes from declared resources. This helped me better understand how CloudFormation identifies relationships and coordinates the deployment of different components within the same system.

In addition to building Templates, I studied the process of creating and managing CloudFormation Stacks. I explored different Stack statuses, how to update configurations using new Templates, and the role of Change Sets in previewing modifications before applying updates. This helped me recognize the importance of reviewing the impact of changes on existing infrastructure.

Finally, I practiced building CloudFormation Templates using YAML and explored the process of deploying AWS resources through CloudFormation. I learned how to create Stacks, check deployment statuses, monitor provisioned resources, and investigate potential deployment errors. I also studied how to update and delete Stacks after completing practical exercises.

By the end of the seventh week, I had gained a clearer understanding of infrastructure management through code and how AWS CloudFormation automates resource deployment. This knowledge is important for improving consistency, reusability, and infrastructure management efficiency in AWS environments.
