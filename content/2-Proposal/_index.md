---
title: "Proposal"
date: 2026-09-14
weight: 2
chapter: false
pre: "<b> 2. </b>"
---

# AWS Phishing Detection Engine (Trust Check)

> This proposal presents the objectives, architecture, technical approach, implementation plan, and expected outcomes of the AWS Phishing Detection Engine (Trust Check).

## 1. Executive Summary

The **AWS Phishing Detection Engine (Trust Check)** is a cloud-based cybersecurity platform designed to help users identify potential phishing threats through automated analysis of URLs, email content, and uploaded files.

The project addresses the growing difficulty of identifying sophisticated phishing attempts, particularly for users with limited cybersecurity knowledge. Rather than relying solely on a binary safe-or-unsafe result, Trust Check combines multiple detection techniques and external threat intelligence sources to provide an explainable **Trust Score ranging from 0 to 100**.

The platform adopts an event-driven serverless architecture built on Amazon Web Services (AWS). Its core services include Amazon API Gateway, AWS Lambda, Amazon Simple Queue Service (SQS), Amazon DynamoDB, and Amazon Simple Storage Service (S3).

By separating request handling from asynchronous detection tasks, the system supports different scanning workloads, improves resource utilization, and reduces unnecessary processing through caching.

## 2. Problem Statement and Project Objectives

### 2.1. Problem Statement

Phishing remains a significant cybersecurity threat. Attackers use fraudulent websites, deceptive emails, misleading URLs, and malicious file attachments to steal sensitive information, compromise accounts, and distribute malware.

Identifying these threats can be challenging because phishing techniques continue to evolve. Traditional detection approaches that rely mainly on reputation databases or predefined rules may not identify newly emerging threats.

Furthermore, security tools do not always explain why particular content is considered suspicious. This can make it difficult for users without technical knowledge to understand detection results and make informed decisions.

These challenges highlight the need for an accessible and automated phishing detection platform that supports multiple content types, combines different analysis techniques, and provides understandable security assessments.

### 2.2. Project Objectives

The primary objective is to develop a cloud-based platform that assists users in identifying potential phishing threats.

The specific objectives are:

- **Multi-Format Threat Detection:** Support the analysis of suspicious URLs, email content, and uploaded files through specialized detection modules.
- **Hybrid Detection Approach:** Combine heuristic rules, content and metadata analysis, and external threat intelligence services.
- **Explainable Risk Assessment:** Generate a Trust Score from 0 to 100, accompanied by security classifications and explanations of detected indicators.
- **Serverless Cloud Architecture:** Use AWS managed services to support event-driven execution, asynchronous processing, and reduced infrastructure management.
- **Efficient Resource Utilization:** Apply caching mechanisms to minimize redundant scans and unnecessary external API requests.
- **User-Friendly Interface:** Provide a web-based platform for submitting content, monitoring scanning progress, and reviewing detection results.

## 3. Proposed Solution and Architecture

### 3.1. Solution Overview

Trust Check provides three primary scanning functions:

1. **URL Detection:** Examines URL characteristics and domain-related indicators, with support from external reputation services such as VirusTotal, Google Safe Browsing, and URLhaus.
2. **Email Detection:** Analyzes email content, embedded links, and relevant message characteristics to identify potential phishing indicators.
3. **File Detection:** Performs static analysis of uploaded files, including file properties and threat intelligence checks, with MalwareBazaar as an additional intelligence source.

After the analysis process, a centralized Scoring Engine evaluates the collected indicators and generates a Trust Score.

| Trust Score | Classification | Interpretation |
|---|---|---|
| 80–100 | SAFE | No significant threats identified by the available detection methods. |
| 50–79 | SUSPICIOUS | Potential security risks detected; further verification is recommended. |
| 0–49 | DANGEROUS | Significant threat indicators identified; users should avoid interacting with the content. |

The score is accompanied by explanations of the detected indicators. It represents an assessment based on the available analysis methods and should not be interpreted as an absolute guarantee that content is safe.

### 3.2. System Architecture

The system adopts an event-driven serverless architecture with three primary processing layers:

- **Request Management Layer:** Receives and validates user submissions through Amazon API Gateway and AWS Lambda.
- **Threat Detection Layer:** Distributes scanning tasks through Amazon SQS to specialized workers for URL/email analysis and file analysis.
- **Data Management Layer:** Uses Amazon DynamoDB to store scanning jobs, detection results, and eligible cached information. Amazon S3 supports uploaded file storage and related analysis artifacts.

The architecture separates URL and email processing from file processing. This allows workloads with different processing requirements to be handled independently.

<a href="/aws-workshop/images/2-Proposal/trust_check_architecture.png" target="_blank">
  <img src="/aws-workshop/images/2-Proposal/trust_check_architecture.png"
       alt="Trust Check Architecture"
       style="width: 90vw; max-width: 1200px; height: auto; display: block; margin-left: 50%; transform: translateX(-50%); cursor: zoom-in;">
</a>

*Figure 2.1. High-Level Architecture of the AWS Phishing Detection Engine.*

### 3.3. System Workflow

The system follows an asynchronous scanning workflow consisting of the following stages:

1. **Request Submission:** Users submit a URL, email content, or file through the web interface.
2. **Request Validation:** Amazon API Gateway forwards the request to the appropriate AWS Lambda function for validation and job initialization.
3. **Task Distribution:** Amazon SQS distributes the scanning task to the appropriate processing queue.
4. **Threat Detection:** Specialized workers analyze the submitted content and retrieve relevant threat intelligence when available.
5. **Trust Score Calculation:** The Scoring Engine evaluates collected indicators and assigns a score and security classification.
6. **Result Storage:** The completed result is stored in Amazon DynamoDB. Eligible results may also be cached for reuse.
7. **Result Retrieval:** The frontend retrieves the job status and displays the completed analysis result to the user.

For file analysis, Amazon S3 supports the upload workflow through temporary presigned URLs. This allows authorized uploads without exposing AWS credentials to the client.

## 4. Technical Implementation

### 4.1. Development Technologies

The project uses the following technologies and services:

| Category | Technologies |
|---|---|
| Frontend | HTML, CSS, JavaScript |
| Backend | Python 3.12, AWS Lambda |
| API Management | Amazon API Gateway |
| Asynchronous Processing | Amazon SQS |
| Database and Caching | Amazon DynamoDB |
| File Storage | Amazon S3 |
| Threat Intelligence | VirusTotal, Google Safe Browsing, URLhaus, MalwareBazaar |
| AWS Integration | Boto3 |
| Testing | Python unittest |
| Development Tools | Visual Studio Code, AWS Management Console |
| Version Control | Git and GitHub |

### 4.2. Detection and Risk Assessment

The detection engine uses different analysis approaches according to the submitted content:

- URL analysis examines suspicious URL characteristics and domain-related indicators.
- Email analysis focuses on message content, embedded links, and potentially deceptive characteristics.
- File analysis evaluates relevant file properties and static indicators of malicious activity.

The collected indicators are passed to the Scoring Engine, which calculates the Trust Score and provides supporting explanations.

External threat intelligence services are used according to the content type and available integrations. Their availability, response limits, and service conditions may affect the completeness of the analysis.

### 4.3. Asynchronous Processing and Data Management

Amazon SQS separates request submission from the actual detection process. This prevents longer scanning operations from unnecessarily blocking the initial API response.

Amazon DynamoDB maintains scanning job information and eligible cached results. Before starting a new scan, the application can check whether a valid previous result is available.

The caching mechanism helps reduce repeated analysis, unnecessary Lambda executions, and external threat intelligence requests. Cache expiration must be checked before a stored result is reused.

Amazon S3 supports file uploads and associated analysis artifacts.

## 5. Implementation Timeline and Milestones

The project is carried out within the **First Cloud AI Journey (FCAJ) – Workforce Bootcamp 2026**, from **14 September 2026 to 14 December 2026**.

The following schedule provides an indicative allocation of project activities across the three-month participation period:

| Period | Main Activities |
|---|---|
| 14 September – 13 October 2026 | Define project requirements, identify detection objectives, design the system architecture, and prepare the AWS infrastructure. |
| 14 October – 13 November 2026 | Develop and integrate URL, email, and file detection modules; configure asynchronous processing, data storage, and threat intelligence integrations. |
| 14 November – 14 December 2026 | Complete frontend and backend integration, conduct functional testing, review detection results, document limitations, and evaluate AWS costs and optimization opportunities. |

The schedule is intended as a project planning framework. Actual task completion and evaluation results should be documented according to the implementation evidence.

## 6. Budget Estimation

The project uses AWS managed services with consumption-based pricing models. Infrastructure costs depend on service configuration, resource consumption, deployment region, and actual scanning workload.

The project report presents a preliminary estimate of approximately **USD 7.68 per month** for a hypothetical workload of **10,000 scanning requests** in the **Asia Pacific (Singapore) Region**.

This estimate is based on assumed service configurations and is presented before applicable Free Tier allowances and promotional credits. It should not be interpreted as a guaranteed monthly charge.

The main cost considerations include:

- AWS Lambda invocations, memory allocation, and execution duration.
- Amazon API Gateway request volume.
- Amazon SQS message processing.
- Amazon DynamoDB capacity and storage.
- Amazon S3 storage and related requests.
- Amazon CloudWatch logging and monitoring.
- External threat intelligence service usage, where applicable.

Actual costs should be reviewed using AWS Pricing Calculator and AWS Billing and Cost Management. AWS Cost Explorer can help examine service usage and spending trends.

## 7. Risk Assessment

The project involves several technical and operational risks that should be considered during implementation and evaluation.

| Risk | Potential Impact | Mitigation Approach |
|---|---|---|
| Incomplete Threat Intelligence | Detection results may lack relevant indicators. | Combine multiple detection techniques and clearly communicate analysis limitations. |
| External API Availability | Third-party services may be unavailable or impose request limits. | Handle integration failures and avoid treating unavailable intelligence as a clean result. |
| False Positives and False Negatives | Content may be incorrectly classified. | Evaluate detection indicators, test different scenarios, and explain the basis of each score. |
| Duplicate or Failed Tasks | Scanning jobs may be processed more than once or fail during execution. | Apply appropriate SQS retry settings, visibility timeouts, and Dead-Letter Queue policies where configured. |
| Excessive Resource Consumption | High workloads or repeated scans may increase AWS costs. | Use caching, workload separation, resource monitoring, and appropriate configuration. |
| Unauthorized File Access | Uploaded files may be exposed if access is not properly controlled. | Apply appropriate S3 permissions and use temporary presigned URLs for authorized uploads. |
| Incomplete Scanning Results | A failed or interrupted scan may produce an unreliable assessment. | Track job status and distinguish incomplete or failed scans from completed security assessments. |

The Trust Score should be treated as a supporting security assessment rather than a guarantee of safety. Users should exercise caution when dealing with suspicious content.

## 8. Expected Outcomes

The project aims to deliver a functional web-based phishing analysis platform with the following outcomes:

- **Multi-Format Detection:** Support URL, email, and file analysis through specialized detection modules.
- **Integrated Detection Techniques:** Combine heuristic analysis, content and metadata inspection, and available external threat intelligence.
- **Explainable Trust Score:** Present a numerical score, security classification, and supporting detection indicators.
- **Serverless AWS Architecture:** Demonstrate the integration of API Gateway, Lambda, SQS, DynamoDB, and S3.
- **Asynchronous Processing:** Separate request handling from detection execution through queue-based processing.
- **Resource Optimization:** Reduce redundant scanning through caching and workload-specific resource allocation.
- **Accessible User Interface:** Allow users to submit suspicious content, monitor scanning progress, and understand the resulting security assessment.

Overall, AWS Phishing Detection Engine (Trust Check) demonstrates how AWS cloud services can be applied to develop a modular, explainable, and resource-conscious cybersecurity solution for identifying potential phishing threats.