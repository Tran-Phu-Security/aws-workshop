---
title: "Blog 3"
date: 2026-11-27
weight: 3
chapter: false
pre: " <b> 3.3. </b> "
---

# WHEN AI BECOMES A CYBERSECURITY “INVESTIGATOR”: EXPLORING AMAZON GUARDDUTY INVESTIGATION AGENT
I used to think that when a security system detects suspicious activity and sends an alert, the next step is simply to check whether that alert is dangerous.

In reality, an alert is only the starting point. A Security Analyst may spend hours reviewing logs, correlating events, identifying affected resources, and determining whether an actual attack is taking place.

While reading the AWS Security Blog article about Amazon GuardDuty Investigation Agent, I came across an interesting approach: instead of having people analyze every alert manually, AWS uses an AI Agent to support the investigation process, gather evidence, and assess risk.

This post summarizes how the feature works. I hope it will be useful to anyone learning about Cloud Security and the application of AI in cybersecurity.
<figure style="text-align: center;">
  <img src="/aws-workshop/images/3-BlogsPosted/3.3-Blog3/guardduty-agent-HD.png" alt="Amazon GuardDuty Investigation Agent" style="width: 90%; max-width: 1100px; height: auto;">
  <figcaption>Amazon GuardDuty Investigation Agent</figcaption>
</figure>1. A FEW KEY CONCEPTS
- Amazon GuardDuty: An AWS threat detection service that continuously monitors accounts and workloads to identify suspicious or potentially malicious activity.
- Security Finding: A security alert generated when the system detects an anomaly. A finding does not necessarily mean that an attack has occurred.
- AI Agent: An AI system capable of carrying out a sequence of tasks to achieve a goal. In this case, the Agent is used to collect, correlate, and analyze information for an investigation.
- Threat Investigation: The process of investigating a threat to determine what happened, how severe it is, and which resources may be affected.
- MITRE ATT&CK: A framework that describes tactics and techniques commonly used by attackers. It helps Security Analysts understand which stage of an attack suspicious behavior may belong to.
2. THE CHALLENGE: DETECTING AN ALERT DOES NOT MEAN UNDERSTANDING THE ATTACK
Imagine that GuardDuty detects an IAM credential being used from an unusual location.

This could indicate that the credential has been stolen. However, it could also simply mean that an employee is using a VPN or accessing the system from a new location.

To determine the cause, a Security Analyst needs to answer several questions:
- Where was this credential usually used?
- Have any unusual API calls appeared?
- Has the account performed sensitive actions, such as changing IAM permissions or accessing important data?
- Are there other alerts related to the same account or resource?
The challenge is that GuardDuty can detect suspicious indicators, but understanding the context and assessing the level of risk still require an investigation.

As the number of alerts increases, manual analysis becomes time-consuming, especially for organizations managing many AWS accounts.
3. THE SOLUTION: AMAZON GUARDDUTY INVESTIGATION AGENT
On July 20, 2026, AWS introduced Amazon GuardDuty Investigation Agent, an AI-powered feature designed to support the investigation of security findings in AWS environments.

Its key difference is that it does more than display alerts. It also helps analyze and correlate related evidence to produce a structured assessment report.

In simple terms:
- GuardDuty: Detects that something suspicious may be happening.
- Investigation Agent: Helps determine whether those indicators are related, how serious the situation may be, and how it should be handled.
According to AWS, this feature can reduce investigation time from hours to minutes.

Investigation Agent is integrated directly into GuardDuty. Users can initiate an investigation through the AWS Management Console, AWS CLI, API, or SDK without having to build and operate a separate AI Agent themselves.
4. HOW DOES THE AI AGENT INVESTIGATE AN ALERT?
According to AWS documentation, the process can be understood through four main stages.
4.1. Define the investigation scope
Users can ask the AI Agent to investigate:
- A specific GuardDuty Finding ID.
- An AWS account.
- All accounts in an AWS Organization that they are authorized to access.
When using the AWS CLI or API, users can also provide a natural-language request through a trigger-prompt, with a maximum length of 2,048 characters.

For example:

Investigate the recent unauthorized access finding.
4.2. Collect and correlate evidence
After receiving the request, the Agent analyzes GuardDuty findings, correlates related activity, and considers the security context of the resources within the investigation scope.

Instead of treating each alert independently, it looks for relationships between indicators to build an overall assessment.
4.3. Assess the threat
After the analysis, the Agent returns several important pieces of information:
- Risk Level: The risk level, with the values Info, Low, Medium, High, and Critical.
- Confidence: The confidence level of the assessment, with the values Unknown, Low, Medium, and High.
- Summary: A summary of important findings and observations.
- MITRE ATT&CK Mapping: Links suspicious behavior to known attack techniques.
- Resource Mapping: Identifies the related AWS resources.
4.4. Recommend response actions
The Agent does not stop at assessment. It also provides remediation actions prioritized by importance, which may include specific AWS CLI commands.

This helps Security Analysts understand what they should investigate or address first.
5. AN IMPORTANT DISTINCTION: RISK LEVEL IS NOT CONFIDENCE
This is one of the points I found particularly important when reading the documentation.

At first, I thought that if AI rated an alert as HIGH risk, it meant that it had confidently confirmed an attack.

However, Risk Level and Confidence are two different concepts:
- Risk Level: Assesses how severe the threat is.
- Confidence: Indicates how reliable the conclusion is based on the evidence analyzed.
Examples:
- Risk: Critical | Confidence: High
  → The indicators suggest a very serious threat, and the AI has a high level of confidence in its assessment.
- Risk: High | Confidence: Low
  → The threat could have a significant impact, but the available evidence is not yet strong enough to support a highly confident conclusion.
My takeaway is that even a high-risk alert must be considered alongside the quality of the evidence, rather than judged only by a High or Critical label.
6. A PRACTICAL EXAMPLE: WHAT HAPPENS WHEN A SERVER IS COMPROMISED?
In its article, AWS provides an example investigation result involving an Amazon EKS (Elastic Kubernetes Service) environment.

The sample report shows:
- A worker node with signs of compromise during operation.
- A reverse shell running with root privileges.
- Activity involving access to the Docker socket and execution of a malicious file.
- Approximately 500 runtime signals associated with multiple attack tactics.
The Agent's assessment was:
- Risk Level: Critical
- Confidence: High
This example illustrates the value of analyzing multiple indicators together.

If an administrator looks at only one alert, they may not fully understand the severity of the situation. By combining related indicators, however, the Agent can provide a more complete picture of the intrusion.

Note: This is a sample result presented by AWS in its documentation. It is not an attack that I personally performed or verified.
7. THREE STEPS TO TRY INVESTIGATION AGENT
7.1. Enable Amazon GuardDuty
Open the AWS Management Console and make sure GuardDuty is enabled in a supported Region.

Then go to Investigations → Settings → Enable to turn on the investigation feature.
7.2. Initiate an investigation
Under Investigations, select Initiate Investigation.

Depending on their permissions, users can enter a Finding ID, select an AWS account, or choose an organization-wide scope.
7.3. Review the assessment results
After the investigation is complete, open the report to review:
- An overview of the findings.
- Attack techniques mapped to MITRE ATT&CK.
- Risk level and confidence.
- Affected resources.
- Recommended remediation actions.
According to the AWS article published during the Public Preview period, an account-level investigation typically took around 2–5 minutes, while an investigation of a specific finding took around 10–12 minutes.

These are reference times from the preview period, not fixed guarantees for every investigation.
8. ANOTHER ADVANTAGE: INTEGRATION INTO A SECURITY WORKFLOW
Investigation Agent does not have to be used manually through the AWS Console.

With APIs, organizations can integrate its investigation capabilities into their existing security monitoring workflows.

One illustrative architecture mentioned by AWS is:

GuardDuty → EventBridge → AWS Lambda → Investigation Agent → SIEM

The flow works as follows:
1. GuardDuty generates a security finding.
2. EventBridge routes the event into the workflow.
3. Lambda calls the API to initiate an investigation.
4. Investigation Agent analyzes the information and returns its results.
5. The results are forwarded to a SIEM (Security Information and Event Management) system for Security Analysts to monitor and handle.
For example, findings assessed as Critical could be routed to a high-priority incident queue.

The important point is that the AI Agent does not replace the entire security monitoring system. Instead, it adds analysis capabilities and helps prioritize alerts for further action.
9. CONSIDERATIONS WHEN USING THE FEATURE
9.1. AI does not guarantee a perfectly accurate conclusion
Even when the Agent provides an assessment with High Confidence, the results should still be reviewed by a Security Analyst, especially before sensitive decisions such as locking an account or isolating a resource.
9.2. Apply appropriate IAM permissions
To use the feature, AWS requires permissions such as:
- guardduty:CreateInvestigation
- guardduty:GetInvestigation
- guardduty:ListInvestigations
Not every account is allowed to initiate investigations across the entire organization. AWS has a separate permission model for administrator accounts and member accounts.
9.3. Pay attention to Region scope and data processing
Investigation Agent uses a Cross-Region Inference mechanism to process assessments within the geographic scope specified by AWS.

Data is stored in the Region where the request is initiated, but investigation data and summary results may be processed in another Region within the same geography.

Therefore, organizations with strict data-processing location requirements should review this aspect before deployment.
9.4. Check availability and cost
At the time the blog was published (July 20, 2026), the feature was in Public Preview in 10 AWS Regions. It was available at no charge, with a limit of 10 investigations per account per day and a maximum of 100 investigations per account throughout the preview period.

These limits may change, so check the latest AWS documentation before trying the feature.
10. WHAT I LEARNED
- Threat detection is only the first step. An alert becomes more useful when we understand its cause, related events, and potential impact.
- AI can help reduce repetitive investigation work. Instead of spending most of their time correlating alerts and compiling information, Security Analysts can focus on verifying results and deciding how to respond.
- Do not confuse severity with confidence. High risk does not mean that a conclusion is certainly correct. Both factors matter when assessing an incident.
- Automation does not eliminate the need for people. An AI Agent is a tool that supports the investigation process, but important security decisions still require appropriate human oversight and assessment.
As someone learning about Cloud Security, I think Amazon GuardDuty Investigation Agent demonstrates a practical direction for applying AI in cybersecurity: not only detecting unusual activity, but also helping people understand what is actually happening and what they should prioritize.
11. REFERENCES
- AWS Security Blog – Introducing the Amazon GuardDuty investigation agent: on-demand AI-powered threat assessment
- Amazon GuardDuty User Guide