
---
title: "Week 2 Worklog"
date: 2026-09-21
weight: 2
chapter: false
pre: " <b> 1.2. </b> "
---

### Week 2 Objectives:

- Understand the role and functionality of AWS Identity and Access Management (IAM).
- Learn how to manage IAM Users, Groups, Roles, and Policies.
- Understand the Principle of Least Privilege.
- Explore AWS account security mechanisms, including Multi-Factor Authentication (MFA) and Access Keys.
- Practice creating users, groups, and configuring access permissions in AWS IAM.

### Tasks to be completed this week:

| Day | Task | Start Date | Completion Date | References |
| --- | --- | --- | --- | --- |
| Monday | - Learn the fundamentals of AWS Identity and Access Management (IAM).<br>- Explore the role of IAM in managing identities and controlling access to AWS resources.<br>- Understand the differences between IAM Users, Groups, Roles, and Policies. | 21/09/2026 | 21/09/2026 | [AWS IAM Workshop](https://000002.awsfcaj.com/) |
| Tuesday | - Learn how to create and manage IAM Users.<br>- Practice creating IAM Users and configuring their login credentials.<br>- Explore how to organize users into IAM Groups.<br>- Practice adding users to groups and checking the permissions inherited from those groups. | 22/09/2026 | 22/09/2026 | [AWS IAM Workshop](https://000002.awsfcaj.com/) |
| Wednesday | - Learn about IAM Policies and the structure of JSON-based policies.<br>- Analyze the Effect, Action, Resource, and Condition elements.<br>- Practice attaching policies to IAM Users and IAM Groups.<br>- Explore the use of AWS managed policies and customer managed policies. | 23/09/2026 | 23/09/2026 | [AWS IAM Workshop](https://000002.awsfcaj.com/) |
| Thursday | - Learn about IAM Roles and the differences between IAM Roles and IAM Users.<br>- Explore how IAM Roles are used to grant access between AWS services.<br>- Understand the Principle of Least Privilege. | 24/09/2026 | 24/09/2026 | [AWS IAM Workshop](https://000002.awsfcaj.com/) |
| Friday | - Learn about Multi-Factor Authentication (MFA) and its role in protecting AWS accounts.<br>- Explore Access Key management and the risks of using long-term credentials.<br>- Study IAM security recommendations and practice reviewing configured access permissions. | 25/09/2026 | 25/09/2026 | [AWS IAM Workshop](https://000002.awsfcaj.com/)<br>[AWS MFA Documentation](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_mfa.html) |

### Week 2 Achievements:

During the second week, I focused on learning AWS Identity and Access Management (IAM), an essential service for managing identities and controlling access to AWS resources.

First, I explored the functionality of IAM and how it supports the management of users, groups, roles, and access permissions. Through this process, I gained a clearer understanding of the differences between IAM Users, IAM Groups, IAM Roles, and IAM Policies. Each component serves a specific purpose, but they work together to establish an access management mechanism for AWS environments.

Next, I learned how to create and manage IAM Users and practiced organizing users into IAM Groups. Managing permissions through groups helps simplify the access management process and reduces the need to configure separate policies for individual users. I also explored how IAM Policies define the actions that an identity is allowed or denied to perform on AWS resources.

Regarding IAM Policies, I studied their JSON structure and important elements, including Effect, Action, Resource, and Condition. This helped me understand how a policy allows or denies specific actions on designated resources. In addition, I learned the differences between AWS managed policies and customer managed policies.

Another important topic during the week was IAM Roles and the Principle of Least Privilege. I understood that access permissions should be limited to what is actually required, helping prevent security risks caused by excessive permissions. I also explored how IAM Roles allow AWS services to access resources without relying on users' long-term credentials.

Finally, I studied AWS account protection mechanisms through Multi-Factor Authentication (MFA) and Access Key management. I recognized the importance of protecting credentials, avoiding unnecessary use of Access Keys, and regularly reviewing assigned access permissions.

By the end of the second week, I had developed a clearer understanding of identity management and access control mechanisms in AWS. The knowledge of IAM, Policies, Roles, MFA, and the Principle of Least Privilege provides an important foundation for deploying and securing cloud resources in the following weeks.
