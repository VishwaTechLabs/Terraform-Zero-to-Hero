<div align="center">

# ☁️ HCP Terraform — Complete Enterprise Masterclass

### 🏢 Remote Operations • Workspaces • VCS • RBAC • Variable Sets • Private Registry • Policy as Code | VishwaTech Labs

[![Terraform](https://img.shields.io/badge/Terraform-844FBA?logo=terraform&logoColor=white)](https://developer.hashicorp.com/terraform/docs)
[![HCP Terraform](https://img.shields.io/badge/HCP%20Terraform-Enterprise%20Workflow-844FBA)](https://developer.hashicorp.com/terraform/cloud-docs)
[![VCS](https://img.shields.io/badge/VCS-GitHub-black?logo=github)](https://github.com/)
[![Security](https://img.shields.io/badge/Security-RBAC%20%7C%20OIDC-green)](#-security)
[![Labs](https://img.shields.io/badge/Labs-30+-purple)](#-hands-on-labs)

**Learn how to move Terraform from a developer laptop into a collaborative, governed, remote execution platform using HCP Terraform.**

[📘 HCP Terraform](https://developer.hashicorp.com/terraform/cloud-docs) •
[📘 Workspaces](https://developer.hashicorp.com/terraform/cloud-docs/workspaces) •
[📘 VCS Integration](https://developer.hashicorp.com/terraform/cloud-docs/vcs) •
[📘 Private Registry](https://developer.hashicorp.com/terraform/cloud-docs/registry)

</div>

---

# 🎯 Project Goal

Build an enterprise Terraform workflow:

```text
Developer
    │
    ▼
GitHub Repository
    │
    ▼
HCP Terraform
    │
    ├── Workspace
    ├── Variables
    ├── Variable Sets
    ├── Remote State
    ├── Run Workflow
    ├── Policies
    └── RBAC
    │
    ▼
Cloud Provider
    │
    ▼
AWS / Azure / GCP
```

The goal is to understand why teams use HCP Terraform instead of asking every engineer to run:

```bash
terraform apply
```

from a personal laptop.

---

# 🧠 1. What Is HCP Terraform?

HCP Terraform is HashiCorp's hosted platform for collaborative Terraform workflows.

It provides capabilities around:

```text
Remote state
Remote runs
Workspace management
VCS integration
Variables
Teams and permissions
Private module registry
Policy controls
Run history
Auditability
Collaboration
```

Think:

```text
Terraform CLI
      +
Git
      +
Remote State
      +
Remote Execution
      +
RBAC
      +
Governance
      =
HCP Terraform
```

Feature availability depends on the HCP Terraform plan and current product capabilities.

---

# 🆚 2. Local Terraform vs HCP Terraform

### Local

```text
Developer Laptop
      │
      ├── Terraform CLI
      ├── Credentials
      └── State
              │
              ▼
             AWS
```

### HCP Terraform

```text
Developer
    │
    ▼
GitHub
    │
    ▼
HCP Terraform
    │
    ├── State
    ├── Runs
    ├── Variables
    ├── Permissions
    └── Policies
    │
    ▼
AWS
```

---

# 🧠 3. Why Teams Use HCP Terraform

Without a centralized platform:

```text
Engineer A → laptop
Engineer B → laptop
Engineer C → laptop
```

Problems:

```text
Different credentials
Different Terraform versions
State management
Manual approvals
Limited auditability
Inconsistent workflows
```

With HCP Terraform:

```text
              HCP Terraform
                    │
        ┌───────────┼───────────┐
        ▼           ▼           ▼
      State        Runs        Access
        │           │           │
        └───────────┼───────────┘
                    ▼
                 Cloud
```

---

# 🏢 4. Organization

An HCP Terraform organization is a top-level collaboration boundary.

Concept:

```text
Organization
      │
      ├── Projects
      │
      ├── Workspaces
      │
      ├── Teams
      │
      ├── Variable Sets
      │
      └── Registry
```

Choose organization structure based on:

```text
Company boundaries
Security
Billing
Team ownership
Environment strategy
Governance
```

---

# 📁 5. Projects

Projects help organize workspaces.

Example:

```text
Organization
│
├── Networking Project
│   ├── dev-network
│   └── prod-network
│
├── Application Project
│   ├── dev-app
│   └── prod-app
│
└── Security Project
    ├── iam
    └── logging
```

This is much easier to manage than one flat list of hundreds of workspaces.

---

# 🧩 6. Workspace

A workspace is a central HCP Terraform unit for a Terraform configuration and its state.

Conceptually:

```text
Workspace
│
├── Configuration
├── Variables
├── State
├── Runs
├── Permissions
└── Run History
```

Example:

```text
prod-vpc
prod-eks
prod-app
dev-vpc
dev-app
```

---

# 🧠 7. Workspace Strategies

### Strategy A — Environment Workspaces

```text
vpc-dev
vpc-stage
vpc-prod
```

### Strategy B — Component Workspaces

```text
network-prod
eks-prod
database-prod
application-prod
```

### Strategy C — Account/Region

```text
prod-us-east-1
prod-eu-north-1
```

Choose based on:

```text
Blast radius
Ownership
State boundaries
Deployment frequency
Access boundaries
```

---

# ⚠️ 8. Don't Put Everything in One Workspace

Bad:

```text
ONE WORKSPACE
│
├── VPC
├── EKS
├── RDS
├── IAM
├── S3
├── Lambda
└── Everything
```

A failure or change can affect an enormous blast radius.

Better:

```text
Network Workspace
Security Workspace
Data Workspace
Application Workspace
```

Use dependencies carefully.

---

# 🔗 9. Workspace Dependencies

Example:

```text
Network Workspace
       │
       ▼
Application Workspace
       │
       ▼
Database Workspace
```

Outputs from one workspace can be consumed by another using supported remote-state/data-sharing mechanisms.

Keep dependency chains intentional.

---

# 💾 10. Remote State

HCP Terraform manages Terraform state centrally.

Instead of:

```text
terraform.tfstate
```

on a developer laptop:

```text
HCP Terraform
     │
     ▼
Remote State
```

Benefits:

```text
Centralized
Access controlled
Versioned
Collaborative
Auditable
```

---

# 🔒 11. State Security

Terraform state can contain:

```text
Resource IDs
Configuration data
Sensitive values
Provider-related data
```

Therefore:

```text
State = Sensitive Infrastructure Data
```

Protect it using:

```text
RBAC
Workspace permissions
Secure credentials
Least privilege
Audit
```

Marking a variable as sensitive does not mean its value is automatically absent from state.

---

# 🔄 12. State Locking and Concurrency

Centralized Terraform execution helps coordinate runs.

Concept:

```text
Engineer A
    │
    ▼
Workspace
    ▲
    │
Engineer B
```

HCP Terraform provides run coordination so teams do not casually perform conflicting applies against the same workspace.

---

# 🚀 13. Run Workflow

Typical:

```text
Code Change
    ↓
Git Commit
    ↓
Pull Request
    ↓
Plan
    ↓
Review
    ↓
Merge
    ↓
Apply
```

This creates:

```text
Code
+
Review
+
Infrastructure Change
```

as one traceable workflow.

---

# 🧪 14. Plan

A plan previews infrastructure changes.

Concept:

```text
Current State
     +
Configuration
     ↓
Terraform Plan
     ↓
Create
Update
Destroy
```

Review:

```text
Resources added
Resources changed
Resources destroyed
```

before approval.

---

# 🚀 15. Apply

Apply executes the approved Terraform changes.

Production pattern:

```text
PR
 ↓
Plan
 ↓
Human Review
 ↓
Approval
 ↓
Apply
```

Avoid uncontrolled production applies.

---

# 🔀 16. VCS-Driven Workflow

HCP Terraform can connect to version control systems.

Example:

```text
Developer
    ↓
GitHub
    ↓
Pull Request
    ↓
HCP Terraform
    ↓
Terraform Plan
    ↓
Review
    ↓
Merge
    ↓
Apply
```

This is one of the most important enterprise workflows.

---

# 🔗 17. GitHub Integration

Typical repository:

```text
terraform-network/
├── main.tf
├── variables.tf
├── outputs.tf
└── versions.tf
```

Workflow:

```text
git push
   ↓
HCP Terraform detects change
   ↓
Workspace run
   ↓
Plan
```

Exact VCS setup depends on the connected GitHub integration and current HCP Terraform interface.

---

# 🌿 18. Branch Strategy

Example:

```text
main
 │
 ├── feature/network
 ├── feature/security
 └── feature/application
```

Production:

```text
Pull Request
      ↓
Review
      ↓
Merge
      ↓
Production Run
```

Protect:

```text
main
production environment
deployment permissions
```

---

# 🧪 19. Speculative Plans

A speculative plan helps evaluate a change without performing the final production apply.

Concept:

```text
Pull Request
     ↓
Speculative Plan
     ↓
Review
     ↓
Merge
```

This is valuable for infrastructure code review.

---

# 🔐 20. Variables

HCP Terraform supports workspace-level variables.

Concept:

```text
Workspace
│
├── Terraform Variables
└── Environment Variables
```

Examples:

```text
AWS_REGION
instance_type
environment
```

Sensitive values should be stored as sensitive variables rather than committed into Git.

---

# 🔑 21. Sensitive Variables

Examples:

```text
API tokens
Passwords
Private keys
Cloud credentials
Database secrets
```

Store them securely.

Never:

```text
Commit secrets
Print secrets
Put secrets in README
```

---

# 🧩 22. Variable Sets

Variable sets allow reusable variables to be shared across workspaces.

Example:

```text
AWS Organization
      │
      ▼
Common Variable Set
      │
 ┌────┼────┐
 ▼    ▼    ▼
Dev  Stage Prod
```

Useful for:

```text
Common cloud region
Organization-wide variables
Shared provider configuration
Standardized settings
```

Do not use variable sets as a dumping ground for unrelated secrets.

---

# 🔒 23. Sensitive Variable Sets

A variable set can centralize sensitive configuration.

Example:

```text
Common AWS configuration
Common service endpoints
Organization-specific variables
```

But minimize secret distribution.

Principle:

```text
Only give a workspace the secrets it actually needs.
```

---

# 🧑‍🤝‍🧑 24. Teams

Teams group users.

Example:

```text
Organization
│
├── Platform Team
├── Security Team
├── Network Team
├── Application Team
└── DevOps Team
```

Teams can receive workspace/project permissions according to the organization's access model.

---

# 🔐 25. RBAC

RBAC = Role-Based Access Control.

Concept:

```text
User
 ↓
Team
 ↓
Permission
 ↓
Workspace / Project
```

Typical roles/permissions vary by HCP Terraform plan and resource scope.

Design:

```text
Developer
   → plan/read

Platform
   → manage

Production Approver
   → approve

Administrator
   → organization administration
```

Grant the minimum necessary access.

---

# 🏢 26. Environment Separation

Example:

```text
DEV
│
├── developers
└── automated deployment

STAGE
│
├── platform
└── QA

PROD
│
├── platform
├── security
└── approval
```

Production should have stronger controls.

---

# 🚦 27. Run Tasks and Governance

Enterprise workflows can integrate checks around Terraform runs.

Concept:

```text
Terraform Plan
      ↓
Security Check
      ↓
Policy Check
      ↓
Compliance Check
      ↓
Approval
      ↓
Apply
```

Examples:

```text
Security scanning
Policy evaluation
Cost estimation
Custom organizational checks
```

Exact available integrations depend on current HCP Terraform capabilities and plan.

---

# 📜 28. Policy as Code

Policy as code means expressing governance rules as code.

Example:

```text
Rule:
EC2 must not use public IP
```

or:

```text
Rule:
S3 bucket must have encryption
```

or:

```text
Rule:
Production resources require approved tags
```

Concept:

```text
Terraform Plan
      ↓
Policy
      ↓
Pass / Fail
```

---

# 🛡️ 29. Sentinel

HashiCorp Sentinel is a policy-as-code framework used with supported HashiCorp products/plans.

Concept:

```text
Terraform Plan
      ↓
Sentinel Policy
      ↓
PASS / FAIL
```

Example policy idea:

```text
Production EC2
    ↓
Must use approved instance types
```

Always verify current HCP Terraform policy features and plan availability.

---

# 🧠 30. Policy Levels

Organizations can create different governance strength:

```text
Advisory
    ↓
Warning
    ↓
Mandatory
```

The exact enforcement behavior depends on the policy framework/configuration.

---

# 💰 31. Cost Governance

Infrastructure changes can create unexpected costs.

Concept:

```text
Terraform Plan
     ↓
Cost Estimate
     ↓
Review
     ↓
Approval
```

Example rule:

```text
Production change
+
Monthly cost increase > threshold
=
Additional approval
```

Use current HCP Terraform cost-management features where available.

---

# 🧩 32. Private Registry

HCP Terraform can provide a private module registry for organizations.

Architecture:

```text
Private Registry
      │
 ┌────┼─────┐
 ▼    ▼     ▼
VPC  EC2   IAM
Module Module Module
```

Advantages:

```text
Centralized modules
Versioning
Documentation
Reuse
Governance
```

---

# 📦 33. Private Module

Example source pattern:

```hcl
module "network" {
  source  = "app.terraform.io/example/network/aws"
  version = "1.0.0"

  # inputs
}
```

The exact hostname/namespace depends on your HCP Terraform organization.

---

# 🏷️ 34. Module Versioning

Example:

```text
network
v1.0.0
v1.1.0
v2.0.0
```

Production should deliberately select versions.

Avoid silently consuming breaking changes.

---

# 🔄 35. Module Upgrade

Process:

```text
Current v1
    ↓
Read changelog
    ↓
Review breaking changes
    ↓
Test
    ↓
Plan
    ↓
Review
    ↓
Upgrade
```

Treat modules like software libraries.

---

# 🤖 36. HCP Terraform API / CLI

HCP Terraform can be managed through supported APIs, CLI workflows and Terraform configuration.

Use automation for:

```text
Workspace creation
Variable management
Team access
Policy management
Run operations
```

Do not manually configure hundreds of workspaces if the organization can standardize the process.

---

# 🏗️ 37. Terraforming HCP Terraform

The HCP Terraform platform itself can be managed using the Terraform provider for HCP Terraform.

Concept:

```text
Terraform
    ↓
HCP Terraform Provider
    ↓
HCP Terraform
    ↓
Organizations
Projects
Workspaces
Teams
Variables
```

This creates a powerful:

```text
Terraform manages Terraform
```

pattern.

Use the current provider documentation for supported resources and arguments.

---

# 🧩 38. Workspace as Code

A conceptual example:

```hcl
resource "tfe_workspace" "network" {
  name         = "prod-network"
  organization = var.organization
}
```

Provider/resource names and exact capabilities depend on the current HCP Terraform/Terraform Enterprise provider version.

---

# 🔐 39. Credentials for HCP Terraform

Common authentication approaches include:

```text
User/API tokens
Team tokens
Organization/workspace scoped credentials
OIDC/dynamic credentials where supported
```

Avoid putting HCP tokens in Git.

Use:

```text
Environment variables
Secure CI/CD secrets
HCP Terraform sensitive variables
```

where appropriate.

---

# ☁️ 40. Dynamic Cloud Credentials

A strong enterprise pattern is:

```text
HCP Terraform
      ↓
Identity Federation
      ↓
Cloud IAM Role
      ↓
Temporary Credentials
      ↓
Terraform Run
```

Instead of:

```text
Long-lived AWS access key
```

This reduces credential-management risk.

---

# 🔥 41. HCP Terraform + AWS OIDC Concept

```text
HCP Terraform Run
       │
       ▼
Identity Federation
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

The exact setup depends on the current HCP Terraform dynamic credential capabilities and AWS trust configuration.

---

# 🧱 42. Enterprise Architecture

```text
                         GITHUB
                           │
                           ▼
                    Pull Request
                           │
                           ▼
                    HCP TERRAFORM
                           │
        ┌──────────────────┼──────────────────┐
        ▼                  ▼                  ▼
     Projects          Workspaces         Registry
        │                  │                  │
        ▼                  ▼                  ▼
     Teams             Remote State        Modules
                           │
                           ▼
                     Terraform Plan
                           │
              ┌────────────┼────────────┐
              ▼            ▼            ▼
           Security      Policy        Cost
              │            │            │
              └────────────┼────────────┘
                           ▼
                        Approval
                           │
                           ▼
                         APPLY
                           │
                           ▼
                     AWS / Azure / GCP
```

---

# 📁 43. Project Folder Structure

```text
16-Terraform-HCP-Terraform/
│
├── README.md
│
├── terraform/
│   ├── versions.tf
│   ├── provider.tf
│   ├── variables.tf
│   ├── organization.tf
│   ├── projects.tf
│   ├── workspaces.tf
│   ├── variable_sets.tf
│   ├── teams.tf
│   ├── policies.tf
│   ├── outputs.tf
│   └── terraform.tfvars.example
│
├── examples/
│   ├── workspace/
│   ├── variable-set/
│   ├── team-rbac/
│   └── vcs-workspace/
│
└── policies/
    ├── naming/
    ├── security/
    └── tagging/
```

---

# ⚙️ 44. Terraform HCP Provider

A typical provider configuration uses the HCP Terraform/TFE provider supported by your chosen workflow.

Example:

```hcl
terraform {
  required_providers {
    tfe = {
      source = "hashicorp/tfe"
    }
  }
}

provider "tfe" {
  hostname = "app.terraform.io"
}
```

Authentication should be supplied securely rather than hardcoded.

---

# 🏢 45. Workspace Example

Conceptual example:

```hcl
resource "tfe_workspace" "network" {
  name         = "prod-network"
  organization = var.organization

  auto_apply = false
}
```

Production:

```text
auto_apply = false
```

is often preferable when a human approval gate is required.

---

# 🔗 46. VCS Workspace Concept

A VCS-driven workspace connects:

```text
Git Repository
       ↓
Workspace
       ↓
Terraform Configuration
```

Typical configuration includes:

```text
Repository
Branch
Working directory
VCS connection
Workspace settings
```

---

# 🧪 47. Terraform CLI with HCP Terraform

A configuration can use the HCP Terraform backend:

```hcl
terraform {
  cloud {
    organization = "example-org"

    workspaces {
      name = "network-dev"
    }
  }
}
```

Then:

```bash
terraform login
terraform init
terraform plan
```

The exact authentication and organization settings should match your HCP Terraform setup.

---

# 🔄 48. Migration from Local State

Before:

```text
Developer Laptop
     ↓
terraform.tfstate
```

After:

```text
HCP Terraform
     ↓
Remote State
```

Migration process:

```text
Backup
 ↓
Configure HCP Terraform
 ↓
terraform init
 ↓
Migrate state
 ↓
Verify
 ↓
Plan
```

Never delete local state before verifying successful migration.

---

# 🔐 49. State Migration Safety

Before migration:

```text
☑ No active apply
☑ Backup
☑ Correct workspace
☑ Correct cloud account
☑ Correct credentials
☑ Review state
```

After:

```text
☑ State exists remotely
☑ Plan is clean
☑ Correct resources tracked
☑ Team can access workspace
```

---

# 🧪 50. Hands-On Labs

## Lab 01 — Create HCP Terraform Account

Create a training organization.

## Lab 02 — Organization

Understand organization structure.

## Lab 03 — Project

Create a project.

## Lab 04 — Workspace

Create a workspace.

## Lab 05 — Remote State

Run Terraform with remote state.

## Lab 06 — Terraform Cloud Block

Configure:

```hcl
terraform {
  cloud {
    ...
  }
}
```

## Lab 07 — Workspace Variables

Create Terraform variables.

## Lab 08 — Sensitive Variables

Store a test secret securely.

## Lab 09 — Variable Set

Create shared configuration.

## Lab 10 — Teams

Create:

```text
Developers
Platform
Security
```

## Lab 11 — RBAC

Assign workspace permissions.

## Lab 12 — GitHub VCS

Connect repository.

## Lab 13 — Pull Request Plan

Create speculative plan workflow.

## Lab 14 — Production Apply

Configure controlled apply.

## Lab 15 — Module Registry

Publish a reusable module.

## Lab 16 — Consume Private Module

Use module from registry.

## Lab 17 — Module Versioning

Publish v1 and v2.

## Lab 18 — Workspace Dependencies

Connect network and application state.

## Lab 19 — Policy as Code

Create a basic governance policy.

## Lab 20 — Naming Policy

Enforce resource naming.

## Lab 21 — Tagging Policy

Require environment tags.

## Lab 22 — Security Policy

Prevent insecure configurations.

## Lab 23 — Cost Governance

Review infrastructure cost changes.

## Lab 24 — HCP Provider

Manage HCP Terraform with Terraform.

## Lab 25 — Workspace as Code

Create multiple workspaces automatically.

## Lab 26 — State Migration

Move a lab from local state.

## Lab 27 — Dynamic Credentials

Implement supported cloud federation.

## Lab 28 — GitHub + HCP

Build:

```text
GitHub
 ↓
HCP Terraform
 ↓
AWS
```

## Lab 29 — Enterprise RBAC

Build team-based access.

## Lab 30 — Enterprise Challenge

Build:

```text
GitHub
   ↓
HCP Terraform
   │
   ├── Projects
   ├── Workspaces
   ├── Teams
   ├── Variable Sets
   ├── Private Registry
   ├── Policy
   └── Remote State
   │
   ▼
AWS
```

with controlled production deployment.

---

# 🏆 51. Production Checklist

```text
ORGANIZATION
☑ Clear organization structure
☑ Projects
☑ Workspace ownership

STATE
☑ Remote state
☑ Access control
☑ Recovery strategy

WORKSPACES
☑ Clear naming
☑ Appropriate state boundaries
☑ Environment separation
☑ Controlled applies

SECURITY
☑ RBAC
☑ Least privilege
☑ Sensitive variables
☑ No credentials in Git
☑ Dynamic credentials where supported

VCS
☑ GitHub integration
☑ Protected branches
☑ PR review
☑ Speculative plans

GOVERNANCE
☑ Policy as code
☑ Security checks
☑ Cost controls
☑ Auditability

MODULES
☑ Private registry
☑ Versioning
☑ Documentation
```

---

# 🚨 52. Common Mistakes

```text
❌ One workspace for everything
❌ Giving everyone admin
❌ Storing secrets in Git
❌ Long-lived cloud credentials
❌ No production approval
❌ No state boundary design
❌ Uncontrolled module upgrades
❌ No policy checks
❌ No backup/recovery process
❌ Ignoring workspace dependencies
```

---

# 🔍 53. Troubleshooting

## Workspace Not Running

Check:

```text
Workspace configuration
VCS connection
Terraform version
Variables
Cloud credentials
Run queue
```

---

## Authentication Failure

Check:

```text
HCP token
Cloud credentials
OIDC configuration
IAM role
Environment variables
```

---

## Plan Fails

Check:

```text
Terraform version
Provider versions
Variables
State
Cloud permissions
```

---

## Apply Is Blocked

Check:

```text
Policy
Approval
Run task
Permissions
Workspace settings
```

---

## Module Not Found

Check:

```text
Registry namespace
Module name
Version
Organization permissions
Source address
```

---

# 🎓 54. Interview Questions

### Beginner

1. What is HCP Terraform?
2. What is a workspace?
3. What is an organization?
4. What is a project?
5. What is remote state?
6. What is VCS integration?
7. What are workspace variables?
8. What is a variable set?
9. What is a team?
10. Why use HCP Terraform?

### Intermediate

11. Local Terraform vs HCP Terraform?
12. How does a VCS-driven workflow work?
13. How do teams access workspaces?
14. How do variable sets work?
15. How do you separate environments?
16. How do you migrate state?
17. What is the private registry?
18. How do you version private modules?
19. What is policy as code?
20. How do you secure HCP Terraform?

### Advanced

21. Design HCP Terraform for 100 AWS accounts.
22. How would you design workspace boundaries?
23. How would you implement production approvals?
24. How would you eliminate long-lived cloud credentials?
25. How would you implement GitHub → HCP → AWS?
26. How would you design RBAC?
27. How would you govern third-party modules?
28. How would you design state recovery?
29. How would you implement policy-as-code at scale?
30. Design an enterprise Terraform platform.

---

# ⚡ 55. Cheat Sheet

Login:

```bash
terraform login
```

Initialize:

```bash
terraform init
```

Plan:

```bash
terraform plan
```

Apply:

```bash
terraform apply
```

HCP Terraform block:

```hcl
terraform {
  cloud {
    organization = "example-org"

    workspaces {
      name = "network-dev"
    }
  }
}
```

Typical workflow:

```text
Git
 ↓
PR
 ↓
Plan
 ↓
Review
 ↓
Merge
 ↓
Apply
```

Enterprise:

```text
Organization
 ↓
Projects
 ↓
Workspaces
 ↓
Teams
 ↓
Policies
 ↓
Cloud
```

---

# 🧠 56. Master Mental Model

Remember:

```text
                 HCP TERRAFORM
                       │
       ┌───────────────┼────────────────┐
       ▼               ▼                ▼
    WORKSPACES       TEAMS           REGISTRY
       │               │                │
       ▼               ▼                ▼
     STATE            RBAC            MODULES
       │
       ▼
      RUN
       │
 ┌─────┼─────┐
 ▼     ▼     ▼
PLAN  POLICY COST
 │
 ▼
APPROVAL
 │
 ▼
APPLY
 │
 ▼
CLOUD
```

---

# 🗺️ 57. Terraform Roadmap

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
16 HCP Terraform                🟢 ← YOU ARE HERE
17 Terraform + GitHub Actions
18 Terraform Security & Policy
19 Multi-Cloud Terraform
20 Enterprise Capstone
```

---

<div align="center">

# ☁️ TERRAFORM AT ENTERPRISE SCALE

### VishwaTech Labs

**Terraform • HCP Terraform • AWS • Azure • GCP • IAM • Docker • GitHub Actions • Kubernetes • Ansible • DevSecOps**

### By Vishwanath Gowda H

⭐ Remote Operations • Governance • Collaboration • Infrastructure as Code

</div>
