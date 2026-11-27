---
title: "Blog 1"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

# AWS CONTINUUM: BRINGING PENETRATION TESTING INTO EVERY CI/CD DEPLOYMENT

## 1. Introduction

In modern software development, applications are updated frequently. New features, configuration changes, and code modifications can introduce security weaknesses at any stage of the development lifecycle.

Traditional penetration testing is often performed periodically, such as before a major release or during a scheduled security assessment. Although valuable, this approach may leave changes made between testing cycles without immediate security validation.

At the same time, automated security tools such as SAST, DAST, and SCA help identify potential weaknesses, but their findings may still require further investigation to determine whether a vulnerability is actually exploitable.

**AWS Continuum for Penetration Testing** introduces a way to integrate penetration testing into CI/CD pipelines. It can automatically test an application after deployment to a pre-production environment and use the results as a security gate before production promotion.

<figure>
  <img src="/aws-workshop/images/3-BlogsPosted/3.1-Blog1/aws-continuum.jpg" alt="AWS Continuum for Penetration Testing" style="display:block;width:90%;max-width:1100px;height:auto;margin:0 auto;" />
  <figcaption style="text-align:center;">AWS Continuum for Penetration Testing integrated into the CI/CD workflow.</figcaption>
</figure>

## 2. Understanding the Key Concepts

Before exploring the integration, it is important to understand several security and DevOps concepts.

### 2.1. SAST, DAST, and SCA

- **SAST (Static Application Security Testing):** Examines application source code or compiled code without executing the application to identify potential security weaknesses.
- **DAST (Dynamic Application Security Testing):** Tests a running application by sending requests and observing its responses.
- **SCA (Software Composition Analysis):** Identifies third-party libraries and dependencies and checks them for known vulnerabilities or licensing issues.

These tools provide useful security signals. However, their findings do not always demonstrate that a weakness can be exploited in the actual application environment.

### 2.2. Penetration Testing

Penetration testing evaluates an application by simulating security attacks within an authorized scope. Its purpose is to determine whether weaknesses can be exploited and to understand their potential impact.

Unlike tools that primarily identify potential weaknesses, penetration testing can validate whether a security issue is practically exploitable.

### 2.3. CI/CD Pipeline

CI/CD stands for Continuous Integration and Continuous Delivery or Deployment. A pipeline automates stages such as building, testing, and deploying application changes.

A security gate can be inserted into this workflow so that a deployment must satisfy defined security requirements before proceeding to the next stage.

### 2.4. OpenID Connect (OIDC)

OIDC allows a CI/CD provider to obtain temporary AWS credentials through identity federation. Instead of storing long-lived AWS access keys in pipeline variables, the workflow receives an OIDC token and exchanges it through AWS Security Token Service (AWS STS) by assuming an IAM role.

## 3. Limitations of Traditional Security Testing

Traditional security testing and automated scanning serve different purposes.

Automated tools help identify areas that may require attention, while penetration testing helps validate whether relevant weaknesses are exploitable.

When penetration testing is performed only at scheduled intervals, changes introduced after an assessment may remain untested until the next cycle. Performing a full penetration test for every deployment, on the other hand, may increase testing time and operational costs.

The challenge is therefore to make security testing continuous without unnecessarily testing the entire application after every small change.

## 4. How AWS Continuum for Penetration Testing Works

AWS Continuum for Penetration Testing addresses this challenge by integrating a scoped penetration test into the deployment pipeline.

The general workflow is:

1. Developers commit application changes.
2. The CI/CD pipeline builds and tests the application.
3. The application is deployed to a staging or pre-production environment.
4. The pipeline authenticates to AWS using OIDC.
5. The integration starts a penetration test against the configured application and scope.
6. AWS Continuum evaluates the test result and returns the outcome to the pipeline.
7. The pipeline either allows or blocks promotion to production according to the configured rules.

The penetration testing job runs on the AWS side. The CI/CD runner coordinates the integration and receives the result rather than executing the penetration testing logic itself.

### 4.1. Security Gate

The security gate determines whether a deployment can proceed.

For example, if the configured severity threshold is `HIGH`, findings classified as `HIGH` or `CRITICAL` block promotion. Findings below the threshold are reported but do not block the pipeline.

This behavior is configurable. Teams can choose a threshold that matches their security requirements.

### 4.2. Understanding the Pipeline Result

A failed pipeline does not necessarily mean that a confirmed vulnerability was found. The outcome must be interpreted using the test result and scope decision.

Important outcomes include:

- **Finding at or above the configured threshold:** The gate fails and blocks promotion.
- **Scoped out:** The change is within the configured scope, but no security-relevant attack-surface change requires testing. The pipeline passes.
- **Scope conflict:** The change falls outside the configured penetration-testing scope. The pipeline follows the configured conflict-handling behavior.
- **Infrastructure error:** The test could not start or complete. Whether this blocks the pipeline depends on the fail-on-infrastructure-error setting.

## 5. Change-Based Scoping

One of the important features of this integration is its ability to scope testing to application changes.

Instead of testing the entire application after every deployment, AWS Continuum determines the commit range between:

- **Head commit:** The commit currently being evaluated.
- **Base commit:** The previous commit that the gate has already cleared.

The integration uses this range to determine which changes need security testing.

Three scope decisions are particularly important:

| Scope decision | Meaning |
|---|---|
| In scope | The change modifies the tested attack surface and is evaluated by the penetration test. |
| Scoped out | The change does not modify the relevant attack surface, so the pipeline can pass without a new test. |
| Scope conflict | The change falls outside the configured test scope and was not tested. |

This approach helps reduce unnecessary testing while maintaining a clear record of which changes have been assessed.

## 6. Integrating AWS Continuum into CI/CD

The integration generally involves three preparation steps.

### 6.1. Configure the Penetration Test

First, configure the target application and its testing scope in AWS Continuum.

The target domain must be verified, and the penetration test must be configured with the appropriate target, credentials, and network scope.

CI/CD mode must then be enabled, and the required Agent Space ID and penetration test ID must be recorded.

The pipeline uses this existing configuration when starting a test. It does not create the target or penetration test automatically.

### 6.2. Configure IAM and OIDC Authentication

Next, configure an IAM OIDC identity provider and an IAM role that the CI/CD workflow can assume.

The role requires two important policies:

- **Trust policy:** Defines which CI/CD identities are allowed to assume the role.
- **Permissions policy:** Defines what the workflow can do after assuming the role.

The trust policy should be restricted to the intended repository, branch, or deployment environment wherever supported.

Using OIDC and temporary credentials avoids the need to store long-lived AWS access keys in CI/CD variables.

### 6.3. Add the Security Gate to the Pipeline

Finally, add the AWS Continuum penetration-testing step after the staging deployment.

The production deployment should depend on the successful completion of this security gate.

For example, in GitHub Actions, the production deployment job can declare the security-testing job as a dependency using `needs`.

This ensures that production promotion is skipped when the security gate fails.

## 7. Rolling Out the Gate: Report-only and Block Modes

Enforcing a security gate immediately may interrupt existing development workflows. A gradual rollout can help teams understand the results before introducing blocking behavior.

### 7.1. Report-only Mode

In report-only mode, the penetration test still runs and findings are collected and published, but the severity threshold is set to `NONE` so findings do not block the pipeline.

This allows teams to observe results and understand how the integration behaves before enforcing a threshold.

### 7.2. Block Mode

After reviewing the results, teams can configure a threshold such as `HIGH`.

Findings at or above the selected threshold then cause the gate to fail and prevent production promotion.

However, three settings must be considered independently:

- `severity-threshold`: Determines which findings block the pipeline.
- `on-scope-conflict`: Determines what happens when a change is outside the configured scope.
- `fail-on-infrastructure-error`: Determines whether infrastructure errors block the pipeline.

### 7.3. Important Baseline Consideration

A successful report-only run can advance the commit baseline. This means that when the threshold is later changed to `HIGH`, previously cleared commits are not automatically tested again.

If an earlier commit range needs to be reassessed, the relevant base and head commit SHAs can be specified explicitly according to the integration documentation.

Teams should understand this behavior before moving from report-only to blocking mode.

## 8. Practical Security Considerations

Several considerations are important when implementing this integration:

- **Start with a controlled rollout:** Observe report-only results before enforcing blocking thresholds.
- **Use OIDC:** Prefer temporary credentials over long-lived access keys.
- **Apply least privilege:** Restrict the IAM role's trust and permissions policies.
- **Review scope conflicts:** A change that was not tested should not be treated as equivalent to a successful penetration test.
- **Handle infrastructure errors deliberately:** If production promotion requires security sign-off, configure the gate to fail when the test cannot run.
- **Protect pipeline configuration:** Restrict who can modify workflow files and security-gate settings.
- **Pin integration versions:** Use an immutable release tag or commit SHA where supported, so upstream changes do not silently alter the pipeline integration.

## 9. Conclusion

AWS Continuum for Penetration Testing provides a practical approach to integrating penetration testing into modern CI/CD workflows.

By testing changes after deployment, using commit-based scoping, and applying configurable security gates, teams can make security validation part of the release process rather than relying exclusively on periodic assessments.

The integration does not replace every security activity. Instead, it complements automated scanning, secure development practices, and broader security assessments by providing an additional validation stage before production deployment.

## References

1. [AWS Security Blog – Penetration Testing Every Deployment with AWS Continuum](https://aws.amazon.com/blogs/security/penetration-testing-every-deployment-with-aws-continuum/)
2. [AWS Documentation – Run Penetration Tests from Your CI/CD Pipeline](https://docs.aws.amazon.com/securityagent/latest/userguide/cicd-pentest.html)
3. [AWS Documentation – GitHub Actions Integration Tutorial](https://docs.aws.amazon.com/securityagent/latest/userguide/sample-cicd-github.html)
