<div align="center">

# ☁️ Terraform Remote State & Locking — Complete Masterclass

### 🔐 Team Collaboration • S3 Backend • State Migration • Concurrency • Enterprise Architecture | VishwaTech Labs

[![Terraform](https://img.shields.io/badge/Terraform-844FBA?logo=terraform&logoColor=white)](https://developer.hashicorp.com/terraform/docs)
[![Backend](https://img.shields.io/badge/Backend-Remote%20State-blue)](#-what-is-a-remote-backend)
[![AWS S3](https://img.shields.io/badge/AWS-S3-FF9900?logo=amazons3&logoColor=white)](https://aws.amazon.com/s3/)
[![Security](https://img.shields.io/badge/Security-Enterprise-red)](#-security)
[![Labs](https://img.shields.io/badge/Labs-30+-success)](#-hands-on-labs)

**Take Terraform from a single-laptop workflow to a secure, collaborative, production-ready state architecture.**

[📘 Backend Configuration](https://developer.hashicorp.com/terraform/language/backend) •
[☁️ S3 Backend](https://developer.hashicorp.com/terraform/language/backend/s3) •
[🔐 State](https://developer.hashicorp.com/terraform/language/state)

</div>

---

# 🎯 What You Will Learn

```text
Local State vs Remote State
What Is a Backend?
Why Teams Need Remote State
S3 Backend
Backend Configuration
Partial Backend Configuration
terraform init -migrate-state
State Migration
State Locking
Concurrency
Lock Acquisition
Lock Timeouts
S3 State Security
Encryption
Versioning
IAM
Audit Logging
Environment Isolation
Key Design
Workspace Considerations
CI/CD
GitHub Actions
State Access
Cross-Account Backend
Recovery
Backup
State Rollback
Force Unlock
Troubleshooting
30 Hands-on Labs
Interview Questions
Enterprise Architecture
```

---

# 🧠 1. What Is a Backend?

A Terraform backend determines where Terraform stores state and how Terraform accesses it.

Concept:

```text
Terraform Configuration
        ↓
      Backend
        ↓
      State
```

Local backend:

```text
Developer Machine
      ↓
terraform.tfstate
```

Remote backend:

```text
Terraform
    ↓
Remote State Storage
    ↓
Shared Team State
```

---

# 🆚 2. Local vs Remote State

| Feature | Local | Remote |
|---|---|---|
| Stored on laptop | ✅ | ❌ |
| Team collaboration | Poor | ✅ |
| Centralized access | ❌ | ✅ |
| Access control | Limited | Backend-dependent |
| Locking | Limited/none depending on setup | Backend-dependent |
| CI/CD friendly | Poor | ✅ |
| Enterprise use | Usually not preferred | ✅ |

---

# 🏢 3. Why Remote State?

Imagine:

```text
Developer A
Developer B
GitHub Actions
Jenkins
Terraform Cloud/Enterprise
```

All need the same state.

Instead of:

```text
A → local state
B → different local state
CI → another state
```

use:

```text
                 Remote Backend
                /      |       \
               /       |        \
          Developer   CI/CD   Developer
```

One authoritative state location per state configuration.

---

# ☁️ 4. AWS S3 Backend

A common AWS architecture:

```text
Terraform
    │
    ▼
AWS S3
    │
    ▼
terraform.tfstate
```

Example backend configuration:

```hcl
terraform {
  backend "s3" {
    bucket = "my-terraform-state-bucket"
    key    = "prod/network/terraform.tfstate"
    region = "eu-north-1"
  }
}
```

Use a unique bucket name and an appropriate region for your organization.

---

# ⚠️ 5. Backend Bucket Must Exist First

Important:

```text
Terraform Backend
        ↓
Needs State Storage
```

You generally cannot use the same state you are trying to create as the backend for creating its own backend storage.

Common pattern:

```text
Bootstrap Stack
      ↓
Creates backend resources
      ↓
S3 bucket
      ↓
Application Terraform
      ↓
Uses S3 backend
```

---

# 🏗️ 6. Bootstrap Architecture

```text
              Bootstrap Terraform
                     │
             ┌───────┴────────┐
             ▼                ▼
         S3 Bucket        IAM Controls
             │
             ▼
        Remote State
             │
             ▼
      Main Terraform Code
```

Keep bootstrap configuration separate from the workloads whose state depends on the backend.

---

# 🔐 7. S3 Backend Security

For production, consider:

```text
☑ Block public access
☑ Encryption at rest
☑ Least-privilege IAM
☑ Versioning
☑ Audit logging
☑ Restricted bucket policy
☑ Secure transport
☑ Separate production state
☑ Recovery process
```

---

# 🔒 8. State Encryption

Terraform state may contain sensitive infrastructure data.

Use encryption at rest supported by your backend.

For AWS S3:

```text
S3
 ↓
Encryption
 ↓
State
```

Organizations may use:

```text
SSE-S3
SSE-KMS
```

Choose based on security, compliance, and key-management requirements.

---

# 🗂️ 9. State Key Design

Example:

```hcl
key = "prod/network/terraform.tfstate"
```

Possible organization:

```text
terraform-state-bucket/
│
├── dev/
│   ├── network/
│   └── application/
│
├── stage/
│   ├── network/
│   └── application/
│
└── prod/
    ├── network/
    └── application/
```

The exact structure should reflect your team's ownership and state boundaries.

---

# 🌍 10. Environment Isolation

Recommended conceptual model:

```text
DEV
 └── dev/network/terraform.tfstate

STAGE
 └── stage/network/terraform.tfstate

PROD
 └── prod/network/terraform.tfstate
```

For stronger isolation, use separate cloud accounts and potentially separate backend buckets for critical environments.

---

# 🔐 11. IAM for Backend Access

A Terraform runner may need permission to:

```text
Read state
Write state
List/inspect required backend resources
Acquire/release locking where applicable
```

Do not give every developer:

```text
AdministratorAccess
```

just to access Terraform state.

Follow least privilege.

---

# 🔄 12. Backend Initialization

After configuring a backend:

```bash
terraform init
```

Terraform initializes the backend.

Typical flow:

```text
Configuration
      ↓
terraform init
      ↓
Backend initialization
      ↓
Provider/module initialization
      ↓
Ready
```

---

# 🚚 13. Migrating Local State to Remote

Suppose you currently have:

```text
Local terraform.tfstate
```

and want:

```text
S3 remote state
```

After configuring the backend:

```bash
terraform init -migrate-state
```

Terraform can migrate compatible existing state to the new backend.

Always back up the state before migration.

---

# 🛡️ 14. Migration Safety Checklist

Before migration:

```text
☑ Stop other Terraform operations
☑ Commit/preserve configuration
☑ Back up local state
☑ Create backend storage
☑ Verify IAM permissions
☑ Verify bucket/region
☑ Verify key
☑ Verify encryption
☑ Verify locking/concurrency design
☑ Run terraform init -migrate-state
☑ Run terraform plan
```

Goal:

```text
No unexpected infrastructure changes
```

---

# 🧩 15. Backend Configuration Is Special

Backend configuration is processed very early.

Therefore:

```hcl
terraform {
  backend "s3" {
    bucket = "example"
  }
}
```

cannot generally use ordinary Terraform variables in the way normal resources do.

This is why teams often use:

```text
Partial backend configuration
```

---

# 🧱 16. Partial Backend Configuration

Example:

```hcl
terraform {
  backend "s3" {}
}
```

Then supply values during initialization:

```bash
terraform init \
  -backend-config="bucket=my-state-bucket" \
  -backend-config="key=prod/network/terraform.tfstate" \
  -backend-config="region=eu-north-1"
```

This is useful when backend details vary by environment.

---

# 📁 17. Backend Configuration File

Example:

```text
backend.hcl
```

Contents:

```hcl
bucket = "my-state-bucket"
key    = "prod/network/terraform.tfstate"
region = "eu-north-1"
```

Initialize:

```bash
terraform init \
  -backend-config=backend.hcl
```

Do not place secrets in backend configuration files committed to Git.

---

# ⚠️ 18. Backend Credentials

Avoid:

```text
Hardcoded access keys
Hardcoded secret keys
Passwords in backend files
```

Prefer:

```text
AWS IAM Roles
OIDC
Environment-provided credentials
AWS CLI profiles
CI/CD identity
```

The exact authentication mechanism should match the execution environment.

---

# 🔐 19. GitHub Actions + OIDC

Production CI/CD should avoid long-lived AWS access keys where possible.

Concept:

```text
GitHub Actions
      ↓
OIDC Token
      ↓
AWS IAM Role
      ↓
S3 Backend
      ↓
Terraform State
```

This can eliminate storing long-lived AWS secret keys in GitHub repository secrets.

---

# 🏗️ 20. GitHub Actions State Architecture

```text
GitHub Repository
       │
       ▼
GitHub Actions
       │
       ▼
AWS OIDC
       │
       ▼
IAM Role
       │
       ├──────────────┐
       ▼              ▼
Terraform        S3 Backend
       │              │
       └──────┬───────┘
              ▼
            AWS
```

The same principle can be used with other CI/CD platforms using their supported identity federation mechanisms.

---

# 🔒 21. State Locking

State locking prevents incompatible concurrent Terraform operations against the same state.

Concept:

```text
Terraform A
    ↓
Acquire Lock 🔒
    ↓
Plan/Apply
    ↓
Release Lock
```

Another operation:

```text
Terraform B
    ↓
Attempts operation
    ↓
Lock unavailable
    ↓
Wait / fail according to backend behavior
```

Locking capabilities depend on the backend and Terraform version.

---

# 🆕 22. S3 Locking Considerations

Modern Terraform supports S3 backend locking mechanisms.

Depending on your Terraform version/backend configuration, S3 native lock support can be configured using the current S3 backend documentation.

Example concept:

```hcl
terraform {
  backend "s3" {
    bucket       = "my-state-bucket"
    key          = "prod/network/terraform.tfstate"
    region       = "eu-north-1"
    use_lockfile = true
  }
}
```

**Important:** Backend capabilities evolve. Always verify the exact locking options supported by the Terraform version used by your course/project.

---

# ⚠️ 23. Old DynamoDB Locking Guidance

You may still see tutorials using:

```text
S3 + DynamoDB
```

for Terraform state locking.

This was a common historical architecture.

For new projects, check the current Terraform S3 backend documentation and your Terraform version before choosing DynamoDB-based locking.

Do not blindly copy old tutorials.

---

# ⏳ 24. Lock Timeout

Terraform can wait for a state lock for a specified duration.

Example:

```bash
terraform apply -lock-timeout=5m
```

Concept:

```text
Lock unavailable
      ↓
Wait up to 5 minutes
      ↓
Retry
```

Use an appropriate timeout for your team workflow.

---

# 🚨 25. Force Unlock

Terraform provides:

```bash
terraform force-unlock LOCK_ID
```

This is a recovery operation.

Never use it casually.

Before force-unlocking:

```text
1. Confirm no Terraform operation is running
2. Confirm the lock is stale
3. Identify the correct state
4. Record the incident
5. Force unlock only if necessary
6. Run plan
```

---

# 🧠 26. Concurrency Scenario

Bad:

```text
Developer A
terraform apply
       │
       ▼
   modifies state

Developer B
terraform apply
       │
       ▼
   simultaneous operation
```

Good:

```text
Developer A
      ↓
State Lock 🔒
      ↓
Apply
      ↓
Unlock
      ↓
Developer B
      ↓
Apply
```

---

# 🏢 27. CI/CD Concurrency

Even with Terraform state locking, CI/CD should also control workflow concurrency.

Example conceptual GitHub Actions policy:

```text
Environment:
production

Concurrency:
terraform-prod
```

This helps prevent multiple production workflows from intentionally running simultaneously.

Use both:

```text
CI/CD concurrency control
+
Terraform backend locking
```

---

# 🔐 28. State Access Is Privileged Access

A user who can read Terraform state may gain access to sensitive infrastructure information.

Therefore:

```text
State Read
    ↓
Privileged permission
```

Control:

```text
Who can read?
Who can write?
Who can delete?
Who can administer backend?
```

---

# 🧩 29. Separate Backend Administration

Consider:

```text
Platform Team
    ↓
Owns State Backend

Application Team
    ↓
Uses Specific State

Security Team
    ↓
Audits Access
```

This provides separation of duties.

---

# 🌐 30. Cross-Account Backend

Enterprise model:

```text
AWS Management / Platform Account
          │
          ▼
     State Account
          │
       S3 Bucket
          │
          ▼
     Production State
```

Terraform execution role in another account can assume an approved role to access the backend.

Benefits:

```text
Centralized state
Separate blast radius
Stronger access controls
Central governance
```

---

# 🔑 31. Role Assumption

Concept:

```text
GitHub Actions
      ↓
OIDC
      ↓
Bootstrap/Trust Role
      ↓
Assume Role
      ↓
State Access Role
      ↓
S3
```

Keep trust policies narrow.

---

# 🧱 32. Backend vs Provider

Very important:

```text
Backend
   ↓
Where Terraform state is stored

Provider
   ↓
How Terraform talks to infrastructure APIs
```

Example:

```text
S3 Backend
     +
AWS Provider
```

means:

```text
S3 → state storage

AWS Provider → AWS infrastructure
```

They are separate concepts.

---

# 🔄 33. Backend Migration

Migration examples:

```text
Local
  ↓
S3

S3 Bucket A
  ↓
S3 Bucket B
```

or:

```text
State Key A
  ↓
State Key B
```

Use:

```bash
terraform init
```

and the appropriate migration/reconfiguration flags.

Always back up state first.

---

# 🧠 34. `terraform init -reconfigure`

Use:

```bash
terraform init -reconfigure
```

when you intentionally want Terraform to reinitialize the backend configuration rather than migrate existing state automatically.

Concept:

```text
Backend configuration changed
        ↓
Reinitialize
        ↓
Use new backend configuration
```

Understand the difference:

```text
-migrate-state
      ↓
Move existing state

-reconfigure
      ↓
Reinitialize backend configuration
```

Do not use either blindly.

---

# 📋 35. Backend Migration Decision

```text
Backend changed?
       │
       ├── Need to move existing state?
       │        ↓
       │   -migrate-state
       │
       └── Intentionally reconfigure?
                ↓
           -reconfigure
```

When uncertain, stop and verify the intended state location before continuing.

---

# 🗂️ 36. Workspaces and Remote State

Terraform workspaces can separate state instances within a configuration.

Concept:

```text
workspace: dev
workspace: stage
workspace: prod
```

However, workspaces are not automatically a replacement for strong environment isolation.

For critical production environments, consider:

```text
Separate configurations
Separate state
Separate accounts
Separate IAM
```

based on organizational requirements.

---

# 🏢 37. Recommended Enterprise Boundary

A mature setup might look like:

```text
Repository
│
├── modules/
│
├── environments/
│   ├── dev/
│   │   └── backend
│   │
│   ├── stage/
│   │   └── backend
│   │
│   └── prod/
│       └── backend
│
└── .github/
    └── workflows/
```

---

# 🔐 38. State Bucket Policy Concepts

A production S3 state bucket should consider:

```text
Deny public access
Require secure transport
Restrict principals
Restrict actions
Restrict resources
Enable audit visibility
```

Example policy design should be generated specifically for your account architecture rather than copied blindly.

---

# 🛡️ 39. Versioning and Recovery

Enable S3 versioning where appropriate:

```text
State v1
State v2
State v3
State v4
```

If a bad state write occurs:

```text
Current
   ↓
Problem
   ↓
Previous valid version
```

Recovery must be coordinated carefully with Terraform and the team.

Do not restore a state version without understanding what infrastructure it represents.

---

# 💥 40. State Recovery Procedure

```text
STOP TERRAFORM
      ↓
Identify affected state
      ↓
Identify last known-good version
      ↓
Backup current state
      ↓
Review version metadata
      ↓
Restore carefully
      ↓
Run terraform plan
      ↓
Review EVERY proposed change
      ↓
Apply only after validation
```

---

# 🔍 41. Troubleshooting Backend Initialization

Error:

```text
Failed to configure backend
```

Check:

```text
☑ Bucket exists
☑ Correct region
☑ Correct key
☑ IAM permissions
☑ Authentication
☑ Network connectivity
☑ Backend syntax
☑ Terraform version
☑ Locking configuration
```

---

# 🔍 42. AccessDenied

Possible causes:

```text
Wrong AWS account
Wrong role
Insufficient S3 permissions
Incorrect bucket policy
Incorrect KMS permissions
Expired credentials
OIDC trust failure
```

Check identity:

```bash
aws sts get-caller-identity
```

Then verify backend access.

---

# 🔍 43. State Lock Error

Check:

```text
Is another apply running?
Is CI/CD running?
Is there a stale lock?
Is the backend healthy?
```

Never immediately run:

```bash
terraform force-unlock
```

without investigation.

---

# 🔍 44. Wrong State Key

A common mistake:

```text
dev/network/terraform.tfstate
```

vs:

```text
prod/network/terraform.tfstate
```

Terraform could be operating against the wrong state if backend configuration is incorrect.

Always verify:

```text
Bucket
Key
Region
Account
Workspace
```

before production operations.

---

# 🧪 45. Hands-On Labs

## Lab 01 — Local Backend

Create a basic local state workflow.

## Lab 02 — S3 Backend Bootstrap

Create a dedicated test backend bucket.

## Lab 03 — Configure S3 Backend

Configure:

```text
bucket
key
region
```

## Lab 04 — Migrate Local → S3

Use:

```bash
terraform init -migrate-state
```

## Lab 05 — Verify Remote State

Inspect the S3 object.

## Lab 06 — Enable Versioning

Enable versioning on the test backend.

## Lab 07 — Encryption

Configure appropriate encryption.

## Lab 08 — IAM Least Privilege

Create a restricted backend access role.

## Lab 09 — Backend Key Design

Create:

```text
dev/network
stage/network
prod/network
```

## Lab 10 — Multiple State Files

Separate:

```text
network
application
database
```

## Lab 11 — State Locking

Test concurrent operations safely.

## Lab 12 — Lock Timeout

Test:

```bash
terraform apply -lock-timeout=2m
```

## Lab 13 — Stale Lock Recovery

Simulate and recover from a lab-only stale lock.

## Lab 14 — Force Unlock

Practice only on disposable state.

## Lab 15 — Backend Reconfiguration

Test:

```bash
terraform init -reconfigure
```

## Lab 16 — Backend Migration

Move a test state to another key.

## Lab 17 — S3 Key Separation

Create isolated environment keys.

## Lab 18 — Cross-Account Backend

Access state through an assumed role.

## Lab 19 — GitHub OIDC

Configure GitHub Actions to assume AWS IAM role.

## Lab 20 — GitHub Actions + S3 Backend

Run Terraform with remote state.

## Lab 21 — CI/CD Concurrency

Prevent simultaneous production workflows.

## Lab 22 — State Access Audit

Review backend access logs.

## Lab 23 — KMS Permissions

Test encrypted backend access.

## Lab 24 — State Recovery

Restore a test backend version.

## Lab 25 — Wrong Key Exercise

Intentionally configure the wrong test key and diagnose it.

## Lab 26 — Workspace Experiment

Compare workspaces with separate state configurations.

## Lab 27 — Environment Isolation

Build:

```text
dev
stage
prod
```

state boundaries.

## Lab 28 — Backend Bootstrap

Create reusable backend bootstrap Terraform.

## Lab 29 — Production Readiness Review

Audit:

```text
IAM
Encryption
Locking
Versioning
Recovery
CI/CD
```

## Lab 30 — Enterprise Challenge

Build:

```text
GitHub
   ↓
GitHub Actions
   ↓
OIDC
   ↓
AWS IAM
   ↓
Remote S3 State
   ↓
Locking
   ↓
Terraform
   ↓
AWS Infrastructure
```

---

# 🏆 46. Production Checklist

```text
BACKEND
☑ Remote backend
☑ Correct bucket
☑ Correct key
☑ Correct region
☑ Correct account

SECURITY
☑ Encryption
☑ Least privilege
☑ No public access
☑ Secure transport
☑ Audit logging
☑ KMS controls where required

STATE
☑ Versioning
☑ Backup/recovery
☑ Environment isolation
☑ No Git commits
☑ Restricted reads

CONCURRENCY
☑ State locking
☑ CI/CD concurrency
☑ No parallel production applies

CI/CD
☑ OIDC
☑ Short-lived credentials
☑ Protected environments
☑ Approval gates

OPERATIONS
☑ Migration procedure
☑ Recovery procedure
☑ Incident procedure
☑ Tested restore
```

---

# 🚫 47. Never Do These

```text
❌ Store state in Git
❌ Use public S3 state buckets
❌ Hardcode AWS secret keys
❌ Give AdministratorAccess unnecessarily
❌ Force-unlock blindly
❌ Restore state without a plan
❌ Mix dev/prod state accidentally
❌ Run two production applies intentionally
❌ Put backend secrets in committed files
❌ Assume old locking tutorials are current
```

---

# 🧠 48. Backend Mental Model

Remember:

```text
                 TERRAFORM
                     │
          ┌──────────┴──────────┐
          ▼                     ▼
      Configuration          Backend
          │                     │
          ▼                     ▼
       Desired               State
          │                     │
          └──────────┬──────────┘
                     ▼
                   PLAN
                     │
                     ▼
                   APPLY
                     │
                     ▼
              Cloud Provider
```

---

# 🆚 49. Backend vs State vs Lock

| Concept | Purpose |
|---|---|
| Backend | Defines where/how state is stored |
| State | Tracks managed infrastructure |
| Lock | Coordinates concurrent operations |
| Provider | Talks to infrastructure APIs |
| Configuration | Declares desired infrastructure |

Mental shortcut:

```text
Backend = Where
State   = What Terraform remembers
Lock    = Who can operate now
Provider= How Terraform talks to cloud
```

---

# 🎓 50. Interview Questions

### Beginner

1. What is a Terraform backend?
2. Local vs remote backend?
3. Why do teams use remote state?
4. What is an S3 backend?
5. What is state locking?
6. What is a state key?
7. What is `terraform init`?
8. What is `-migrate-state`?
9. What is `-reconfigure`?
10. Why should state not be stored in Git?

### Intermediate

11. How do you migrate local state to S3?
12. How do you secure an S3 backend?
13. What is partial backend configuration?
14. How do you use `-backend-config`?
15. What is lock timeout?
16. Why is `force-unlock` dangerous?
17. How do you isolate dev and prod state?
18. What is backend vs provider?
19. How does versioning help recovery?
20. How does CI/CD interact with remote state?

### Advanced

21. Design an enterprise S3 backend.
22. How would you implement cross-account state access?
23. How would you secure state with KMS?
24. How would you migrate a production backend safely?
25. How would you recover from a bad state version?
26. How would you prevent concurrent GitHub Actions applies?
27. Why shouldn't workspaces automatically be treated as security boundaries?
28. How would you design state keys for hundreds of applications?
29. How would you implement least privilege for state access?
30. Design a production Terraform backend architecture.

---

# ⚡ 51. Cheat Sheet

```hcl
terraform {
  backend "s3" {
    bucket       = "my-state-bucket"
    key          = "prod/network/terraform.tfstate"
    region       = "eu-north-1"
    use_lockfile = true
  }
}
```

Initialize:

```bash
terraform init
```

Migrate:

```bash
terraform init -migrate-state
```

Reconfigure:

```bash
terraform init -reconfigure
```

Backend config:

```bash
terraform init \
  -backend-config=backend.hcl
```

Lock timeout:

```bash
terraform apply -lock-timeout=5m
```

Advanced recovery:

```bash
terraform force-unlock LOCK_ID
```

AWS identity:

```bash
aws sts get-caller-identity
```

---

# 🗺️ 52. Terraform Roadmap

```text
01 Terraform Fundamentals       🟢
        ↓
02 Terraform Installation       🟢
        ↓
03 Terraform CLI & Workflow     🟢
        ↓
04 Providers & Authentication   🟢
        ↓
05 Variables & Outputs          🟢
        ↓
06 Resources & Data Sources     🟢
        ↓
07 Expressions & Functions      🟢
        ↓
08 Meta-Arguments               🟢
        ↓
09 State & State Management     🟢
        ↓
10 Remote State & Locking       🟢 ← YOU ARE HERE
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

# ☁️ LOCAL IS FOR LEARNING • REMOTE IS FOR COLLABORATION

### VishwaTech Labs

**Terraform • AWS • Azure • GCP • Docker • GitHub Actions • Kubernetes • Ansible • DevSecOps**

### By Vishwanath Gowda H

⭐ Secure the State • Lock the Workflow • Automate the Infrastructure

</div>
