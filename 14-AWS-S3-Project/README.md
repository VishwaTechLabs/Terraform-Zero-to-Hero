<div align="center">

# 🪣 Terraform AWS S3 — Complete Real-World Project

### 🔐 Secure Object Storage • Encryption • Versioning • Lifecycle • Policies • Replication | VishwaTech Labs

[![Terraform](https://img.shields.io/badge/Terraform-844FBA?logo=terraform&logoColor=white)](https://developer.hashicorp.com/terraform/docs)
[![AWS S3](https://img.shields.io/badge/AWS-S3-FF9900?logo=amazons3&logoColor=white)](https://aws.amazon.com/s3/)
[![Security](https://img.shields.io/badge/Security-Hardened-red)](#-security-hardening)
[![Storage](https://img.shields.io/badge/Storage-Object%20Storage-blue)](#-what-is-amazon-s3)
[![Labs](https://img.shields.io/badge/Labs-30+-success)](#-hands-on-labs)

**Build a production-style Amazon S3 platform with Terraform — secure buckets, encryption, versioning, lifecycle management, IAM, bucket policies, replication, logging, object ownership, cost controls and reusable modules.**

[📘 Amazon S3](https://docs.aws.amazon.com/s3/) •
[📘 Terraform AWS S3](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/s3_bucket) •
[🔐 S3 Security](https://docs.aws.amazon.com/AmazonS3/latest/userguide/security-best-practices.html)

</div>

---

# 🎯 Project Goal

Build secure S3 infrastructure:

```text
                       AWS ACCOUNT
                            │
                            ▼
                    ┌───────────────┐
                    │   S3 Bucket   │
                    │               │
                    │ Encryption    │
                    │ Versioning     │
                    │ Lifecycle     │
                    │ Object Lock*   │
                    │ Logging        │
                    │ Access Policy  │
                    └───────┬───────┘
                            │
              ┌─────────────┼─────────────┐
              ▼             ▼             ▼
           Terraform       Apps         CI/CD
              │             │             │
              └─────────────┼─────────────┘
                            ▼
                       AWS IAM / KMS
```

`*` Object Lock has special creation/configuration considerations and should be enabled deliberately.

---

# 🧠 1. What Is Amazon S3?

Amazon S3 is an object storage service.

Think:

```text
Bucket
  │
  ├── Object
  ├── Object
  ├── Object
  └── Object
```

An object consists conceptually of:

```text
Data
+
Key
+
Metadata
```

S3 is commonly used for:

```text
Backups
Logs
Static assets
Data lakes
Artifacts
Documents
Terraform state
Application uploads
Archives
```

---

# 🪣 2. Bucket

A bucket is a container for objects.

Example:

```text
vishwatech-prod-data
```

Objects:

```text
logs/app.log
images/logo.png
reports/2026/report.pdf
```

Terraform:

```hcl
resource "aws_s3_bucket" "this" {
  bucket = var.bucket_name

  tags = var.tags
}
```

Bucket names must satisfy Amazon S3 naming requirements and must be globally unique within the relevant S3 namespace.

---

# 🔑 3. Object Key

S3 does not use traditional folders.

Example:

```text
reports/2026/august/report.pdf
```

is an object key.

Console UI makes it look like:

```text
reports/
└── 2026/
    └── august/
        └── report.pdf
```

Conceptually:

```text
Bucket
   ↓
Object Key
   ↓
Object
```

---

# 🔐 4. S3 Security Baseline

For a private production bucket:

```text
☑ Block public access
☑ Object ownership
☑ Encryption
☑ Versioning where required
☑ Least privilege
☑ Bucket policy review
☑ TLS/secure transport
☑ Logging/auditing where required
☑ Lifecycle rules
☑ Backup/recovery strategy
```

---

# 🚫 5. Block Public Access

Use:

```hcl
resource "aws_s3_bucket_public_access_block" "this" {
  bucket = aws_s3_bucket.this.id

  block_public_acls       = true
  block_public_policy     = true
  ignore_public_acls      = true
  restrict_public_buckets = true
}
```

This is a strong default for private buckets.

---

# 👤 6. Object Ownership

Modern S3 designs commonly use:

```hcl
resource "aws_s3_bucket_ownership_controls" "this" {
  bucket = aws_s3_bucket.this.id

  rule {
    object_ownership = "BucketOwnerEnforced"
  }
}
```

This disables ACL-based object ownership management for the bucket and simplifies access control.

---

# 🔐 7. Encryption

S3 supports server-side encryption.

Example using SSE-S3:

```hcl
resource "aws_s3_bucket_server_side_encryption_configuration" "this" {
  bucket = aws_s3_bucket.this.id

  rule {
    apply_server_side_encryption_by_default {
      sse_algorithm = "AES256"
    }
  }
}
```

For organizations requiring customer-managed key control, SSE-KMS can be used.

---

# 🔑 8. SSE-KMS

Example concept:

```hcl
resource "aws_s3_bucket_server_side_encryption_configuration" "this" {
  bucket = aws_s3_bucket.this.id

  rule {
    apply_server_side_encryption_by_default {
      kms_master_key_id = aws_kms_key.s3.arn
      sse_algorithm     = "aws:kms"
    }
  }
}
```

Remember:

```text
S3 permissions
+
KMS key permissions
```

both need to be designed correctly.

---

# 🔒 9. KMS Key Architecture

```text
Application
    │
    ▼
   S3
    │
    ▼
  KMS Key
    │
    ▼
Encrypted Object
```

Consider:

```text
Key policy
IAM policy
Key rotation
Key administrators
Key users
Audit logging
```

---

# 🔄 10. Versioning

Versioning keeps multiple versions of an object.

Example:

```text
report.pdf
   │
   ├── Version 1
   ├── Version 2
   └── Version 3
```

Terraform:

```hcl
resource "aws_s3_bucket_versioning" "this" {
  bucket = aws_s3_bucket.this.id

  versioning_configuration {
    status = "Enabled"
  }
}
```

Versioning can help with:

```text
Accidental deletion
Accidental overwrite
Recovery
Rollback
```

It does not replace a complete backup strategy.

---

# 🧹 11. Lifecycle Rules

Lifecycle rules automate object transitions/deletion.

Concept:

```text
STANDARD
   ↓
INFREQUENT ACCESS
   ↓
ARCHIVE
   ↓
DELETE
```

Use based on:

```text
Access frequency
Retention requirements
Compliance
Cost
Recovery requirements
```

---

# 📦 12. Lifecycle Example

Example concept:

```hcl
resource "aws_s3_bucket_lifecycle_configuration" "this" {
  bucket = aws_s3_bucket.this.id

  rule {
    id     = "archive-old-objects"
    status = "Enabled"

    filter {
      prefix = "logs/"
    }

    transition {
      days          = 30
      storage_class = "STANDARD_IA"
    }

    expiration {
      days = 365
    }
  }
}
```

Validate lifecycle behavior against current AWS and provider documentation before production use.

---

# 🗑️ 13. Noncurrent Versions

Versioned buckets can accumulate old versions.

Lifecycle rules can manage noncurrent versions.

Concept:

```text
Current Version
       │
       ▼
Older Version
       │
       ▼
Expiration
```

This is important for cost control.

---

# 🧱 14. Bucket Policy

Bucket policies are resource-based policies.

Example pattern:

```hcl
data "aws_iam_policy_document" "bucket" {
  statement {
    sid    = "DenyInsecureTransport"
    effect = "Deny"

    principals {
      type        = "*"
      identifiers = ["*"]
    }

    actions = ["s3:*"]

    resources = [
      aws_s3_bucket.this.arn,
      "${aws_s3_bucket.this.arn}/*"
    ]

    condition {
      test     = "Bool"
      variable = "aws:SecureTransport"
      values   = ["false"]
    }
  }
}
```

Then:

```hcl
resource "aws_s3_bucket_policy" "this" {
  bucket = aws_s3_bucket.this.id
  policy = data.aws_iam_policy_document.bucket.json
}
```

---

# 🚨 15. Secure Transport

A useful bucket policy pattern is:

```text
HTTP
 ↓
DENY

HTTPS
 ↓
ALLOW according to other policies
```

This helps prevent insecure transport to the bucket.

---

# 👥 16. IAM Policy vs Bucket Policy

### IAM Policy

Attached to:

```text
User
Role
Group
```

### Bucket Policy

Attached to:

```text
S3 Bucket
```

Mental model:

```text
IAM Policy
     +
Bucket Policy
     +
Block Public Access
     +
KMS
     ↓
Effective Access
```

Always evaluate the complete authorization model.

---

# 🧠 17. Least Privilege

Avoid:

```text
s3:*
Resource: *
```

when unnecessary.

Prefer:

```text
s3:GetObject
s3:PutObject
s3:ListBucket
```

for only the resources required.

Example:

```text
Application
  ↓
GetObject
  ↓
specific bucket/prefix
```

---

# 🗂️ 18. Prefix-Level Access

Example requirement:

```text
Application A
   ↓
bucket/app-a/*
```

Application B:

```text
bucket/app-b/*
```

Policies can be designed around object ARNs/prefixes.

Always test both:

```text
bucket ARN
object ARN
```

because bucket-level and object-level actions use different resource forms.

---

# 📋 19. Bucket Logging

S3 supports multiple logging/auditing approaches depending on the requirement.

Possible services/patterns include:

```text
CloudTrail data events
S3 server access logging
CloudWatch / security analytics integrations
```

For sensitive production environments, decide what audit visibility is required and configure the appropriate mechanism.

---

# 🔍 20. CloudTrail Data Events

CloudTrail can record object-level S3 API activity when data events are configured.

Examples:

```text
GetObject
PutObject
DeleteObject
```

This can generate significant event volume and cost, so configure selectors deliberately.

---

# 🛡️ 21. S3 Access Analyzer

AWS tooling can help identify unintended access paths.

Use security analysis to check:

```text
Public access
Cross-account access
Policy exposure
```

Review findings instead of assuming every finding represents an actual vulnerability.

---

# 🔁 22. Cross-Region Replication

S3 replication can copy objects between buckets in different regions.

Architecture:

```text
Source Bucket
   │
   │ Replication
   ▼
Destination Bucket
```

Use cases:

```text
Disaster recovery
Compliance
Regional resilience
Data distribution
```

Replication has prerequisites and IAM configuration.

---

# 🌍 23. Replication Architecture

```text
AWS Region A
┌─────────────────┐
│ Source Bucket   │
│ Versioned       │
└────────┬────────┘
         │
         │ Replication
         ▼
AWS Region B
┌─────────────────┐
│ Destination     │
│ Bucket          │
└─────────────────┘
```

Test replication with non-production data first.

---

# 🔐 24. Replication IAM

Replication generally requires an IAM role with appropriate permissions.

Concept:

```text
S3
 │
 ▼
Replication Role
 │
 ├── Read source
 └── Write destination
```

Keep permissions limited to the replication buckets and required actions.

---

# 🧊 25. Storage Classes

Common S3 storage classes include:

```text
S3 Standard
S3 Intelligent-Tiering
S3 Standard-IA
S3 One Zone-IA
S3 Glacier Instant Retrieval
S3 Glacier Flexible Retrieval
S3 Glacier Deep Archive
```

Choose based on:

```text
Access pattern
Latency
Retention
Availability
Cost
```

---

# 💰 26. Cost Optimization

S3 cost can include:

```text
Storage
Requests
Data retrieval
Data transfer
Replication
Lifecycle transitions
Monitoring
KMS requests
```

Use lifecycle policies and appropriate storage classes.

Do not move data to archive storage without understanding retrieval requirements.

---

# 🧱 27. Object Lock

S3 Object Lock supports WORM-style retention controls for supported buckets.

Use cases:

```text
Compliance
Immutable backups
Ransomware resilience
Regulatory retention
```

Object Lock should be designed carefully because retention/legal hold behavior can prevent deletion.

---

# ⚠️ 28. Object Lock Warning

Do not enable retention policies in production without understanding:

```text
Retention mode
Retention period
Legal holds
Deletion behavior
Recovery process
```

Test thoroughly first.

---

# 🏷️ 29. Bucket Tags

```hcl
resource "aws_s3_bucket" "this" {
  bucket = var.bucket_name

  tags = {
    Project     = var.project_name
    Environment = var.environment
    ManagedBy   = "Terraform"
    Owner       = var.owner
  }
}
```

Tags support:

```text
Cost allocation
Ownership
Automation
Inventory
Governance
```

---

# 📁 30. Project Folder Structure

```text
14-Terraform-AWS-S3-Project/
│
├── README.md
│
├── terraform/
│   ├── versions.tf
│   ├── provider.tf
│   ├── variables.tf
│   ├── locals.tf
│   ├── bucket.tf
│   ├── encryption.tf
│   ├── versioning.tf
│   ├── lifecycle.tf
│   ├── ownership.tf
│   ├── public_access.tf
│   ├── policy.tf
│   ├── replication.tf
│   ├── logging.tf
│   ├── outputs.tf
│   ├── terraform.tfvars.example
│   └── .gitignore
│
├── modules/
│   └── s3/
│       ├── main.tf
│       ├── variables.tf
│       ├── outputs.tf
│       └── versions.tf
│
└── examples/
    ├── private-bucket/
    ├── logs-bucket/
    └── replication/
```

---

# ⚙️ 31. Provider Configuration

```hcl
terraform {
  required_version = ">= 1.5.0"

  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 6.0"
    }
  }
}

provider "aws" {
  region = var.aws_region
}
```

---

# 📋 32. Variables

```hcl
variable "aws_region" {
  description = "AWS region"
  type        = string
  default     = "eu-north-1"
}

variable "bucket_name" {
  description = "Globally unique S3 bucket name"
  type        = string
}

variable "environment" {
  description = "Environment"
  type        = string

  validation {
    condition = contains(
      ["dev", "stage", "prod"],
      var.environment
    )

    error_message = "Use dev, stage, or prod."
  }
}

variable "owner" {
  description = "Resource owner"
  type        = string
}
```

---

# 🪣 33. Create Bucket

```hcl
resource "aws_s3_bucket" "this" {
  bucket = var.bucket_name

  tags = {
    Environment = var.environment
    Owner       = var.owner
    ManagedBy   = "Terraform"
  }
}
```

For production, keep bucket naming predictable and avoid putting sensitive information in bucket names.

---

# 🚫 34. Block Public Access

```hcl
resource "aws_s3_bucket_public_access_block" "this" {
  bucket = aws_s3_bucket.this.id

  block_public_acls       = true
  block_public_policy     = true
  ignore_public_acls      = true
  restrict_public_buckets = true
}
```

---

# 👤 35. Ownership Controls

```hcl
resource "aws_s3_bucket_ownership_controls" "this" {
  bucket = aws_s3_bucket.this.id

  rule {
    object_ownership = "BucketOwnerEnforced"
  }
}
```

---

# 🔐 36. Encryption

```hcl
resource "aws_s3_bucket_server_side_encryption_configuration" "this" {
  bucket = aws_s3_bucket.this.id

  rule {
    apply_server_side_encryption_by_default {
      sse_algorithm = "AES256"
    }
  }
}
```

---

# 🔄 37. Versioning

```hcl
resource "aws_s3_bucket_versioning" "this" {
  bucket = aws_s3_bucket.this.id

  versioning_configuration {
    status = "Enabled"
  }
}
```

---

# 🧹 38. Lifecycle

A production lifecycle configuration should be based on the actual retention policy.

Example:

```hcl
resource "aws_s3_bucket_lifecycle_configuration" "this" {
  bucket = aws_s3_bucket.this.id

  rule {
    id     = "log-retention"
    status = "Enabled"

    filter {
      prefix = "logs/"
    }

    transition {
      days          = 30
      storage_class = "STANDARD_IA"
    }

    expiration {
      days = 365
    }
  }
}
```

---

# 🛡️ 39. Deny HTTP

```hcl
data "aws_iam_policy_document" "secure_transport" {
  statement {
    sid    = "DenyInsecureTransport"
    effect = "Deny"

    principals {
      type        = "*"
      identifiers = ["*"]
    }

    actions = ["s3:*"]

    resources = [
      aws_s3_bucket.this.arn,
      "${aws_s3_bucket.this.arn}/*"
    ]

    condition {
      test     = "Bool"
      variable = "aws:SecureTransport"
      values   = ["false"]
    }
  }
}
```

---

# 📜 40. Attach Bucket Policy

```hcl
resource "aws_s3_bucket_policy" "this" {
  bucket = aws_s3_bucket.this.id
  policy = data.aws_iam_policy_document.secure_transport.json
}
```

Do not replace security policy with a permissive public-read policy unless public access is an intentional, reviewed requirement.

---

# 📤 41. Outputs

```hcl
output "bucket_id" {
  description = "S3 bucket ID"
  value       = aws_s3_bucket.this.id
}

output "bucket_arn" {
  description = "S3 bucket ARN"
  value       = aws_s3_bucket.this.arn
}

output "bucket_domain_name" {
  description = "Bucket regional domain"
  value       = aws_s3_bucket.this.bucket_regional_domain_name
}
```

---

# 🧩 42. Reusable S3 Module

Root:

```hcl
module "storage" {
  source = "./modules/s3"

  bucket_name = "vishwatech-prod-data"
  environment = "prod"
  owner       = "platform"
}
```

Module should own:

```text
Bucket
Encryption
Versioning
Ownership
Public access block
Lifecycle
Policy
Outputs
```

depending on the desired module boundary.

---

# 🔐 43. Sensitive Data

Do not put secrets into object names or tags.

Do not use S3 as a generic replacement for a secrets manager.

For secrets:

```text
AWS Secrets Manager
AWS Systems Manager Parameter Store
```

are usually more appropriate depending on the use case.

---

# 🗄️ 44. S3 as Terraform Backend

S3 can also store Terraform state.

Architecture:

```text
Terraform
    │
    ▼
S3
    │
    ▼
terraform.tfstate
```

For a production Terraform backend, combine:

```text
Encryption
Access control
Versioning
Locking/concurrency
Audit
Recovery
```

See the previous module:

```text
10-Terraform-Remote-State-and-Locking
```

---

# 🔍 45. Troubleshooting

## BucketAlreadyExists / Naming Error

Check:

```text
Bucket name uniqueness
Naming rules
Account/region assumptions
```

---

## AccessDenied

Check:

```text
IAM
Bucket policy
KMS permissions
SCPs
Explicit denies
Account
Role
```

Identity:

```bash
aws sts get-caller-identity
```

---

## Object Upload Denied

Check:

```text
s3:PutObject
Object ARN
KMS permissions
Bucket policy
Encryption requirements
```

---

## Cannot Delete Bucket

Possible causes:

```text
Bucket contains objects
Versioned objects exist
Delete markers exist
Object Lock retention
Legal hold
Policy restrictions
```

Never force-delete production data just to make Terraform finish.

---

# 🧪 46. Hands-On Labs

## Lab 01 — Create S3 Bucket

Create a private bucket.

## Lab 02 — Public Access Block

Enable all four public access block settings.

## Lab 03 — Ownership Controls

Use BucketOwnerEnforced.

## Lab 04 — Encryption

Configure SSE-S3.

## Lab 05 — KMS Encryption

Configure SSE-KMS in a test environment.

## Lab 06 — Versioning

Enable versioning.

## Lab 07 — Lifecycle

Transition and expire test objects.

## Lab 08 — Noncurrent Versions

Configure noncurrent version lifecycle management.

## Lab 09 — Secure Transport

Deny insecure requests.

## Lab 10 — IAM Access

Create least-privilege access.

## Lab 11 — Prefix Access

Restrict application access to a prefix.

## Lab 12 — Bucket Module

Build reusable module.

## Lab 13 — Tags

Standardize metadata.

## Lab 14 — Logging

Configure an appropriate S3 audit/logging mechanism.

## Lab 15 — CloudTrail Data Events

Capture object-level API activity.

## Lab 16 — Replication

Create test cross-region replication.

## Lab 17 — Replication IAM

Build least-privilege replication role.

## Lab 18 — Object Lock

Test retention behavior in a dedicated lab bucket.

## Lab 19 — Storage Classes

Compare storage strategies.

## Lab 20 — Cost Analysis

Estimate storage/request/retrieval costs.

## Lab 21 — Terraform Backend

Use S3 for remote state in a test project.

## Lab 22 — State Version Recovery

Practice recovery using versioned state storage.

## Lab 23 — Security Review

Audit bucket policy.

## Lab 24 — Access Analyzer

Review unintended external access.

## Lab 25 — Multi-Environment

Create:

```text
dev
stage
prod
```

buckets.

## Lab 26 — Multi-Region

Build source/destination storage.

## Lab 27 — CI/CD Artifact Bucket

Create secure artifact storage.

## Lab 28 — Application Upload Bucket

Design prefix-based access.

## Lab 29 — Disaster Recovery

Test replication/recovery.

## Lab 30 — Enterprise Challenge

Build:

```text
Private S3
├── Encryption
├── Versioning
├── Lifecycle
├── Public Access Block
├── Ownership Controls
├── Secure Transport
├── IAM
├── Audit
├── Replication
└── Terraform Module
```

---

# 🏆 47. Production Checklist

```text
BUCKET
☑ Unique name
☑ Correct region
☑ Clear ownership
☑ Tags

SECURITY
☑ Public access blocked
☑ Ownership controls
☑ Encryption
☑ KMS where required
☑ Least privilege
☑ Secure transport
☑ Policy review

DATA
☑ Versioning where required
☑ Lifecycle
☑ Retention
☑ Backup/replication
☑ Recovery testing

AUDIT
☑ CloudTrail/data events where required
☑ Access analysis
☑ Monitoring

TERRAFORM
☑ Module
☑ Variables
☑ Outputs
☑ Remote state
☑ Locking
☑ CI/CD
```

---

# 🚫 48. Never Do These

```text
❌ Make production buckets public accidentally
❌ Use s3:* unnecessarily
❌ Store credentials in S3 object metadata
❌ Commit sensitive tfvars
❌ Disable encryption without a reason
❌ Ignore versioned-object costs
❌ Delete Object Lock data without understanding retention
❌ Copy old S3 tutorials without checking current provider behavior
❌ Give every application full bucket access
❌ Treat versioning as a complete backup strategy
```

---

# 🎓 49. Interview Questions

### Beginner

1. What is Amazon S3?
2. What is a bucket?
3. What is an S3 object?
4. What is an object key?
5. What is S3 versioning?
6. What is S3 encryption?
7. What is a bucket policy?
8. What is Block Public Access?
9. What is object ownership?
10. What is a storage class?

### Intermediate

11. IAM policy vs bucket policy?
12. How do you secure an S3 bucket?
13. How does versioning work?
14. What is an S3 lifecycle rule?
15. What is SSE-S3?
16. What is SSE-KMS?
17. How do you restrict prefix access?
18. How does S3 replication work?
19. What are CloudTrail S3 data events?
20. How do you troubleshoot AccessDenied?

### Advanced

21. Design an enterprise S3 architecture.
22. How would you implement cross-region disaster recovery?
23. How would you secure an S3 bucket against accidental public exposure?
24. How would you design KMS permissions for S3?
25. How would you manage lifecycle and compliance retention?
26. How would you minimize S3 costs?
27. How would you secure a Terraform S3 backend?
28. How would you implement application-specific prefixes?
29. How would you design immutable backups with Object Lock?
30. Design a secure multi-account S3 platform with Terraform.

---

# ⚡ 50. Cheat Sheet

Create:

```bash
terraform init
terraform fmt
terraform validate
terraform plan
terraform apply
```

Destroy lab resources:

```bash
terraform destroy
```

AWS identity:

```bash
aws sts get-caller-identity
```

S3 CLI:

```bash
aws s3 ls
```

```bash
aws s3 ls s3://BUCKET-NAME
```

Terraform bucket:

```hcl
resource "aws_s3_bucket" "this" {
  bucket = var.bucket_name
}
```

Public access block:

```hcl
resource "aws_s3_bucket_public_access_block" "this" {
  bucket = aws_s3_bucket.this.id

  block_public_acls       = true
  block_public_policy     = true
  ignore_public_acls      = true
  restrict_public_buckets = true
}
```

Versioning:

```hcl
resource "aws_s3_bucket_versioning" "this" {
  bucket = aws_s3_bucket.this.id

  versioning_configuration {
    status = "Enabled"
  }
}
```

---

# 🧠 51. S3 Security Mental Model

Remember:

```text
                  S3 BUCKET
                     │
       ┌─────────────┼─────────────┐
       ▼             ▼             ▼
   Encryption     IAM/Policy    Public Block
       │             │             │
       └─────────────┼─────────────┘
                     ▼
                 OBJECTS
                     │
          ┌──────────┴──────────┐
          ▼                     ▼
      Versioning            Lifecycle
          │                     │
          └──────────┬──────────┘
                     ▼
                  Recovery
```

---

# 🗺️ 52. Terraform Roadmap

```text
01 Terraform Fundamentals       🟢
02 Terraform Installation       🟢
03 Terraform CLI & Workflow     🟢
04 Providers & Authentication   🟢
05 Variables & Outputs          🟢
06 Resources & Data Sources     🟢
07 Expressions & Functions      🟢
08 Meta-Arguments               🟢
09 State & State Management     🟢
10 Remote State & Locking       🟢
11 Terraform Modules            🟢
12 AWS VPC Project              🟢
13 AWS EC2 Project              🟢
14 AWS S3 Project               🟢 ← YOU ARE HERE
15 IAM & Security
16 HCP Terraform
17 Terraform + GitHub Actions
18 Terraform Security & Policy
19 Multi-Cloud Terraform
20 Enterprise Capstone
```

---

<div align="center">

# 🪣 SECURE THE BUCKET • PROTECT THE DATA

### VishwaTech Labs

**Terraform • AWS • Azure • GCP • Docker • GitHub Actions • Kubernetes • Ansible • DevSecOps**

### By Vishwanath Gowda H

⭐ Infrastructure as Code • Cloud Storage • Security • Automation

</div>
