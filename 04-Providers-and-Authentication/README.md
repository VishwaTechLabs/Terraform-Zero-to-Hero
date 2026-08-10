<div align="center">

# 🔐 Terraform Providers & Authentication — Complete Masterclass

### ☁️ AWS • Azure • GCP • CLI Profiles • SSO • Roles • AssumeRole • GitHub OIDC | VishwaTech Labs

[![Terraform](https://img.shields.io/badge/Terraform-844FBA?logo=terraform&logoColor=white)](https://developer.hashicorp.com/terraform/docs)
[![AWS](https://img.shields.io/badge/AWS-FF9900?logo=amazonaws&logoColor=white)](https://aws.amazon.com/)
[![Azure](https://img.shields.io/badge/Azure-0078D4?logo=microsoftazure&logoColor=white)](https://azure.microsoft.com/)
[![GCP](https://img.shields.io/badge/GCP-4285F4?logo=googlecloud&logoColor=white)](https://cloud.google.com/)
[![Security](https://img.shields.io/badge/Security-Least%20Privilege-red)](#-authentication-security)
[![OIDC](https://img.shields.io/badge/GitHub-OIDC-blueviolet)](#-github-actions--aws-oidc)

**Understand how Terraform discovers providers and how secure credentials flow from your workstation or CI/CD system to cloud APIs.**

[📘 Provider Configuration](https://developer.hashicorp.com/terraform/language/providers/configuration) •
[🧩 Terraform Registry](https://registry.terraform.io/) •
[🔐 AWS Provider](https://registry.terraform.io/providers/hashicorp/aws/latest) •
[🔐 AzureRM Provider](https://registry.terraform.io/providers/hashicorp/azurerm/latest) •
[🔐 Google Provider](https://registry.terraform.io/providers/hashicorp/google/latest)

</div>

---

# 🎯 What You Will Learn

This module answers two fundamental questions:

```text
1. Which external API should Terraform talk to?
2. How does Terraform securely authenticate to that API?
```

You will learn:

```text
Providers
Provider Sources
Provider Versions
Provider Configuration
Provider Aliases
AWS Authentication
AWS CLI Profiles
AWS IAM Identity Center
Environment Variables
IAM Roles
AssumeRole
GitHub OIDC
Azure Authentication Concepts
GCP Authentication Concepts
Credential Chains
Least Privilege
Multi-Account Terraform
Multi-Region Terraform
Provider Security
Hands-on Labs
Troubleshooting
Interview Questions
```

---

# 🧠 1. Provider Mental Model

Terraform itself does not know how to create an EC2 instance, Azure VM, or Google Compute Engine instance.

A provider connects Terraform to an external API.

```text
                 Terraform
                     │
        ┌────────────┼────────────┐
        ▼            ▼            ▼
     AWS Provider AzureRM       Google
        │            │            │
        ▼            ▼            ▼
      AWS API      Azure API    GCP API
        │            │            │
        ▼            ▼            ▼
    Resources      Resources    Resources
```

---

# 🔌 2. What Is a Provider?

A provider is a plugin that implements resources and data sources for an external platform or service.

Examples:

```text
hashicorp/aws
hashicorp/azurerm
hashicorp/google
hashicorp/kubernetes
hashicorp/docker
```

Provider:

```text
Terraform
   ↓
Provider Plugin
   ↓
External API
```

---

# 🆚 3. Provider vs Resource

This distinction is extremely important.

### Provider

Defines **how Terraform communicates** with a platform.

```hcl
provider "aws" {
  region = "us-east-1"
}
```

### Resource

Defines **what Terraform manages**.

```hcl
resource "aws_s3_bucket" "demo" {
  bucket = "replace-with-a-unique-name"
}
```

Think:

```text
Provider = Connection/Integration Layer
Resource = Infrastructure Object
```

---

# 📦 4. Provider Source Address

Modern Terraform provider requirements use a source address.

Example:

```hcl
terraform {
  required_providers {
    aws = {
      source = "hashicorp/aws"
    }
  }
}
```

Source:

```text
hashicorp/aws
```

Breakdown:

```text
hashicorp
    ↓
Namespace

aws
    ↓
Provider
```

---

# 🏷️ 5. Provider Version Constraints

Example:

```hcl
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 6.0"
    }
  }
}
```

Version constraints help control upgrades.

Common operators include:

```text
=
!=
>
>=
<
<=
~
```

Use a version strategy appropriate for your organization's release and upgrade policy.

---

# 🔒 6. Provider Lock File

After:

```bash
terraform init
```

Terraform can create:

```text
.terraform.lock.hcl
```

This records selected provider versions and package checksums.

Recommended:

```text
.terraform/
        ❌ Do not commit

.terraform.lock.hcl
        ✅ Normally commit
```

This improves reproducibility across developer machines and CI.

---

# 🧩 7. Provider Configuration

Example:

```hcl
provider "aws" {
  region = "us-east-1"
}
```

The provider block configures the provider.

Authentication is generally better handled through the provider's supported credential chain rather than hard-coded secrets.

---

# ⚠️ 8. Never Hard-Code Credentials

Avoid:

```hcl
provider "aws" {
  access_key = "AKIA..."
  secret_key = "..."
}
```

Why?

```text
Git history
Pull requests
Logs
Backups
Developer machines
CI artifacts
```

can expose secrets.

Instead:

```text
AWS CLI Profile
IAM Identity Center
Environment
IAM Role
OIDC
```

---

# 🔐 9. AWS Authentication Architecture

```text
Terraform
    │
    ▼
AWS Provider
    │
    ▼
AWS SDK Credential Chain
    │
    ├── Environment
    ├── Shared Config / Profile
    ├── IAM Identity Center
    ├── Role Credentials
    └── Other supported sources
    │
    ▼
AWS API
```

Exact credential precedence can vary by SDK/provider behavior and configuration, so use the current AWS provider documentation when troubleshooting a specific environment.

---

# 🧑‍💻 10. AWS CLI Profile Authentication

Create a profile using your organization's approved authentication method.

For traditional configuration:

```bash
aws configure --profile dev
```

Verify:

```bash
aws sts get-caller-identity --profile dev
```

Terraform:

```hcl
provider "aws" {
  region  = "us-east-1"
  profile = "dev"
}
```

---

# 🌐 11. AWS IAM Identity Center / SSO

For organizations using IAM Identity Center:

```bash
aws configure sso
```

Login:

```bash
aws sso login --profile dev
```

Verify:

```bash
aws sts get-caller-identity --profile dev
```

Terraform can use the profile:

```hcl
provider "aws" {
  region  = "us-east-1"
  profile = "dev"
}
```

Benefits:

```text
Short-lived sessions
Centralized identity
Centralized access management
Reduced long-lived credentials
```

---

# 🌱 12. Environment Variables

AWS SDK-based tools can obtain credentials from environment variables.

Examples:

```bash
export AWS_PROFILE=dev
```

PowerShell:

```powershell
$env:AWS_PROFILE="dev"
```

Temporary credentials can also be supplied through environment variables.

Never put these values into:

```text
.tf files
Git
README files
Dockerfiles
GitHub issues
Logs
```

---

# 🧑‍💼 13. IAM Roles

For AWS compute environments:

```text
EC2
ECS
EKS
Lambda
```

prefer workload identity / IAM roles rather than static access keys.

Concept:

```text
Application
    ↓
AWS Runtime Identity
    ↓
IAM Role
    ↓
Temporary Credentials
    ↓
AWS API
```

---

# 🔁 14. AssumeRole

Terraform can use an IAM role different from the initial identity.

Concept:

```text
Developer / CI Identity
          ↓
      AssumeRole
          ↓
     Target IAM Role
          ↓
     Temporary Credentials
          ↓
       AWS Account
```

Example provider configuration:

```hcl
provider "aws" {
  region = "us-east-1"

  assume_role {
    role_arn = "arn:aws:iam::123456789012:role/TerraformRole"
  }
}
```

Use placeholders and replace them with your approved role.

---

# 🏢 15. Multi-Account AWS

A common enterprise model:

```text
AWS Organization
│
├── Security Account
├── Logging Account
├── Shared Services
├── Development
├── Staging
└── Production
```

Terraform can authenticate to different accounts through separate roles.

Concept:

```text
GitHub / Developer
       │
       ├── Dev Role
       ├── Staging Role
       └── Prod Role
```

Use strict trust and permissions for each environment.

---

# 🌍 16. Multi-Region AWS

Example:

```hcl
provider "aws" {
  region = "us-east-1"
}

provider "aws" {
  alias  = "europe"
  region = "eu-north-1"
}
```

Then a resource can select a provider configuration:

```hcl
resource "aws_s3_bucket" "europe" {
  provider = aws.europe
  bucket   = "replace-with-unique-name"
}
```

---

# 🏷️ 17. Provider Alias

An alias creates an additional provider configuration.

```hcl
provider "aws" {
  region = "us-east-1"
}

provider "aws" {
  alias  = "eu"
  region = "eu-north-1"
}
```

Default:

```text
aws
```

Aliased:

```text
aws.eu
```

---

# 🌎 18. Azure Provider

Azure Resource Manager provider:

```hcl
terraform {
  required_providers {
    azurerm = {
      source = "hashicorp/azurerm"
    }
  }
}

provider "azurerm" {
  features {}
}
```

Authentication should use Azure's supported identity mechanisms rather than hard-coded client secrets.

Official provider:

[AzureRM Provider](https://registry.terraform.io/providers/hashicorp/azurerm/latest)

---

# ☁️ 19. GCP Provider

Google provider:

```hcl
terraform {
  required_providers {
    google = {
      source = "hashicorp/google"
    }
  }
}

provider "google" {
  project = "YOUR_PROJECT_ID"
  region  = "us-central1"
}
```

Authentication should use Google Cloud's supported application/default credential mechanisms or workload identity patterns appropriate to the environment.

Official:

[Google Provider](https://registry.terraform.io/providers/hashicorp/google/latest)

---

# 🧩 20. Kubernetes Provider

Terraform can also communicate with Kubernetes:

```hcl
terraform {
  required_providers {
    kubernetes = {
      source = "hashicorp/kubernetes"
    }
  }
}
```

Architecture:

```text
Terraform
   ↓
Kubernetes Provider
   ↓
Kubernetes API Server
   ↓
Cluster
```

This becomes important later when Terraform and Kubernetes are combined.

---

# 🐳 21. Docker Provider

Terraform can also interact with Docker through a provider:

```text
Terraform
   ↓
Docker Provider
   ↓
Docker API
   ↓
Containers / Images / Networks
```

This is useful for:

```text
Local labs
Testing
Development environments
```

It should not be confused with Docker's own CLI.

---

# 🔐 22. GitHub Provider

Terraform can manage GitHub resources through a provider.

Concept:

```text
Terraform
   ↓
GitHub Provider
   ↓
GitHub API
   ↓
Repository / Teams / Variables / Rules
```

This becomes useful for Infrastructure-as-Code-driven repository administration.

---

# 🔥 23. GitHub Actions + AWS OIDC

One of the most important enterprise patterns:

```text
GitHub Repository
       ↓
GitHub Actions
       ↓
OIDC Token
       ↓
AWS IAM Trust Policy
       ↓
IAM Role
       ↓
Temporary Credentials
       ↓
Terraform
       ↓
AWS
```

This avoids storing long-lived AWS access keys in GitHub.

---

# 🪪 24. OIDC Trust Concept

The AWS role trust policy should restrict:

```text
Repository
Organization
Branch
Environment
Audience
Subject claims
```

Concept:

```text
GitHub OIDC Token
        ↓
AWS Trust Policy
        ↓
Is this repository allowed?
        ↓
Is this branch/environment allowed?
        ↓
YES → Temporary Role Credentials
NO  → Access Denied
```

Do not create an overly broad trust policy.

---

# 🧩 25. GitHub Actions Terraform Example

```yaml
name: Terraform

on:
  pull_request:
  push:
    branches:
      - main

permissions:
  id-token: write
  contents: read

jobs:
  terraform:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v4
        with:
          role-to-assume: ${{ secrets.AWS_ROLE_ARN }}
          aws-region: us-east-1

      - name: Setup Terraform
        uses: hashicorp/setup-terraform@v3

      - name: Terraform Init
        run: terraform init

      - name: Terraform Format Check
        run: terraform fmt -check -recursive

      - name: Terraform Validate
        run: terraform validate

      - name: Terraform Plan
        run: terraform plan
```

For production, pin/action-governance should follow your organization's supply-chain policy.

---

# 🏗️ 26. Authentication Comparison

| Method | Best Use | Long-Lived Secret? | Security |
|---|---|---:|---|
| Hard-coded keys | ❌ Never | Yes | 🔴 |
| Environment credentials | Temporary/local automation | Depends | 🟡 |
| AWS CLI Profile | Local development | Depends | 🟢 |
| IAM Identity Center | Enterprise developers | Usually no | 🟢🟢 |
| EC2/ECS/EKS Role | AWS workloads | No | 🟢🟢 |
| AssumeRole | Cross-account | No | 🟢🟢 |
| GitHub OIDC | CI/CD | No | 🟢🟢🟢 |

---

# 🧠 27. Authentication vs Authorization

Very important:

```text
Authentication
       ↓
Who are you?
```

```text
Authorization
       ↓
What are you allowed to do?
```

Example:

```text
GitHub OIDC
    ↓
Authentication / Identity Federation
    ↓
IAM Role
    ↓
Authorization through IAM Policy
```

---

# 🔑 28. Least Privilege

Do not give Terraform:

```text
AdministratorAccess
```

just because it is convenient.

Instead:

```text
Terraform
   ↓
Required Actions Only
   ↓
Required Resources Only
```

Examples:

```text
Development Role
Production Role
Read-only Plan Role
Apply Role
```

depending on your architecture.

---

# 🧱 29. Plan vs Apply Credentials

A mature environment may separate:

```text
Pull Request
    ↓
Plan Role
    ↓
Read / Inspect
```

and:

```text
Approved Main
    ↓
Apply Role
    ↓
Write Infrastructure
```

This reduces unnecessary write access.

---

# 🔐 30. Credential Leakage

Never expose credentials through:

```text
terraform.tfvars
provider.tf
GitHub repository
GitHub logs
Shell history
Terraform debug logs
Docker images
Public artifacts
```

Remember:

```text
State may also contain sensitive values.
```

---

# 🔎 31. Verify AWS Identity

Always use:

```bash
aws sts get-caller-identity
```

before troubleshooting Terraform.

This answers:

```text
Which account?
Which identity?
Which role?
```

---

# 🧪 32. Authentication Troubleshooting

Check:

```bash
aws configure list
```

Profiles:

```bash
aws configure list-profiles
```

Current identity:

```bash
aws sts get-caller-identity
```

Specific profile:

```bash
aws sts get-caller-identity --profile dev
```

---

# 🚨 33. Common Authentication Errors

## `NoCredentialsError`

Possible causes:

```text
No credentials found
Wrong profile
Expired SSO session
Missing environment
Incorrect role configuration
```

---

## `AccessDenied`

Possible causes:

```text
Identity authenticated
BUT
IAM policy does not allow action
```

Remember:

```text
Authentication ≠ Authorization
```

---

## `ExpiredToken`

Usually indicates:

```text
Temporary credentials/session expired
```

For SSO:

```bash
aws sso login --profile dev
```

---

## Wrong AWS Account

Run:

```bash
aws sts get-caller-identity
```

Do not run production Terraform until you verify the target account.

---

# 🏢 34. Enterprise Authentication Architecture

```text
                         Identity Provider
                               │
                    ┌──────────┴──────────┐
                    ▼                     ▼
                Developer             GitHub
                    │                     │
                    ▼                     ▼
              IAM Identity           OIDC Token
                 Center                  │
                    │                     ▼
                    │                 IAM Trust
                    │                     │
                    └──────────┬──────────┘
                               ▼
                           IAM Role
                               │
                               ▼
                           Terraform
                               │
                  ┌────────────┼────────────┐
                  ▼            ▼            ▼
                 Dev         Stage         Prod
```

---

# 🌍 35. Multi-Cloud Authentication Concept

```text
                   Terraform
                       │
        ┌──────────────┼──────────────┐
        ▼              ▼              ▼
       AWS            Azure           GCP
        │              │              │
       IAM         Entra ID       IAM / Workload
        │              │          Identity
        ▼              ▼              ▼
      Cloud          Cloud           Cloud
```

Each cloud has different identity models.

Do not assume:

```text
AWS authentication
=
Azure authentication
=
GCP authentication
```

---

# 🧪 36. Hands-On Labs

## Lab 01 — Provider

Create an AWS provider.

## Lab 02 — Provider Version

Add a version constraint.

## Lab 03 — Lock File

Run:

```bash
terraform init
```

and inspect:

```text
.terraform.lock.hcl
```

## Lab 04 — AWS Profile

Create a test profile and authenticate.

## Lab 05 — Identity Verification

Run:

```bash
aws sts get-caller-identity
```

## Lab 06 — Terraform Profile

Configure Terraform to use the profile.

## Lab 07 — IAM Identity Center

Configure a test SSO profile.

## Lab 08 — Environment

Use an approved temporary credential environment.

## Lab 09 — AssumeRole

Assume a test cross-account role.

## Lab 10 — Provider Alias

Create:

```text
us-east-1
eu-north-1
```

providers.

## Lab 11 — Multi-Region Resource

Deploy test resources using an aliased provider.

## Lab 12 — Azure Provider

Configure AzureRM provider.

## Lab 13 — GCP Provider

Configure Google provider.

## Lab 14 — Kubernetes Provider

Connect Terraform to a test cluster.

## Lab 15 — Docker Provider

Connect Terraform to local Docker.

## Lab 16 — GitHub Provider

Configure a test GitHub provider.

## Lab 17 — Least Privilege

Create a minimal test role for Terraform.

## Lab 18 — GitHub OIDC

Configure GitHub → AWS IAM role federation.

## Lab 19 — OIDC Restriction

Restrict the role to a specific repository/branch/environment.

## Lab 20 — Plan Role

Design a read-oriented Terraform plan role.

## Lab 21 — Apply Role

Design a separate write-capable role.

## Lab 22 — Wrong Account

Deliberately select the wrong profile and troubleshoot.

## Lab 23 — Expired SSO

Allow an SSO session to expire and troubleshoot.

## Lab 24 — AccessDenied

Create a controlled missing-permission scenario.

## Lab 25 — Multi-Account

Design:

```text
Dev
Stage
Prod
```

role separation.

## Lab 26 — Multi-Cloud

Create a basic configuration using AWS + Azure + GCP providers.

## Lab 27 — CI Authentication

Run Terraform plan from GitHub Actions using OIDC.

## Lab 28 — Credential Leak Review

Scan a sample repository for exposed credentials.

## Lab 29 — Provider Upgrade

Upgrade a provider in a controlled branch and inspect the lock-file changes.

## Lab 30 — Enterprise Authentication Design

Design:

```text
Developer
   ↓
Identity Center

GitHub
   ↓
OIDC

Terraform
   ↓
Role Assumption

Cloud Accounts
   ↓
Dev / Stage / Prod
```

---

# 🛡️ 37. Provider Security Checklist

```text
☑ Use official/trusted provider sources
☑ Pin provider versions appropriately
☑ Commit .terraform.lock.hcl
☑ Never hard-code credentials
☑ Prefer short-lived credentials
☑ Use least privilege
☑ Use IAM roles for AWS workloads
☑ Use OIDC for GitHub Actions
☑ Restrict OIDC trust policies
☑ Separate environments
☑ Verify AWS account before apply
☑ Protect production credentials
☑ Review provider upgrades
☑ Scan code for secrets
```

---

# 🧪 38. Provider Upgrade Workflow

```text
Current Version
      ↓
Review Release Notes
      ↓
Create Branch
      ↓
Update Constraint
      ↓
terraform init -upgrade
      ↓
Review Lock File
      ↓
terraform validate
      ↓
terraform plan
      ↓
Tests
      ↓
Pull Request
      ↓
Approval
```

Do not upgrade providers blindly in production.

---

# 📚 39. Terraform Registry

Official Registry:

[Terraform Registry](https://registry.terraform.io/)

Before using a provider:

```text
☑ Correct namespace
☑ Correct source
☑ Version
☑ Documentation
☑ Maintainer
☑ Release history
☑ Security considerations
```

Example:

```hcl
required_providers {
  aws = {
    source = "hashicorp/aws"
  }
}
```

---

# 🎓 40. Interview Questions

### Beginner

1. What is a Terraform provider?
2. What is the difference between provider and resource?
3. What is a provider source address?
4. What is `.terraform.lock.hcl`?
5. Why do we specify provider versions?
6. What is AWS provider?
7. What is AzureRM provider?
8. What is Google provider?
9. What is provider alias?
10. Why should credentials not be hard-coded?

### Intermediate

11. How does Terraform authenticate to AWS?
12. What is an AWS CLI profile?
13. What is IAM Identity Center?
14. What is an IAM role?
15. What is AssumeRole?
16. What is the difference between authentication and authorization?
17. What is least privilege?
18. How do you configure multiple AWS regions?
19. How do you configure multiple AWS accounts?
20. What is GitHub OIDC?

### Advanced

21. Design GitHub Actions → AWS → Terraform authentication.
22. Why is OIDC safer than long-lived access keys?
23. How would you restrict GitHub OIDC trust?
24. How would you separate Terraform plan and apply permissions?
25. How would you design Terraform authentication for Dev/Stage/Prod?
26. How would you manage provider upgrades?
27. How would you troubleshoot `AccessDenied`?
28. How would you troubleshoot expired SSO credentials?
29. How would you secure multi-cloud Terraform authentication?
30. Design an enterprise Terraform provider/authentication architecture.

---

# ⚡ 41. Cheat Sheet

```bash
# Terraform
terraform init
terraform providers
terraform version

# AWS
aws --version
aws configure list
aws configure list-profiles
aws sts get-caller-identity

# Profile
aws sts get-caller-identity --profile dev

# SSO
aws configure sso
aws sso login --profile dev

# Provider upgrade
terraform init -upgrade

# Validate
terraform validate

# Plan
terraform plan
```

---

# 🏆 42. Mastery Checklist

```text
PROVIDERS
☑ Provider
☑ Resource
☑ Data Source
☑ Provider Source
☑ Provider Version
☑ Provider Lock File
☑ Provider Alias

AWS AUTHENTICATION
☑ CLI Profile
☑ IAM Identity Center
☑ Environment
☑ IAM Role
☑ AssumeRole
☑ Multi-Account
☑ Multi-Region

MULTI-CLOUD
☑ AWS
☑ Azure
☑ GCP
☑ Kubernetes
☑ Docker
☑ GitHub

CI/CD
☑ GitHub OIDC
☑ IAM Trust
☑ Temporary Credentials
☑ Least Privilege
☑ Plan Role
☑ Apply Role

SECURITY
☑ No Hard-Coded Secrets
☑ Credential Protection
☑ OIDC Restrictions
☑ Provider Verification
☑ Version Management
```

---

# 🗺️ 43. Terraform Roadmap

```text
01 Terraform Fundamentals       🟢
        ↓
02 Terraform Installation       🟢
        ↓
03 Terraform CLI & Workflow     🟢
        ↓
04 Providers & Authentication   🟢 ← YOU ARE HERE
        ↓
05 Variables & Outputs
        ↓
06 Resources & Data Sources
        ↓
07 Expressions & Functions
        ↓
08 Meta-Arguments
        ↓
09 State & State Management
        ↓
10 Remote State & Locking
        ↓
11 Terraform Modules
        ↓
12 AWS VPC Project
        ↓
13 AWS EC2 Project
        ↓
14 AWS S3 Project
        ↓
15 IAM & Security
        ↓
16 HCP Terraform
        ↓
17 Terraform + GitHub Actions
        ↓
18 Terraform Security & Policy
        ↓
19 Multi-Cloud Terraform
        ↓
20 Enterprise Capstone
```

---

<div align="center">

# 🔐 IDENTITY → PROVIDER → CLOUD → INFRASTRUCTURE

### VishwaTech Labs

**Terraform • AWS • Azure • GCP • Docker • GitHub Actions • Kubernetes • Ansible • DevSecOps**

### By Vishwanath Gowda H

⭐ Authenticate securely. Automate confidently.

</div>
