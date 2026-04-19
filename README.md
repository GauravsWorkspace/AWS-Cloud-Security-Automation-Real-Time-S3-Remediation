# AWS Cloud Security Automation: Real-Time S3 Remediation Lab

## 📌 Project Overview
As a final-semester MCA student specializing in Cybersecurity, I developed this project to demonstrate **Event-Driven Security** and **Automated Incident Response** within an AWS Cloud environment. 

The system identifies unauthorized S3 bucket public access and automatically remediates the configuration in under 60 seconds, significantly reducing the "window of exposure" for sensitive data.

---

## 🏗️ Technical Architecture
The automation follows a professional-grade security workflow:
1. **Audit:** Initial baseline audit performed using **Prowler** (Kali Linux).
2. **Detection:** **AWS CloudTrail** captures the `PutBucketPublicAccessBlock` API call.
3. **Filtering:** **Amazon EventBridge** monitors for specific security violations using a JSON event pattern.
4. **Remediation:** An **AWS Lambda** function (Python 3.12) is triggered to re-enforce the "Block All Public Access" policy via the Boto3 SDK.

---

## 🛠️ Technologies Used
* **Platform:** AWS (S3, Lambda, EventBridge, CloudTrail, IAM, CloudWatch)
* **Security Tooling:** Prowler Open Source Security Audit
* **Language:** Python 3.12 (Boto3)
* **Environment:** Kali Linux (Primary OS)
* **Hardware:** MSI GF63 Laptop

---

## 📜 Lambda Function (The "Fixer")
```python
import boto3

def lambda_handler(event, context):
    s3 = boto3.client('s3')
    
    # Extract bucket name from the CloudTrail event detail
    bucket_name = event['detail']['requestParameters']['bucketName']
    
    print(f"Alert! Public access detected on: {bucket_name}. Remediating...")
    
    # Re-apply the 'Block All Public Access' settings immediately
    s3.put_public_access_block(
        Bucket=bucket_name,
        PublicAccessBlockConfiguration={
            'BlockPublicAcls': True,
            'IgnorePublicAcls': True,
            'BlockPublicPolicy': True,
            'RestrictPublicBuckets': True
        }
    )
    return {"status": "Success", "bucket": bucket_name}

## 📊 Proof of Remediation

Note: Full visual evidence is located in the /assets directory.
1. Detection via CloudWatch

The system successfully logs the remediation event, providing the SOC team with immediate visibility.
2. Real-Time Automation Metrics

The Lambda Invocations graph confirms that the code executed the exact moment the misconfiguration occurred.
3. Before vs After
Configuration	Status	Visual Proof
Initial Violation	Public (Vulnerable)	View Screenshot
Post-Remediation	Private (Secure)	View Screenshot
🚀 Key Learning Outcomes

    IAM Least Privilege: Configured granular IAM roles for Lambda execution.

    Incident Response: Built a closed-loop system for real-time threat mitigation.

    Compliance as Code: Used Prowler to validate adherence to AWS security best practices (CIS Benchmarks).

Contact: [Your Name] | MCA Graduate (Cybersecurity) | SOC Analyst Aspirant
