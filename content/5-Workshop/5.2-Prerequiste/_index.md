---
title : "Prerequisites"
date : 2026-11-27
weight : 2
chapter : false
pre : " <b> 5.2. </b> "
---

Before deploying **Trust Check** in section 5.4, prepare the required AWS account, IAM permissions, local tools, and source code below.

#### 1. AWS Account & Permissions
- An active **AWS Account** using the **Asia Pacific (Singapore) `ap-southeast-1`** Region.
- An IAM User or Role with permissions to create and manage: **DynamoDB, Amazon S3, Amazon SQS, IAM, AWS Lambda, API Gateway (HTTP API), Systems Manager Parameter Store (optional), and CloudWatch**.
- Ensure billing alerts or AWS Free Tier monitoring are enabled so you can track usage during the lab.

#### 2. Local Environment & Tools
Verify the following tools are installed on your machine (Windows PowerShell or terminal):
- **Python 3.12** (matching the Lambda `Python 3.12` runtime) — check with `python --version`. No external `pip` packages are required because the backend uses only the Python standard library and `boto3` (built into Lambda).
- **Git** — check with `git --version`.
- **Modern web browser** (Chrome or Edge) to operate the AWS Management Console and test the frontend.

#### 3. External Threat Intelligence API Keys (Optional)
The system can run without external API keys (with lower confidence scores), or you can prepare free-tier keys for full detection in sections 5.4.6 / 5.4.8:
- **VirusTotal** (`VT_API_KEY`, optional backup keys `VT_BACKUP_API_KEYS`)
- **Google Safe Browsing** (`GSB_API_KEY`)
- **abuse.ch** (`ABUSECH_AUTH_KEY`)

#### 4. Clone the Source Code
Open PowerShell and clone the `AWS-Phishing-Detection` repository used in section 5.4.1:

    git clone <link-repo-AWS-Phishing-Detection>
    cd AWS-Phishing-Detection

Confirm the `backend` folder contains `ingest_lambda.py`, `status_lambda.py`, `build_zip.py`, `common/`, `detectors/`, and `tests/`.