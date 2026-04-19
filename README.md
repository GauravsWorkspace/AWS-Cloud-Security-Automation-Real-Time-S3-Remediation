## 🚀 Proof of Work (Evidence)

To verify the effectiveness of the automation, I conducted a live simulation. Below is the technical evidence captured during the remediation lifecycle.

### 1. The Vulnerability (Initial State)
I simulated a security breach by disabling the "Block all public access" settings. This created a critical security gap.
**Status:** 🔴 CRITICAL - Public Access Enabled

![S3 Public Access Violation](./assets/before.png)

---

### 2. Detection & Remediation (The Logic)
The **CloudWatch Logs** capture the exact moment the Lambda function identified the target bucket and enforced security.
**Output:** `Alert! Public access detected... Remediating...`

![CloudWatch Audit Logs](./assets/logs.png)

---

### 3. Real-Time Automation Metrics
This graph shows the **Invocation Spike**. It proves the system is event-driven and responded instantly to the threat without manual input.

![Lambda Invocation Spike](./assets/metrics.png)

---

### 4. The Secure State (Final Result)
The S3 bucket permissions were automatically reverted to the "Private" state. The automation successfully enforced the security baseline.
**Status:** 🟢 SECURE - Block All Public Access: ON

![S3 Auto-Secured](./assets/after.png)# AWS Cloud Security Automation: Real-Time S3 Remediation

## 🛡️ Executive Summary
Misconfigured S3 buckets are a leading cause of data leaks. In this project, I engineered a **Cloud Security Posture Management (CSPM)** tool that automatically detects and remediates public S3 bucket violations in real-time. 

By leveraging event-driven architecture, the system reduces the window of exposure from hours to less than **60 seconds** without human intervention.

---

## 🏗️ Technical Architecture
The system utilizes a "Detection-Response" loop:
1. **Trigger:** An unauthorized user/process changes S3 bucket permissions.
2. **Detection:** **AWS CloudTrail** logs the `PutBucketPublicAccessBlock` event.
3. **Filtering:** **Amazon EventBridge** matches the event against a security rule.
4. **Remediation:** An **AWS Lambda** (Python 3.12) function executes, re-applying the "Block All Public Access" configuration via the **Boto3 SDK**.



---

## 🛠️ Tech Stack & Environment
* **Cloud Platform:** Amazon Web Services (AWS)
* **Programming:** Python 3.12 (Boto3 Library)
* **Security Audit:** Prowler (CIS Benchmark Compliance)
* **Operating System:** Kali Linux
* **Hardware:** MSI GF63

---

## 🚀 Proof of Work (The Evidence)

To verify the effectiveness of the automation, I conducted a live simulation. Below is the technical evidence captured during the remediation lifecycle.

### 1. The Vulnerability (Before)
I simulated a security breach by disabling the "Block all public access" settings on a production-style bucket.
* **Status:** 🔴 CRITICAL - Public Access Enabled
* **Evidence:** [View "Before" Screenshot](./assets/before.png)

### 2. The Remediation "Heartbeat" (During)
The **Lambda Invocations** graph shows the exact millisecond my Python code was triggered by EventBridge. This proves the system is responsive and reliable.
* **Metric:** 1 Success (0% Error Rate)
* **Evidence:** ![Lambda Invocation Spike](./assets/metrics.png)

### 3. Technical Audit Trail (Logs)
The **CloudWatch Logs** capture the function's internal logic as it identifies the target bucket and enforces security.
* **Log Output:** `Alert! Public access detected on: [bucket-name]. Remediating...`
* **Evidence:** ![CloudWatch Log Entry](./assets/logs.png)

### 4. The Secure State (After)
The S3 bucket permissions were automatically reverted to the "Private" state by the Lambda function.
* **Status:** 🟢 SECURE - Block All Public Access: ON
* **Evidence:** [View "After" Screenshot](./assets/after.png)

---

## 📜 Remediation Script
```python
import boto3

def lambda_handler(event, context):
    s3 = boto3.client('s3')
    bucket_name = event['detail']['requestParameters']['bucketName']
    
    print(f"Alert! Public access detected on: {bucket_name}. Remediating...")
    
    s3.put_public_access_block(
        Bucket=bucket_name,
        PublicAccessBlockConfiguration={
            'BlockPublicAcls': True,
            'IgnorePublicAcls': True,
            'BlockPublicPolicy': True,
            'RestrictPublicBuckets': True
        }
    )
    return {"status": "Success", "remediated_bucket": bucket_name}
