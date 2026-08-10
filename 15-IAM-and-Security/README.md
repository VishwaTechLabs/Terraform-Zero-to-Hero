<div align="center">

# 🔐 Terraform AWS IAM & Security — Complete Masterclass

### 🛡️ Identity • Least Privilege • Roles • Policies • STS • Cross-Account • OIDC | VishwaTech Labs

[![Terraform](https://img.shields.io/badge/Terraform-844FBA?logo=terraform&logoColor=white)](https://developer.hashicorp.com/terraform/docs)
[![AWS IAM](https://img.shields.io/badge/AWS-IAM-FF9900?logo=amazonaws&logoColor=white)](https://aws.amazon.com/iam/)
[![Security](https://img.shields.io/badge/Security-Least%20Privilege-red)](#-security-first)
[![STS](https://img.shields.io/badge/AWS-STS-blue?logo=amazonaws&logoColor=white)](https://docs.aws.amazon.com/STS/latest/APIReference/)
[![Labs](https://img.shields.io/badge/Labs-30+-success)](#-hands-on-labs)

**Build secure AWS identity and access infrastructure with Terraform — IAM users, groups, roles, policies, trust relationships, STS, cross-account access, permission boundaries, OIDC federation and CI/CD security.**

[📘 AWS IAM](https://docs.aws.amazon.com/iam/) •
[📘 Terraform IAM Resources](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/iam_role) •
[📘 AWS Security Best Practices](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html)

</div>

---

# 🎯 Project Goal

Build an enterprise-style identity architecture:

```text
                         AWS ACCOUNT
                              │
                 ┌────────────┴────────────┐
                 │                         │
              HUMAN                     WORKLOAD
                 │                         │
                 ▼                         ▼
          Identity Center / IAM       IAM Role
                 │                         │
                 ▼                         ▼
             Permissions              AWS Services
                 │
                 ▼
           Least Privilege
                 │
        ┌────────┼────────┐
        ▼        ▼        ▼
      S3       EC2       RDS
```

The core principle:

```text
WHO
 ↓
CAN DO WHAT
 ↓
ON WHICH RESOURCE
 ↓
UNDER WHICH CONDITIONS
```

---

# 🧠 1. What Is AWS IAM?

IAM = Identity and Access Management.

IAM controls:

```text
Authentication
Authorization
Permissions
Roles
Policies
Federation
Temporary Credentials
```

Simple model:

```text
Principal
   ↓
Policy Evaluation
   ↓
Permission
   ↓
AWS Resource
```

---

# 👤 2. IAM Principal

A principal is an identity that can make AWS requests.

Examples include:

```text
IAM User
IAM Role
Federated User
AWS Service
Account Principal
```

Important:

```text
Principal ≠ Permission
```

A principal needs an authorization path to perform an action.

---

# 👤 3. IAM User

An IAM user represents a long-lived AWS identity.

Example:

```hcl
resource "aws_iam_user" "developer" {
  name = "developer"

  tags = {
    ManagedBy = "Terraform"
  }
}
```

For human access in modern AWS environments, evaluate AWS IAM Identity Center before creating long-lived IAM users.

---

# 👥 4. IAM Group

Groups help organize users and attach permissions.

```hcl
resource "aws_iam_group" "developers" {
  name = "developers"
}
```

Concept:

```text
Developers Group
      │
      ├── Alice
      ├── Bob
      └── Charlie
```

But groups are for IAM users; IAM roles are generally preferred for workload access.

---

# 🎭 5. IAM Role

An IAM role provides temporary credentials when assumed.

```text
User / AWS Service / Workload
          ↓
       AssumeRole
          ↓
       IAM Role
          ↓
 Temporary Credentials
          ↓
       AWS API
```

Terraform:

```hcl
resource "aws_iam_role" "app" {
  name = "app-role"

  assume_role_policy = jsonencode({
    Version = "2012-10-17"

    Statement = [{
      Effect = "Allow"

      Principal = {
        Service = "ec2.amazonaws.com"
      }

      Action = "sts:AssumeRole"
    }]
  })
}
```

---

# 🤝 6. Trust Policy

A trust policy answers:

> Who is allowed to assume this role?

Example:

```text
EC2 Service
    ↓
Can assume
    ↓
EC2 IAM Role
```

Trust policy:

```hcl
assume_role_policy = jsonencode({
  Version = "2012-10-17"

  Statement = [{
    Effect = "Allow"

    Principal = {
      Service = "ec2.amazonaws.com"
    }

    Action = "sts:AssumeRole"
  }]
})
```

---

# 📜 7. Permission Policy

A permissions policy answers:

> What can the role do after it is assumed?

Example:

```hcl
resource "aws_iam_role_policy" "s3_read" {
  role = aws_iam_role.app.id

  policy = jsonencode({
    Version = "2012-10-17"

    Statement = [{
      Effect = "Allow"

      Action = [
        "s3:GetObject"
      ]

      Resource = "${aws_s3_bucket.app.arn}/app/*"
    }]
  })
}
```

Remember:

```text
Trust Policy
    =
Who can assume?

Permission Policy
    =
What can they do?
```

---

# 🧠 8. IAM Policy Structure

Basic policy:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::example-bucket/*"
    }
  ]
}
```

Core elements:

```text
Version
Statement
Effect
Action
Resource
Condition
Principal
```

`Principal` is primarily used in resource/trust policies and is not a general element of identity-based permission policies.

---

# ⚖️ 9. Allow vs Deny

IAM starts from:

```text
Implicit Deny
```

Then:

```text
Allow
```

can grant access.

But:

```text
Explicit Deny
```

overrides an Allow.

Mental model:

```text
Implicit Deny
     │
     ├── Allow → Access
     │
     └── Explicit Deny → NO ACCESS
```

---

# 🧮 10. IAM Policy Evaluation

A simplified model:

```text
Request
  ↓
Authentication
  ↓
Applicable Policies
  ↓
Explicit Deny?
  ├── YES → DENY
  └── NO
       ↓
    Allow?
     ├── YES → ALLOW
     └── NO → DENY
```

Real authorization can involve multiple policy types and organizational controls.

---

# 🏢 11. Policy Layers

AWS authorization can involve:

```text
Identity-Based Policies
Resource-Based Policies
Permission Boundaries
Session Policies
SCPs
VPC Endpoint Policies
Key Policies
```

For enterprise troubleshooting, inspect the complete policy chain.

---

# 🛡️ 12. Least Privilege

Bad:

```json
{
  "Effect": "Allow",
  "Action": "*",
  "Resource": "*"
}
```

Better:

```json
{
  "Effect": "Allow",
  "Action": [
    "s3:GetObject"
  ],
  "Resource": "arn:aws:s3:::app-bucket/reports/*"
}
```

Ask:

```text
What?
Where?
Why?
Who?
When?
```

---

# 🚨 13. Wildcards

Be careful with:

```text
Action: "*"
Resource: "*"
```

Wildcards may be appropriate in carefully designed policies, but should never be the default.

Prefer:

```text
Specific action
Specific resource
Specific condition
```

---

# 🧩 14. Managed Policies

AWS provides managed policies.

Terraform example:

```hcl
resource "aws_iam_role_policy_attachment" "ssm" {
  role       = aws_iam_role.ec2.name
  policy_arn = "arn:aws:iam::aws:policy/AmazonSSMManagedInstanceCore"
}
```

Managed policies are convenient, but review permissions before attaching them.

---

# 📄 15. Inline Policies

Inline policy:

```hcl
resource "aws_iam_role_policy" "app" {
  name = "app-policy"
  role = aws_iam_role.app.id

  policy = jsonencode({
    Version = "2012-10-17"

    Statement = [{
      Effect   = "Allow"
      Action   = ["s3:GetObject"]
      Resource = "${var.bucket_arn}/app/*"
    }]
  })
}
```

Use deliberately based on your policy lifecycle and reuse requirements.

---

# 📚 16. Policy Documents

Terraform can generate JSON using:

```hcl
data "aws_iam_policy_document" "app" {
  statement {
    effect = "Allow"

    actions = [
      "s3:GetObject"
    ]

    resources = [
      "${var.bucket_arn}/app/*"
    ]
  }
}
```

Then:

```hcl
resource "aws_iam_policy" "app" {
  name   = "app-policy"
  policy = data.aws_iam_policy_document.app.json
}
```

This is often cleaner than manually escaping JSON.

---

# 🧱 17. Custom IAM Policy

```hcl
resource "aws_iam_policy" "app_s3_read" {
  name        = "app-s3-read"
  description = "Read application objects"

  policy = data.aws_iam_policy_document.app.json
}
```

Attach:

```hcl
resource "aws_iam_role_policy_attachment" "app_s3_read" {
  role       = aws_iam_role.app.name
  policy_arn = aws_iam_policy.app_s3_read.arn
}
```

---

# 🔐 18. Permission Boundary

A permission boundary defines the maximum permissions an identity-based policy can grant.

Concept:

```text
Identity Policy
      +
Permission Boundary
      ↓
Maximum Effective Permissions
```

It does not itself grant permissions.

Terraform:

```hcl
resource "aws_iam_policy" "boundary" {
  name = "developer-boundary"

  policy = data.aws_iam_policy_document.boundary.json
}
```

Attach to supported IAM identities using the appropriate Terraform resource/argument.

---

# 🏢 19. Service Control Policy

SCPs are organization-level guardrails in AWS Organizations.

Example concept:

```text
AWS Organization
      │
      ▼
      SCP
      │
      ▼
AWS Account
      │
      ▼
IAM Permissions
```

Important:

```text
SCP does not grant permissions.
```

It limits the maximum permissions available within an account/OU.

---

# 🔄 20. AWS STS

STS = Security Token Service.

It issues temporary credentials.

```text
Principal
   ↓
STS
   ↓
Temporary Credentials
   ↓
AWS API
```

Temporary credentials generally contain:

```text
Access Key ID
Secret Access Key
Session Token
```

---

# 🔁 21. AssumeRole

Concept:

```text
Developer
   ↓
STS AssumeRole
   ↓
Production Role
   ↓
Temporary Credentials
   ↓
AWS
```

CLI example:

```bash
aws sts assume-role \
  --role-arn arn:aws:iam::123456789012:role/DeploymentRole \
  --role-session-name deployment-session
```

Avoid putting the resulting temporary credentials into source code.

---

# 🌍 22. Cross-Account Access

Architecture:

```text
ACCOUNT A
Developer
   │
   │ AssumeRole
   ▼
ACCOUNT B
DeploymentRole
   │
   ▼
AWS Resources
```

Requires:

```text
Trust policy
+
Permissions
+
Correct external controls
```

---

# 🤝 23. Cross-Account Trust

Target account role:

```hcl
resource "aws_iam_role" "cross_account" {
  name = "CrossAccountDeploymentRole"

  assume_role_policy = jsonencode({
    Version = "2012-10-17"

    Statement = [{
      Effect = "Allow"

      Principal = {
        AWS = "arn:aws:iam::111111111111:root"
      }

      Action = "sts:AssumeRole"
    }]
  })
}
```

In production, narrow the trust principal and use appropriate conditions where possible.

---

# 🔒 24. External ID

For certain third-party cross-account access patterns, an external ID can reduce confused-deputy risk.

Concept:

```text
Third Party
    ↓
External ID
    ↓
AssumeRole
    ↓
Target Account
```

Example condition:

```json
"Condition": {
  "StringEquals": {
    "sts:ExternalId": "example-id"
  }
}
```

Use externally supplied values appropriately; do not hardcode shared secrets into public repositories.

---

# 🌐 25. OIDC Federation

OIDC allows external identity providers to obtain temporary AWS credentials through role assumption.

Important for:

```text
GitHub Actions
CI/CD
Kubernetes workloads
External identity platforms
```

Architecture:

```text
GitHub Actions
      │
      ▼
OIDC Token
      │
      ▼
AWS STS
      │
      ▼
IAM Role
      │
      ▼
AWS Resources
```

No long-lived AWS access keys are required for this flow.

---

# 🚀 26. GitHub Actions + AWS OIDC

Concept:

```text
GitHub Repository
       │
       ▼
GitHub Actions
       │
       ▼
OIDC
       │
       ▼
AWS IAM Role
       │
       ▼
Terraform
       │
       ▼
AWS
```

Trust should be restricted to the intended:

```text
Repository
Branch / Environment
Organization
Workflow context
```

Do not trust every GitHub repository.

---

# 🔐 27. GitHub OIDC Trust Policy Concept

Example pattern:

```json
{
  "Effect": "Allow",
  "Principal": {
    "Federated": "arn:aws:iam::123456789012:oidc-provider/token.actions.githubusercontent.com"
  },
  "Action": "sts:AssumeRoleWithWebIdentity",
  "Condition": {
    "StringEquals": {
      "token.actions.githubusercontent.com:aud": "sts.amazonaws.com"
    },
    "StringLike": {
      "token.actions.githubusercontent.com:sub": "repo:ORG/REPO:ref:refs/heads/main"
    }
  }
}
```

Replace placeholders with your real organization/repository values.

---

# 🧑‍💻 28. Terraform Authentication

Preferred approaches depend on environment.

Common patterns:

```text
AWS CLI profile
Environment variables
AWS SSO / IAM Identity Center
AssumeRole
EC2 instance role
ECS task role
GitHub OIDC
Other workload identity federation
```

Avoid:

```text
Hardcoded credentials
Credentials in tfvars
Credentials in Git
```

---

# 🚫 29. Never Do This

```hcl
provider "aws" {
  access_key = "AKIA..."
  secret_key = "SECRET..."
}
```

Never commit credentials.

If credentials are exposed:

```text
Stop use
Rotate/revoke
Investigate
Audit usage
Replace with temporary credentials
```

---

# 🪪 30. IAM Identity Center

For human workforce access, AWS IAM Identity Center is generally preferred over creating many long-lived IAM users.

Concept:

```text
Human
  ↓
Identity Provider / IAM Identity Center
  ↓
Permission Set
  ↓
AWS Account
  ↓
Role Session
```

Benefits:

```text
Centralized access
Temporary credentials
SSO
MFA integration
Account-level access management
```

---

# 🔑 31. MFA

MFA adds another authentication factor.

Concept:

```text
Password
   +
MFA
   ↓
Authentication
```

For human access, use strong MFA according to your organization's security policy.

---

# 🔍 32. IAM Access Analyzer

Use IAM Access Analyzer to identify:

```text
External access
Unintended resource sharing
Policy issues
```

Architecture:

```text
IAM Policies
     ↓
Analyzer
     ↓
Findings
     ↓
Security Review
```

---

# 📊 33. IAM Policy Simulator

The IAM Policy Simulator can help answer:

```text
Can this principal perform this action?
```

Useful for troubleshooting:

```text
AccessDenied
Unexpected permission
Policy conflicts
```

---

# 🧠 34. Explicit Deny Debugging

If a request fails:

```text
Check identity policy
Check resource policy
Check SCP
Check permission boundary
Check session policy
Check KMS key policy
Check VPC endpoint policy
Check conditions
```

Look specifically for:

```text
Explicit Deny
```

---

# 🧩 35. IAM + KMS

KMS authorization can involve:

```text
IAM Policy
+
KMS Key Policy
+
Grants
+
Other conditions
```

For encrypted S3:

```text
Application
    ↓
S3
    ↓
KMS
    ↓
Encrypted Object
```

The application may need both S3 and KMS permissions.

---

# 🪣 36. IAM + S3 Example

Application needs:

```text
s3:GetObject
```

on:

```text
arn:aws:s3:::vishwatech-prod-data/app/*
```

Terraform:

```hcl
data "aws_iam_policy_document" "app_s3" {
  statement {
    effect = "Allow"

    actions = [
      "s3:GetObject"
    ]

    resources = [
      "${var.bucket_arn}/app/*"
    ]
  }
}
```

Use the bucket ARN separately for bucket-level actions such as `s3:ListBucket`.

---

# 🖥️ 37. EC2 IAM Role

Architecture:

```text
EC2
 │
 ▼
Instance Profile
 │
 ▼
IAM Role
 │
 ▼
Temporary Credentials
 │
 ▼
S3 / SSM / CloudWatch
```

Never store AWS access keys on the instance when an instance role can be used.

---

# ☸️ 38. Kubernetes Workload Identity

Modern Kubernetes deployments can use workload identity mechanisms.

AWS EKS commonly uses:

```text
EKS Pod
   ↓
IAM Role
   ↓
Temporary Credentials
   ↓
AWS Service
```

Depending on the architecture, mechanisms such as EKS Pod Identity or IRSA can be used.

This becomes especially important in the later Kubernetes module.

---

# 🧱 39. IAM Module Architecture

Recommended:

```text
modules/
└── iam-role/
    ├── main.tf
    ├── variables.tf
    ├── outputs.tf
    └── versions.tf
```

Caller:

```hcl
module "app_role" {
  source = "./modules/iam-role"

  role_name = "application-role"
}
```

---

# 📁 40. Project Folder Structure

```text
15-Terraform-IAM-and-Security/
│
├── README.md
│
├── terraform/
│   ├── versions.tf
│   ├── provider.tf
│   ├── variables.tf
│   ├── locals.tf
│   ├── users.tf
│   ├── groups.tf
│   ├── roles.tf
│   ├── policies.tf
│   ├── trust_policies.tf
│   ├── permission_boundaries.tf
│   ├── oidc_github.tf
│   ├── cross_account.tf
│   ├── outputs.tf
│   ├── terraform.tfvars.example
│   └── .gitignore
│
├── modules/
│   ├── iam-role/
│   ├── iam-policy/
│   └── github-oidc-role/
│
└── examples/
    ├── ec2-role/
    ├── cross-account/
    └── github-actions-oidc/
```

---

# ⚙️ 41. Terraform Provider

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

# 👤 42. IAM User Example

```hcl
resource "aws_iam_user" "developer" {
  name = "developer"

  tags = {
    ManagedBy = "Terraform"
  }
}
```

For workforce identities, prefer IAM Identity Center when it fits your organization's model.

---

# 👥 43. IAM Group Example

```hcl
resource "aws_iam_group" "developers" {
  name = "developers"
}
```

Attach a policy:

```hcl
resource "aws_iam_group_policy_attachment" "developer_readonly" {
  group      = aws_iam_group.developers.name
  policy_arn = "arn:aws:iam::aws:policy/ReadOnlyAccess"
}
```

Review managed policy scope before using it in production.

---

# 🎭 44. IAM Role Example

```hcl
resource "aws_iam_role" "app" {
  name = "application-role"

  assume_role_policy = jsonencode({
    Version = "2012-10-17"

    Statement = [{
      Effect = "Allow"

      Principal = {
        Service = "ec2.amazonaws.com"
      }

      Action = "sts:AssumeRole"
    }]
  })
}
```

---

# 📜 45. Custom Policy Example

```hcl
data "aws_iam_policy_document" "app" {
  statement {
    sid    = "ReadApplicationObjects"
    effect = "Allow"

    actions = [
      "s3:GetObject"
    ]

    resources = [
      "arn:aws:s3:::vishwatech-prod-data/app/*"
    ]
  }
}
```

---

# 🔗 46. Attach Policy

```hcl
resource "aws_iam_policy" "app" {
  name   = "application-s3-read"
  policy = data.aws_iam_policy_document.app.json
}
```

```hcl
resource "aws_iam_role_policy_attachment" "app" {
  role       = aws_iam_role.app.name
  policy_arn = aws_iam_policy.app.arn
}
```

---

# 🌐 47. GitHub OIDC Provider

The AWS account needs an OIDC identity provider configuration for GitHub Actions.

Terraform pattern:

```hcl
resource "aws_iam_openid_connect_provider" "github" {
  url = "https://token.actions.githubusercontent.com"

  client_id_list = [
    "sts.amazonaws.com"
  ]

  thumbprint_list = [
    var.github_oidc_thumbprint
  ]
}
```

Use the current AWS/GitHub guidance for provider configuration and certificate/thumbprint handling appropriate to your provider version and deployment model.

---

# 🚀 48. GitHub Actions Role

```hcl
resource "aws_iam_role" "github_actions" {
  name = "github-actions-deploy"

  assume_role_policy = data.aws_iam_policy_document.github_trust.json
}
```

Attach only the deployment permissions required by the workflow.

---

# 🔒 49. GitHub OIDC Security Rules

```text
☑ Use OIDC
☑ No long-lived AWS keys
☑ Restrict repository
☑ Restrict branch/environment
☑ Limit permissions
☑ Review pull-request permissions
☑ Protect production environments
☑ Use Terraform plan/apply separation
```

---

# 🏢 50. Enterprise IAM Architecture

```text
                    Organization
                         │
              ┌──────────┴──────────┐
              ▼                     ▼
          Identity              AWS Accounts
          Center                     │
              │              ┌───────┼───────┐
              ▼              ▼       ▼       ▼
       Permission Sets      Dev    Stage    Prod
                               │
                         IAM Roles
                               │
                  ┌────────────┼────────────┐
                  ▼            ▼            ▼
                 EC2          S3           EKS
```

---

# 🔐 51. Security-First Design

```text
IDENTITY
   ↓
MFA / SSO
   ↓
TEMPORARY CREDENTIALS
   ↓
LEAST PRIVILEGE
   ↓
RESOURCE-LEVEL ACCESS
   ↓
CONDITIONS
   ↓
AUDIT
```

---

# 🚨 52. Common Mistakes

### Mistake 1

```text
Action = "*"
Resource = "*"
```

Fix:

```text
Specific actions
Specific resources
```

---

### Mistake 2

```text
Long-lived access keys
```

Fix:

```text
Roles
SSO
OIDC
Temporary credentials
```

---

### Mistake 3

```text
Trust all principals
```

Fix:

```text
Narrow trust policy
Conditions
External ID where appropriate
```

---

### Mistake 4

```text
IAM policy correct
but AccessDenied
```

Check:

```text
SCP
Boundary
Resource policy
KMS
Endpoint policy
Explicit deny
```

---

# 🔍 53. Troubleshooting

## AccessDenied

Run:

```bash
aws sts get-caller-identity
```

Then inspect:

```text
Identity
Action
Resource
Policy
Condition
Explicit Deny
```

---

## AssumeRole Failed

Check:

```text
Trust policy
Principal
Action sts:AssumeRole
External ID
MFA conditions
Source identity
SCPs
```

---

## GitHub OIDC Failed

Check:

```text
OIDC provider
Audience
Subject claim
Repository
Branch/environment
IAM role
GitHub workflow permissions
```

Workflow needs:

```yaml
permissions:
  id-token: write
  contents: read
```

---

## EC2 Cannot Access S3

Check:

```text
Instance profile
IAM role
S3 action
Bucket ARN
Object ARN
Bucket policy
KMS
VPC endpoint
SCP
```

---

# 🧪 54. Hands-On Labs

## Lab 01 — Create IAM User

Create a test IAM user.

## Lab 02 — Create IAM Group

Build a developers group.

## Lab 03 — Attach ReadOnly

Test managed permissions.

## Lab 04 — Create IAM Role

Create an EC2 role.

## Lab 05 — Trust Policy

Understand role assumption.

## Lab 06 — Custom Policy

Allow specific S3 actions.

## Lab 07 — Least Privilege

Remove unnecessary permissions.

## Lab 08 — Policy Document

Generate JSON using Terraform.

## Lab 09 — Permission Boundary

Create a boundary.

## Lab 10 — STS

Practice temporary credentials.

## Lab 11 — AssumeRole

Assume a deployment role.

## Lab 12 — Cross Account

Deploy from Account A to Account B.

## Lab 13 — External ID

Test a third-party trust pattern.

## Lab 14 — EC2 Role

Access S3 without access keys.

## Lab 15 — SSM Role

Manage EC2 using Systems Manager.

## Lab 16 — KMS Permissions

Access encrypted S3 objects.

## Lab 17 — S3 Prefix Access

Restrict application to one prefix.

## Lab 18 — IAM Access Analyzer

Investigate findings.

## Lab 19 — Policy Simulator

Debug authorization.

## Lab 20 — SCP Concept

Test organization guardrails in an AWS Organizations lab environment.

## Lab 21 — GitHub OIDC

Configure GitHub-to-AWS federation.

## Lab 22 — GitHub Terraform Role

Run Terraform without AWS keys.

## Lab 23 — Environment Trust

Restrict production deployment.

## Lab 24 — Module

Build reusable IAM role module.

## Lab 25 — Multiple Roles

Create:

```text
Developer
ReadOnly
Deployment
Application
```

roles.

## Lab 26 — Role Chaining

Practice controlled role assumption.

## Lab 27 — Security Review

Audit an intentionally over-permissive policy.

## Lab 28 — AccessDenied Challenge

Find the missing permission.

## Lab 29 — IAM CI/CD

Implement plan/apply with OIDC.

## Lab 30 — Enterprise Challenge

Build:

```text
GitHub
  ↓ OIDC
Deployment Role
  ↓
Terraform
  ↓
AWS
  ├── VPC
  ├── EC2
  ├── S3
  └── IAM
```

with:

```text
Least Privilege
Trust Restrictions
Remote State
Audit
Environment Separation
```

---

# 🏆 55. Production Checklist

```text
IDENTITY
☑ Prefer SSO/Identity Center for workforce access
☑ Avoid unnecessary IAM users
☑ MFA
☑ Temporary credentials

ROLES
☑ Narrow trust policies
☑ Least privilege
☑ External ID where required
☑ Permission boundaries where useful

POLICIES
☑ Specific actions
☑ Specific resources
☑ Conditions
☑ Avoid unnecessary wildcards

CI/CD
☑ OIDC
☑ No long-lived keys
☑ Environment protection
☑ Restricted trust

OPERATIONS
☑ Access Analyzer
☑ CloudTrail
☑ Policy review
☑ Credential rotation/removal
☑ Incident response process
```

---

# 🚫 56. Never Commit These

```text
❌ AWS Access Keys
❌ AWS Secret Keys
❌ Session Tokens
❌ Private Keys
❌ Production credentials
❌ Sensitive tfvars
❌ KMS secrets
❌ CI/CD secrets
```

If credentials are exposed:

```text
1. Revoke/rotate immediately
2. Investigate usage
3. Review CloudTrail
4. Remove from repository/history
5. Replace with short-lived identity
```

---

# 🎓 57. Interview Questions

### Beginner

1. What is IAM?
2. What is an IAM user?
3. What is an IAM role?
4. What is an IAM policy?
5. What is a trust policy?
6. What is an instance profile?
7. What is least privilege?
8. What is STS?
9. What is MFA?
10. What is an IAM group?

### Intermediate

11. Trust policy vs permission policy?
12. Identity policy vs resource policy?
13. What is an explicit deny?
14. What is a permission boundary?
15. What is an SCP?
16. What is AssumeRole?
17. What is cross-account access?
18. What is OIDC?
19. Why use GitHub OIDC?
20. How do you troubleshoot AccessDenied?

### Advanced

21. Design enterprise IAM for multiple AWS accounts.
22. How would you remove long-lived credentials?
23. How would you secure GitHub Actions?
24. How would you design cross-account deployment?
25. How would you troubleshoot a policy that appears correct?
26. Explain the complete IAM evaluation flow.
27. How do SCPs and permission boundaries differ?
28. How would you design an EC2 role for S3 access?
29. How would you secure KMS + S3 access?
30. Design a zero-long-lived-credential Terraform platform.

---

# ⚡ 58. Cheat Sheet

Identity:

```bash
aws sts get-caller-identity
```

Assume role:

```bash
aws sts assume-role \
  --role-arn ROLE_ARN \
  --role-session-name session
```

Terraform role:

```hcl
resource "aws_iam_role" "app" {
  name = "app-role"

  assume_role_policy = jsonencode({
    Version = "2012-10-17"

    Statement = [{
      Effect = "Allow"

      Principal = {
        Service = "ec2.amazonaws.com"
      }

      Action = "sts:AssumeRole"
    }]
  })
}
```

Least privilege:

```text
Specific Principal
Specific Action
Specific Resource
Specific Condition
```

GitHub OIDC:

```text
GitHub
 ↓
OIDC
 ↓
STS
 ↓
IAM Role
 ↓
AWS
```

---

# 🧠 59. IAM Mental Model

Remember this forever:

```text
                 WHO?
                  │
                  ▼
              PRINCIPAL
                  │
                  ▼
             TRUST POLICY
                  │
             AssumeRole
                  │
                  ▼
            TEMP CREDENTIALS
                  │
                  ▼
          PERMISSION POLICIES
                  │
                  ▼
        ┌─────────────────────┐
        │ Action + Resource   │
        │ + Conditions        │
        └──────────┬──────────┘
                   ▼
             AWS RESOURCE
```

And:

```text
Explicit Deny
      ↓
OVERRIDES ALLOW
```

---

# 🗺️ 60. Terraform Roadmap

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
14 AWS S3 Project               🟢
15 IAM & Security               🟢 ← YOU ARE HERE
16 HCP Terraform
17 Terraform + GitHub Actions
18 Terraform Security & Policy
19 Multi-Cloud Terraform
20 Enterprise Capstone
```

---

<div align="center">

# 🔐 IDENTITY IS THE NEW PERIMETER

### VishwaTech Labs

**Terraform • AWS • Azure • GCP • IAM • Docker • GitHub Actions • Kubernetes • Ansible • DevSecOps**

### By Vishwanath Gowda H

⭐ Identity • Least Privilege • Cloud Security • Infrastructure as Code

</div>
