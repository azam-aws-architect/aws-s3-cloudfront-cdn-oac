Description: Secure static web hosting architecture using Amazon S3 with disabled public access, integrated via CloudFront CDN using Origin Access Control (OAC).
# 🚀 Project 36: Secure Global Content Delivery Network (CDN) via AWS S3 & CloudFront (OAC)

## 📌 Project Overview
This project demonstrates the implementation of an enterprise-grade, highly scalable, and secure static content delivery architecture on AWS. It utilizes **Amazon S3** as an isolated storage backend and **Amazon CloudFront** as a global Content Delivery Network (CDN) to serve static web pages with low latency while enforcing strict object security boundaries.

---

## 🏗️ Infrastructure Architecture
[ End User / Browser ]
│
▼ (Secure HTTPS Request)
┌─────────────────────────────────┐
│    Amazon CloudFront CDN        │
│   (Edge Locations Cache)        │
└────────┬────────────────────────┘
│
▼ (Authorized IAM OAC Handshake)
┌─────────────────────────────────┐
│     Private Amazon S3 Bucket    │
│  (Public Access: 100% BLOCKED)  │
└─────────────────────────────────┘
---

## 🌟 Core Engineering Features
* **Zero Public Ingress:** The backend S3 bucket (`healthvitals-media-storage-azam`) is strictly isolated with **"Block all public access"** explicitly turned ON. Direct data leaks via object URLs are completely prevented.
* **Origin Access Control (OAC):** Implemented modern cryptographic service principal authorization to allow the CloudFront distribution proxy to securely ingest static objects without making the bucket public.
* **Global Optimization:** Leveraged CloudFront edge caching to serve assets globally, reducing structural server loads and maximizing download delivery speeds.

---

## 🛠️ Step-by-Step Deployment Logs

### Phase 1: Isolated Storage Tier Configuration
1. Created a private Amazon S3 storage instance: `healthvitals-media-storage-azam`.
2. Enforced structural compliance by checking **"Block all public access"**.
3. Ingested the core frontend deployment package (`index.html`) containing profile metrics.
4. *Verification:* Attempting to stream directly via the S3 Object URI outputted an expected **"Access Denied (403 Forbidden)"** state.

### Phase 2: Content Delivery Routing
1. Provisioned an Amazon CloudFront web distribution.
2. Mapped the `Origin Domain` directly to the target storage bucket node.
3. Enabled **Origin Access Control (OAC)** settings, establishing an IAM validation perimeter for the service principal `cloudfront.amazonaws.com`.
4. Suppressed heavy firewall costs for this deployment sandbox by disabling unnecessary AWS WAF features.

### Phase 3: Enforcing Least Privilege Permissions
Extracted the structured security payload from CloudFront and embedded it safely into the S3 **Bucket Policy** schema to allow explicit read authorizations:

```json
{
    "Version": "2012-10-17",
    "Statement": {
        "Sid": "AllowCloudFrontServicePrincipalReadOnly",
        "Effect": "Allow",
        "Principal": {
            "Service": "cloudfront.amazonaws.com"
        },
        "Action": "s3:GetObject",
        "Resource": "arn:aws:s3:::healthvitals-media-storage-azam/*",
        "Condition": {
            "StringEquals": {
                "AWS:SourceArn": "arn:aws:cloudfront::YOUR_ACCOUNT_ID:distribution/E1X2Y3Z4ABCDEF"
            }
        }
    }
}

