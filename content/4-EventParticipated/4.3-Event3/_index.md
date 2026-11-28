---
title : "Event 3"
date : 2026-11-28
weight : 3
chapter : false
pre : " <b> 4.3. </b> "
---

# Summary Report: “Cloud Mastery”

#### Event information

| Item | Details |
|---|---|
| Event name | Cloud Mastery |
| Organizer | AWS Study Group Vietnam community (AWS First Cloud Journey AI) |
| Speakers | Khuong Huynh, Bao Nguyen, Phuong Huyen, Khang Duong |
| Date | Saturday morning, November 14, 2026 |
| Location | AWS Vietnam office, Bitexco Financial Tower, Ho Chi Minh City |
| Role | Participant |

Cloud Mastery was a community session by AWS Study Group Vietnam on taking Agentic AI systems from prototype to production. Four speakers presented the core technical criteria, real-world case studies and deployment architecture on AWS.

#### Key takeaways

- **From prototype to production.** When bringing an Agentic AI system to AWS, three criteria must be met: security (Secure), reliability (Reliable) and scalability (Scalable).
- **Cox Automotive case study.** This was an Agentic AI use case at large enterprise scale, with more than 29,000 employees, 2.3 billion online interactions a year and 57 opportunities for AI. The focus was on building automation that users can trust.
- **Duolingo case study.** Their problem was that teams kept rebuilding the same infrastructure ("Every team rebuilds the same setup"). The solution was to standardize it into a shared platform, called the Shared Agent Platform.
- **Scaling enterprise agents.** When running hundreds of agents, thousands of tools, hundreds of MCP servers and more than 100,000 tool policies, Agentic AI infrastructure must be governed systematically.
- **Security and identity with Amazon Bedrock AgentCore Identity.** The framework has a standard 5-step architecture and two layers of authentication. Inbound Auth covers users accessing an agent through an application, using JWT or IAM through an Authorizer. Outbound Auth covers the agent accessing external resources. The system records the caller's identity and issues a Workload Access Token (WAT).
- **Deployment architecture on AWS.** The sample model runs a Strands Agent on Amazon ECS, with the container image pulled from Amazon ECR, placed in the private subnets of a VPC, together with AWS Secrets Manager and other supporting services.
- **Q&A and Quiz.** The Q&A gave me more practical advice from the speakers on learning and Cloud and AI career direction. The closing Quiz helped reinforce what I had heard.

#### Photos from the event

![Photo from the event 1](/aws-workshop/images/4-EventParticipated/4.3-Event3/photo-1.jpg)

![Photo from the event 2](/aws-workshop/images/4-EventParticipated/4.3-Event3/photo-2.jpg)

#### Lessons learned and personal contribution

1. **Enterprise AI does not stop at an experimental model.** I understand better that bringing AI into real use needs holistic architectural thinking, including shared infrastructure, identity security, governance of resources and policies at scale, and a secure network design on AWS.
2. **Share infrastructure instead of every team rebuilding it.** Duolingo's story showed me that standardizing on a common platform saves effort and keeps teams consistent. I will consider this approach when organizing projects with many components.
3. **Distinguish clearly who calls the agent and what the agent calls.** The two-way Inbound and Outbound authentication model made me look again at how permissions work in Trust Check, where API keys are stored in Parameter Store and each Lambda has its own role with minimum permissions. I will keep applying this principle on larger systems.
4. **Build network design and secrets management into the architecture from the start.** Running agents in private subnets with Secrets Manager shows that security is part of the architecture, not a step added at the end.
5. **Shape my own skill path.** The live Q&A gave me a clearer direction for developing Cloud and AI skills in the coming period.

As for my personal contribution, I attended the full program, took careful notes and organized the knowledge throughout. I also followed and engaged in the Q&A and joined the closing Quiz enthusiastically, contributing to the shared learning and networking atmosphere of the AWS student community in Ho Chi Minh City.