<div align="center">

# ☁️ Terraform Multi-Cloud — AWS + Azure + GCP Masterclass

### 🌍 One Terraform Codebase • Multiple Clouds • Provider Aliases • Secure Authentication | VishwaTech Labs

[![Terraform](https://img.shields.io/badge/Terraform-844FBA?logo=terraform&logoColor=white)](https://developer.hashicorp.com/terraform/docs)
[![AWS](https://img.shields.io/badge/AWS-FF9900?logo=amazonaws&logoColor=white)](https://aws.amazon.com/)
[![Azure](https://img.shields.io/badge/Azure-0078D4?logo=microsoftazure&logoColor=white)](https://azure.microsoft.com/)
[![GCP](https://img.shields.io/badge/GCP-4285F4?logo=googlecloud&logoColor=white)](https://cloud.google.com/)
[![Multi-Cloud](https://img.shields.io/badge/Architecture-Multi--Cloud-success)](#-multi-cloud-architecture)
[![Labs](https://img.shields.io/badge/Labs-30+-purple)](#-hands-on-labs)

**Learn how to design, authenticate, provision and govern AWS, Azure and Google Cloud infrastructure using Terraform — with provider aliases, reusable modules, environment separation, security and CI/CD.**

[📘 Terraform Providers](https://developer.hashicorp.com/terraform/language/providers) •
[📘 AWS Provider](https://registry.terraform.io/providers/hashicorp/aws/latest) •
[📘 AzureRM Provider](https://registry.terraform.io/providers/hashicorp/azurerm/latest) •
[📘 Google Provider](https://registry.terraform.io/providers/hashicorp/google/latest)

</div>

---

# 🎯 Project Goal

Build a multi-cloud architecture:

```text
                         TERRAFORM
                             │
          ┌──────────────────┼──────────────────┐
          ▼                  ▼                  ▼
        AWS                AZURE               GCP
          │                  │                  │
       VPC/VPC           VNet/VMs          VPC/Compute
       EC2/S3            Storage/AKS       GCS/GKE
       IAM               Entra/RBAC        IAM
```

The objective is not simply:

```text
"Deploy to three clouds."
```

It is:

```text
ONE IaC PLATFORM
+
SEPARATE CLOUD PROVIDERS
+
SECURE IDENTITY
+
REUSABLE MODULES
+
ENVIRONMENT GOVERNANCE
```

---

# 🧠 1. What Is Multi-Cloud?

Multi-cloud means using services from more than one cloud provider.

Example:

```text
AWS
+
Azure
+
GCP
```

Possible reasons:

```text
Business requirements
Existing investments
Regional availability
Specialized services
Resilience
Acquisition / merger requirements
Avoiding excessive provider dependency
```

Multi-cloud should be adopted for a clear business or technical reason, not simply because it is possible.

---

# 🆚 2. Multi-Cloud vs Hybrid Cloud

### Multi-Cloud

```text
AWS
 +
Azure
 +
GCP
```

### Hybrid Cloud

```text
On-Premises
     +
Cloud
```

### Hybrid Multi-Cloud

```text
On-Prem
  │
  ├── AWS
  ├── Azure
  └── GCP
```

---

# 🧩 3. Terraform's Role

Terraform provides a common Infrastructure-as-Code workflow.

```text
Terraform
    │
    ├── AWS Provider
    ├── AzureRM Provider
    └── Google Provider
```

Terraform does **not** make AWS, Azure and GCP identical.

Instead:

```text
Common IaC Workflow
        +
Cloud-Specific Resources
```

---

# ☁️ 4. Cloud Mapping

| Requirement | AWS | Azure | GCP |
|---|---|---|---|
| Virtual Network | VPC | VNet | VPC |
| Compute | EC2 | Virtual Machine | Compute Engine |
| Object Storage | S3 | Blob Storage | Cloud Storage |
| Kubernetes | EKS | AKS | GKE |
| IAM | IAM | Entra ID / RBAC | Cloud IAM |
| DNS | Route 53 | Azure DNS | Cloud DNS |
| Key Management | KMS | Key Vault | Cloud KMS |
| Container Registry | ECR | ACR | Artifact Registry |
| Monitoring | CloudWatch | Azure Monitor | Cloud Monitoring |

Service capabilities and naming differ, so do not force artificial one-to-one abstractions.

---

# 🔐 5. Multi-Cloud Authentication

A secure architecture:

```text
Terraform
   │
   ├── AWS → IAM Role / Federation
   │
   ├── Azure → Workload Identity / Service Principal
   │
   └── GCP → Workload Identity Federation / Service Account
```

Avoid:

```text
Hardcoded credentials
Credentials in Git
Long-lived CI keys where federation is available
```

---

# 🔥 6. Provider Configuration

Terraform can configure multiple providers:

```hcl
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 6.0"
    }

    azurerm = {
      source  = "hashicorp/azurerm"
      version = "~> 4.0"
    }

    google = {
      source  = "hashicorp/google"
      version = "~> 7.0"
    }
  }
}
```

Always verify current provider versions and compatibility before production use.

---

# 🟠 7. AWS Provider

```hcl
provider "aws" {
  region = var.aws_region
}
```

Use secure authentication:

```text
AWS CLI profile
IAM role
OIDC
Environment-specific federation
```

---

# 🔵 8. Azure Provider

```hcl
provider "azurerm" {
  features {}
}
```

Authentication can use supported Azure identity mechanisms such as:

```text
Azure CLI
Managed Identity
Workload Identity Federation
Service Principal
```

Prefer short-lived/federated identity for CI/CD where feasible.

---

# 🔴 9. GCP Provider

```hcl
provider "google" {
  project = var.gcp_project
  region  = var.gcp_region
}
```

Authentication can use:

```text
Application Default Credentials
Workload Identity Federation
Service Account
```

For CI/CD, prefer federation over long-lived service-account keys where supported.

---

# 🧩 10. Provider Aliases

Aliases allow multiple configurations of the same provider.

Example AWS:

```hcl
provider "aws" {
  region = "us-east-1"
}

provider "aws" {
  alias  = "europe"
  region = "eu-north-1"
}
```

Resource:

```hcl
resource "aws_s3_bucket" "europe" {
  provider = aws.europe

  bucket = var.bucket_name
}
```

---

# 🌍 11. Multi-Region AWS

```text
AWS Provider
│
├── us-east-1
│
└── eu-north-1
```

Example:

```hcl
provider "aws" {
  region = "us-east-1"
}

provider "aws" {
  alias  = "eu"
  region = "eu-north-1"
}
```

This is a useful bridge between single-cloud multi-region and true multi-cloud architecture.

---

# 🔵 12. Azure Provider Aliases

Provider aliasing can also be used when multiple Azure subscriptions or configurations are required.

Concept:

```hcl
provider "azurerm" {
  features {}
}

provider "azurerm" {
  alias           = "secondary"
  subscription_id = var.secondary_subscription_id
  features {}
}
```

Pass the correct provider explicitly to resources/modules.

---

# 🔴 13. GCP Provider Aliases

Example:

```hcl
provider "google" {
  project = var.gcp_project_a
  region  = var.gcp_region
}

provider "google" {
  alias   = "secondary"
  project = var.gcp_project_b
  region  = var.gcp_region
}
```

Use aliases when managing multiple projects or configurations.

---

# 🧠 14. Provider Selection

Terraform resource:

```hcl
resource "aws_s3_bucket" "data" {
  provider = aws.europe

  bucket = var.bucket_name
}
```

Mental model:

```text
Resource
   ↓
Provider Configuration
   ↓
Cloud Account / Subscription / Project
   ↓
Region
```

---

# 🧱 15. Multi-Cloud Architecture Patterns

### Pattern A — Separate Stacks

```text
terraform/
├── aws/
├── azure/
└── gcp/
```

### Pattern B — Shared Modules

```text
modules/
├── aws/
├── azure/
└── gcp/
```

### Pattern C — Platform Abstraction

```text
application
    ↓
platform module
    ↓
cloud-specific module
```

Choose based on how much behavior is genuinely shared.

---

# ⚠️ 16. Don't Over-Abbreviate Clouds

Bad abstraction:

```text
module "server" {
  source = "./universal-server"
}
```

when:

```text
AWS EC2
Azure VM
GCP Compute Engine
```

have significantly different semantics.

Better:

```text
modules/aws-ec2
modules/azure-vm
modules/gcp-compute
```

with shared interfaces only where they provide real value.

---

# 📦 17. Multi-Cloud Module Structure

```text
modules/
│
├── aws-network/
├── aws-compute/
│
├── azure-network/
├── azure-compute/
│
├── gcp-network/
└── gcp-compute/
```

Then:

```text
environments/
├── dev/
├── stage/
└── prod/
```

---

# 🏗️ 18. Environment Structure

Recommended:

```text
environments/
│
├── dev/
│   ├── aws/
│   ├── azure/
│   └── gcp/
│
├── stage/
│   ├── aws/
│   ├── azure/
│   └── gcp/
│
└── prod/
    ├── aws/
    ├── azure/
    └── gcp/
```

This creates clear boundaries.

---

# 💾 19. State Strategy

Do not put every cloud into one giant state file by default.

Better:

```text
AWS Network State
AWS Application State

Azure Network State
Azure Application State

GCP Network State
GCP Application State
```

Benefits:

```text
Smaller blast radius
Independent deployments
Clear ownership
Faster plans
Reduced coupling
```

---

# 🔗 20. Cross-Stack Data

Example:

```text
Network Stack
      ↓
Outputs
      ↓
Application Stack
```

Use supported mechanisms such as:

```text
Remote state
Cloud data sources
Platform APIs
Explicit variables
```

Avoid unnecessary cross-cloud state coupling.

---

# 🌐 21. Multi-Cloud Networking

Architecture:

```text
                GLOBAL APPLICATION
                       │
        ┌──────────────┼──────────────┐
        ▼              ▼              ▼
       AWS            Azure           GCP
        │              │              │
       VPC            VNet            VPC
        │              │              │
       App            App             App
```

Connectivity may use:

```text
VPN
Dedicated private connectivity
Transit architectures
Cloud routers
SD-WAN
Service mesh
Application-level connectivity
```

Terraform provisions the infrastructure; it does not automatically solve your networking design.

---

# 🔐 22. Multi-Cloud Identity

Think:

```text
Human Identity
      ↓
Enterprise IdP
      ↓
Cloud Federation
      ├── AWS
      ├── Azure
      └── GCP
```

For workloads:

```text
GitHub / HCP Terraform
      ↓
Federated Identity
      ↓
Cloud Role
```

---

# 🛡️ 23. Least Privilege Across Clouds

AWS:

```text
IAM Policy
```

Azure:

```text
RBAC
```

GCP:

```text
IAM
```

Principle remains:

```text
Minimum required access
+
Minimum required scope
+
Minimum required duration
```

---

# 🔑 24. Multi-Cloud Secrets

Avoid:

```text
aws_access_key = "..."
azure_client_secret = "..."
gcp_service_account_key = "..."
```

Prefer:

```text
OIDC
Workload Identity
Managed Identity
IAM Roles
Secret Managers
```

---

# 🧩 25. AWS + Azure + GCP Example

```hcl
provider "aws" {
  region = var.aws_region
}

provider "azurerm" {
  features {}
}

provider "google" {
  project = var.gcp_project
  region  = var.gcp_region
}
```

Then each cloud resource explicitly uses its provider.

---

# 🪣 26. AWS Storage Example

```hcl
resource "aws_s3_bucket" "data" {
  bucket = var.aws_bucket_name
}
```

---

# 🔵 27. Azure Storage Example

```hcl
resource "azurerm_storage_account" "data" {
  name                     = var.azure_storage_name
  resource_group_name      = var.resource_group_name
  location                 = var.azure_location
  account_tier             = "Standard"
  account_replication_type = "LRS"
}
```

Use globally unique names and comply with Azure naming requirements.

---

# 🔴 28. GCP Storage Example

```hcl
resource "google_storage_bucket" "data" {
  name     = var.gcp_bucket_name
  location = var.gcp_location
}
```

Bucket names are globally scoped in Google Cloud.

---

# 🖥️ 29. Compute Comparison

AWS:

```text
aws_instance
```

Azure:

```text
azurerm_linux_virtual_machine
```

GCP:

```text
google_compute_instance
```

Different resource models:

```text
Terraform syntax differs
Cloud APIs differ
Networking differs
IAM differs
Pricing differs
```

---

# ☸️ 30. Kubernetes Comparison

```text
AWS   → EKS
Azure → AKS
GCP   → GKE
```

Terraform can provision:

```text
Cluster
Node pools
Identity
Networking
Add-ons
```

But Kubernetes workloads are often managed separately from infrastructure provisioning.

---

# 🏷️ 31. Common Metadata Strategy

Use a common logical model:

```text
Environment
Application
Owner
CostCenter
ManagedBy
```

Map it to cloud-specific tagging/labeling systems:

```text
AWS Tags
Azure Tags
GCP Labels
```

---

# 📊 32. Naming Strategy

Example:

```text
<company>-<environment>-<cloud>-<component>
```

Examples:

```text
vishwatech-prod-aws-network
vishwatech-prod-azure-network
vishwatech-prod-gcp-network
```

Do not force one naming rule where a provider has stricter naming constraints.

---

# 🔐 33. Encryption Across Clouds

AWS:

```text
KMS
```

Azure:

```text
Key Vault
```

GCP:

```text
Cloud KMS
```

Common requirement:

```text
Encryption at rest
+
Encryption in transit
+
Key governance
```

---

# 📝 34. Logging Across Clouds

AWS:

```text
CloudTrail
CloudWatch
```

Azure:

```text
Azure Monitor
Activity Log
```

GCP:

```text
Cloud Logging
Cloud Audit Logs
```

Centralize where appropriate:

```text
Cloud Logs
    ↓
Central SIEM
```

---

# 🛡️ 35. Security Services

AWS:

```text
Security Hub
GuardDuty
Inspector
Config
```

Azure:

```text
Defender for Cloud
Azure Policy
```

GCP:

```text
Security Command Center
Organization Policy
```

Terraform can configure these controls, but security operations still require monitoring and response.

---

# 📜 36. Policy Across Clouds

A mature platform can enforce:

```text
Encryption
Allowed regions
Required tags
Public exposure
IAM boundaries
Logging
Approved machine types
```

Architecture:

```text
Terraform Plan
      ↓
Policy Engine
      ↓
Cloud-Specific Rules
      ↓
PASS / FAIL
```

---

# 🔥 37. Multi-Cloud CI/CD

```text
GitHub
   │
   ▼
GitHub Actions
   │
   ├── Terraform fmt
   ├── Validate
   ├── Security
   └── Plan
          │
          ▼
      Approval
          │
          ▼
     ┌────┼────┐
     ▼    ▼    ▼
    AWS Azure  GCP
```

Each cloud should use separate credentials/roles.

---

# 🔐 38. GitHub OIDC Multi-Cloud

Concept:

```text
GitHub OIDC
     │
 ┌───┼────────┐
 ▼   ▼        ▼
AWS Azure    GCP
Role Federated Identity
```

This avoids storing long-lived cloud credentials in GitHub.

Exact federation setup differs by provider.

---

# 🧩 39. Multi-Cloud HCP Terraform

Architecture:

```text
GitHub
   ↓
HCP Terraform
   │
   ├── AWS Workspace
   ├── Azure Workspace
   └── GCP Workspace
```

Benefits:

```text
Central state
RBAC
Remote runs
Variables
Policy
Registry
Audit
```

---

# 🏢 40. Enterprise Multi-Cloud Architecture

```text
                         GITHUB
                            │
                            ▼
                      HCP TERRAFORM
                            │
          ┌─────────────────┼─────────────────┐
          ▼                 ▼                 ▼
        AWS               AZURE              GCP
          │                 │                 │
       Network           Network           Network
       Security          Security          Security
       Compute           Compute           Compute
       Storage           Storage           Storage
          │                 │                 │
          └─────────────────┼─────────────────┘
                            ▼
                       CENTRAL SIEM
```

---

# 📁 41. Project Folder Structure

```text
19-Terraform-Multi-Cloud/
│
├── README.md
│
├── modules/
│   ├── aws/
│   │   ├── network/
│   │   ├── compute/
│   │   └── storage/
│   │
│   ├── azure/
│   │   ├── network/
│   │   ├── compute/
│   │   └── storage/
│   │
│   └── gcp/
│       ├── network/
│       ├── compute/
│       └── storage/
│
├── environments/
│   ├── dev/
│   ├── stage/
│   └── prod/
│
├── policies/
│   ├── security/
│   ├── naming/
│   └── tagging/
│
└── .github/
    └── workflows/
        └── multi-cloud.yml
```

---

# ⚙️ 42. Root Provider Example

```hcl
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 6.0"
    }

    azurerm = {
      source  = "hashicorp/azurerm"
      version = "~> 4.0"
    }

    google = {
      source  = "hashicorp/google"
      version = "~> 7.0"
    }
  }
}

provider "aws" {
  region = var.aws_region
}

provider "azurerm" {
  features {}
}

provider "google" {
  project = var.gcp_project
  region  = var.gcp_region
}
```

---

# 🧩 43. Module Provider Passing

When a module requires a specific provider configuration:

```hcl
module "aws_network" {
  source = "./modules/aws/network"

  providers = {
    aws = aws
  }
}
```

Aliased provider:

```hcl
module "aws_europe" {
  source = "./modules/aws/network"

  providers = {
    aws = aws.europe
  }
}
```

Provider requirements inside modules should be declared correctly.

---

# 🔄 44. Multi-Cloud State Strategy

Recommended:

```text
STATE
│
├── AWS
│   ├── network
│   └── application
│
├── AZURE
│   ├── network
│   └── application
│
└── GCP
    ├── network
    └── application
```

Avoid unnecessary:

```text
ONE HUGE STATE
```

because the blast radius grows rapidly.

---

# 🚦 45. Dependency Strategy

Good:

```text
AWS Network
    ↓
AWS Application
```

Potentially complex:

```text
AWS Network
    ↓
Azure Application
    ↓
GCP Service
    ↓
AWS Database
```

Cross-cloud dependencies increase:

```text
Latency
Operational complexity
Failure modes
Security complexity
Cost
```

Use them intentionally.

---

# 💰 46. Multi-Cloud Cost Management

Track:

```text
AWS Cost Explorer
Azure Cost Management
GCP Cloud Billing
```

Common metadata:

```text
Project
Owner
Environment
CostCenter
Application
```

Terraform can enforce tagging/labeling standards.

---

# 🌍 47. Region Governance

Example approved locations:

```text
AWS:
eu-north-1

Azure:
North Europe

GCP:
europe-north1
```

Do not assume region names are identical across providers.

Policy should map each provider to approved locations.

---

# 🛡️ 48. Multi-Cloud Security Checklist

```text
☑ Federated authentication
☑ No hardcoded credentials
☑ Separate cloud roles
☑ Least privilege
☑ Remote state
☑ Encryption
☑ Logging
☑ Security scanning
☑ Policy as code
☑ Approved regions
☑ Mandatory tags
☑ Module review
☑ Provider lock file
☑ CI/CD approvals
```

---

# 🧪 49. Hands-On Labs

## Lab 01 — AWS Provider

Configure AWS.

## Lab 02 — Azure Provider

Configure Azure.

## Lab 03 — GCP Provider

Configure GCP.

## Lab 04 — Three Providers

Configure all three.

## Lab 05 — Provider Alias

Deploy AWS to two regions.

## Lab 06 — Azure Subscriptions

Use provider aliases.

## Lab 07 — GCP Projects

Use provider aliases.

## Lab 08 — AWS S3

Create storage.

## Lab 09 — Azure Storage

Create storage.

## Lab 10 — GCP Storage

Create storage.

## Lab 11 — Multi-Cloud Storage

Deploy storage in all three clouds.

## Lab 12 — AWS Network

Create VPC.

## Lab 13 — Azure Network

Create VNet.

## Lab 14 — GCP Network

Create VPC.

## Lab 15 — Multi-Cloud Network

Build three isolated networks.

## Lab 16 — Compute

Deploy one compute resource per cloud.

## Lab 17 — IAM

Configure least privilege.

## Lab 18 — Tags / Labels

Standardize metadata.

## Lab 19 — Encryption

Configure cloud-native encryption.

## Lab 20 — Logging

Enable audit logging.

## Lab 21 — Provider Aliases

Deploy across multiple accounts/projects.

## Lab 22 — Remote State

Separate state per cloud.

## Lab 23 — Multi-Cloud Modules

Build reusable modules.

## Lab 24 — Security Scan

Run Checkov/Trivy.

## Lab 25 — Policy as Code

Enforce region/tag rules.

## Lab 26 — GitHub OIDC

Federate GitHub to all clouds.

## Lab 27 — CI/CD

Build multi-cloud pipeline.

## Lab 28 — HCP Terraform

Create cloud-specific workspaces.

## Lab 29 — Drift

Detect out-of-band changes.

## Lab 30 — Enterprise Challenge

Build:

```text
GitHub
   ↓
CI/CD
   ↓
Terraform
   │
   ├── AWS
   ├── Azure
   └── GCP
        │
        ▼
Security + Policy + Audit
```

---

# 🏆 50. Production Checklist

```text
ARCHITECTURE
☑ Clear reason for multi-cloud
☑ Minimize cross-cloud coupling
☑ Separate state
☑ Separate environments

IDENTITY
☑ Federation
☑ Least privilege
☑ Short-lived credentials
☑ Separate cloud roles

TERRAFORM
☑ Provider constraints
☑ Lock file
☑ Reusable modules
☑ State boundaries
☑ Explicit provider aliases

SECURITY
☑ Encryption
☑ Logging
☑ Security scanning
☑ Policy as code
☑ Drift detection

OPERATIONS
☑ CI/CD
☑ Approval
☑ Monitoring
☑ Disaster recovery
☑ Cost controls
```

---

# 🚨 51. Common Mistakes

```text
❌ One state file for everything
❌ One credential for all clouds
❌ Hardcoded secrets
❌ Assuming cloud services are identical
❌ Over-abstracting modules
❌ Excessive cross-cloud dependencies
❌ No provider version constraints
❌ Ignoring .terraform.lock.hcl
❌ No environment separation
❌ No cost governance
```

---

# 🔍 52. Troubleshooting

## Provider Authentication Fails

Check:

```text
Cloud identity
Credential source
Role/federation
Subscription/project/account
Region
Permissions
```

---

## Wrong Provider Used

Check:

```text
provider alias
resource provider argument
module providers map
```

---

## Module Provider Error

Check:

```text
required_providers
provider aliases
providers map
module declaration
```

---

## State Conflict

Check:

```text
Workspace
Backend
State key
Cloud environment
Concurrent runs
```

---

## Permission Denied

Check:

```text
AWS IAM
Azure RBAC
GCP IAM
Resource policy
Organization policies
Conditions
```

---

# 🎓 53. Interview Questions

### Beginner

1. What is multi-cloud?
2. Multi-cloud vs hybrid cloud?
3. Why use Terraform for multi-cloud?
4. What is a Terraform provider?
5. What is a provider alias?
6. What is AWS VPC equivalent in Azure?
7. What is S3 equivalent in GCP?
8. What is Azure RBAC?
9. What is GCP IAM?
10. Why separate Terraform state?

### Intermediate

11. How do provider aliases work?
12. How do you authenticate Terraform to three clouds?
13. How do you design multi-cloud modules?
14. How do you manage state?
15. How do you enforce common tags?
16. How do you secure CI/CD?
17. How do you implement GitHub OIDC?
18. How do you handle cloud-specific differences?
19. How do you manage multi-cloud networking?
20. How do you control multi-cloud cost?

### Advanced

21. Design enterprise AWS/Azure/GCP Terraform architecture.
22. How would you avoid over-abstraction?
23. How would you isolate state?
24. How would you implement multi-cloud identity federation?
25. How would you enforce common security policy?
26. How would you handle cross-cloud dependencies?
27. How would you design disaster recovery?
28. How would you govern provider/module supply chain?
29. How would you build multi-cloud CI/CD?
30. Design a secure multi-cloud landing-zone automation platform.

---

# ⚡ 54. Cheat Sheet

Initialize:

```bash
terraform init
```

Format:

```bash
terraform fmt -recursive
```

Validate:

```bash
terraform validate
```

Plan:

```bash
terraform plan
```

Apply:

```bash
terraform apply
```

Providers:

```hcl
provider "aws" {
  region = "us-east-1"
}

provider "aws" {
  alias  = "europe"
  region = "eu-north-1"
}
```

Resource:

```hcl
resource "aws_s3_bucket" "example" {
  provider = aws.europe
  bucket   = var.bucket_name
}
```

Mental model:

```text
Terraform
 ├── AWS
 ├── Azure
 └── GCP
```

---

# 🧠 55. Master Mental Model

```text
                         TERRAFORM
                             │
             ┌───────────────┼───────────────┐
             ▼               ▼               ▼
            AWS             AZURE            GCP
             │               │               │
            IAM             RBAC             IAM
             │               │               │
            VPC             VNet             VPC
             │               │               │
          Compute          Compute         Compute
             │               │               │
          Storage          Storage         Storage
             │               │               │
             └───────────────┼───────────────┘
                             ▼
                       COMMON GOVERNANCE
                             │
              ┌──────────────┼──────────────┐
              ▼              ▼              ▼
           Security         Policy          Audit
              │              │              │
              └──────────────┼──────────────┘
                             ▼
                           CI/CD
```

---

# 🗺️ 56. Terraform Roadmap

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
15 IAM & Security               🟢
16 HCP Terraform                🟢
17 Terraform + GitHub Actions   🟢
18 Terraform Security & Policy  🟢
19 Multi-Cloud Terraform        🟢 ← YOU ARE HERE
20 Enterprise Capstone          ⏭️ NEXT
```

---

<div align="center">

# 🌍 ONE IaC WORKFLOW • THREE CLOUDS • ENTERPRISE GOVERNANCE

### VishwaTech Labs

**Terraform • AWS • Azure • GCP • IAM • GitHub Actions • Docker • Kubernetes • Ansible • DevSecOps**

### By Vishwanath Gowda H

⭐ Multi-Cloud • Security • Automation • Infrastructure as Code

</div>
