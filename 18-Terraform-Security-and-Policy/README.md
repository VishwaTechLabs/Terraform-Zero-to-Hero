<div align="center">

# 🛡️ Terraform Security & Policy — Complete DevSecOps Masterclass

### 🔐 Secure IaC • Policy as Code • SAST for Terraform • Compliance • Supply Chain • State Security | VishwaTech Labs

[![Terraform](https://img.shields.io/badge/Terraform-844FBA?logo=terraform&logoColor=white)](https://developer.hashicorp.com/terraform/docs)
[![Security](https://img.shields.io/badge/DevSecOps-Security%20First-red)](#-terraform-security-mindset)
[![Checkov](https://img.shields.io/badge/Checkov-IaC%20Security-blue)](https://www.checkov.io/)
[![Trivy](https://img.shields.io/badge/Trivy-IaC%20Scanning-1904DA)](https://trivy.dev/)
[![OPA](https://img.shields.io/badge/OPA-Policy%20as%20Code-7D5AA6)](https://www.openpolicyagent.org/)
[![Labs](https://img.shields.io/badge/Labs-30+-success)](#-hands-on-labs)

**Build security into Terraform from day one — scan configuration, enforce policies, protect state, secure credentials, validate providers/modules, detect drift, govern cloud resources and integrate security gates into CI/CD.**

[📘 Terraform Security](https://developer.hashicorp.com/terraform/language/state/sensitive-data) •
[📘 Checkov](https://www.checkov.io/) •
[📘 Trivy](https://trivy.dev/) •
[📘 Open Policy Agent](https://www.openpolicyagent.org/)

</div>

---

# 🎯 Project Goal

Build a secure Infrastructure-as-Code pipeline:

```text
Developer
   │
   ▼
Terraform Code
   │
   ├── Format
   ├── Validate
   ├── Lint
   ├── Security Scan
   ├── Policy Check
   ├── Secret Scan
   └── Dependency Review
   │
   ▼
Terraform Plan
   │
   ▼
Security / Compliance Gate
   │
   ▼
Approval
   │
   ▼
Terraform Apply
   │
   ▼
Cloud
```

Core principle:

```text
SHIFT LEFT
     +
CONTINUOUS SECURITY
     +
POLICY AS CODE
     +
LEAST PRIVILEGE
     +
SECURE STATE
```

---

# 🧠 1. What Is Terraform Security?

Terraform security means protecting the entire Infrastructure-as-Code lifecycle:

```text
Source Code
     ↓
Dependencies
     ↓
Variables
     ↓
Providers
     ↓
State
     ↓
Plan
     ↓
Apply
     ↓
Cloud Resources
```

Security is not only:

```text
"Run a scanner."
```

It includes:

```text
Identity
Secrets
Code
State
Modules
Providers
CI/CD
Policies
Cloud configuration
Monitoring
Drift
```

---

# 🔥 2. Terraform Security Mindset

Think:

```text
Prevent
   ↓
Detect
   ↓
Block
   ↓
Remediate
   ↓
Monitor
```

Example:

```text
Bad S3 configuration
       ↓
Checkov detects
       ↓
CI fails
       ↓
Developer fixes
       ↓
Plan passes
       ↓
Apply
```

---

# 🧱 3. Terraform Security Layers

```text
                 TERRAFORM SECURITY
                         │
       ┌─────────────────┼─────────────────┐
       ▼                 ▼                 ▼
   Developer          CI/CD             Runtime
       │                 │                 │
       ▼                 ▼                 ▼
   IDE / Hooks       Scanners          Cloud Controls
       │                 │                 │
       └─────────────────┼─────────────────┘
                         ▼
                      Policy
                         │
                         ▼
                       State
```

---

# 🔍 4. Security Testing Types

Terraform security can include:

```text
SAST / IaC scanning
Linting
Secret scanning
Dependency scanning
Policy testing
Compliance validation
Plan analysis
State security
Drift detection
Cloud posture validation
```

---

# 🛡️ 5. Checkov

Checkov scans Infrastructure-as-Code for security and compliance issues.

Example:

```bash
checkov -d .
```

Typical findings:

```text
Public S3 bucket
Open Security Group
Unencrypted storage
Missing logging
Weak IAM
```

Use findings as a starting point; validate exceptions against your architecture.

---

# 🔎 6. Trivy IaC

Trivy can scan Terraform and other IaC formats.

Example:

```bash
trivy config .
```

Typical flow:

```text
Terraform
   ↓
Trivy
   ↓
Misconfiguration
   ↓
Fix
```

---

# 🧹 7. TFLint

TFLint focuses on Terraform linting and provider-specific rules.

Example:

```bash
tflint
```

Pipeline:

```text
fmt
 ↓
validate
 ↓
tflint
 ↓
security scan
 ↓
plan
```

---

# 🧠 8. Terraform Validate

```bash
terraform validate
```

This checks Terraform configuration syntax and internal consistency.

It is not a security scanner.

Remember:

```text
validate ≠ security scan
```

---

# 🧩 9. Terraform Format

```bash
terraform fmt -check -recursive
```

Formatting is not security, but consistent code improves:

```text
Review
Maintenance
Automation
Diff quality
```

---

# 🚨 10. Common IaC Security Problems

```text
❌ Public S3 buckets
❌ 0.0.0.0/0 SSH
❌ Unencrypted storage
❌ Public databases
❌ Wildcard IAM
❌ Hardcoded credentials
❌ Open Kubernetes API
❌ Missing logging
❌ Weak TLS
❌ Disabled security controls
```

---

# 🔐 11. Secrets in Terraform

Never hardcode:

```hcl
password = "MyPassword123"
```

or:

```hcl
access_key = "AKIA..."
secret_key = "..."
```

Use:

```text
OIDC
IAM Roles
AWS Secrets Manager
SSM Parameter Store
HCP Terraform sensitive variables
CI/CD secret stores
```

depending on the architecture.

---

# ⚠️ 12. Sensitive Variables

Terraform:

```hcl
variable "db_password" {
  type      = string
  sensitive = true
}
```

Important:

```text
sensitive = true
```

mainly affects display/redaction behavior. It does not guarantee that the value will never exist in Terraform state.

Therefore:

```text
Protect state
```

is critical.

---

# 💾 13. Terraform State Security

Terraform state can contain:

```text
Resource information
Attributes
Sensitive values
Connection details
Identifiers
```

Treat state as sensitive infrastructure data.

Bad:

```text
Git Repository
└── terraform.tfstate
```

Better:

```text
Secure Remote Backend
        │
        ├── Encryption
        ├── Access Control
        ├── Versioning
        └── Concurrency / Locking
```

---

# 🔐 14. Remote State Security

Possible architectures:

```text
HCP Terraform
```

or:

```text
AWS S3
+
appropriate locking/concurrency mechanism
```

Security requirements:

```text
Encryption
Least privilege
Private access
Audit
Recovery
Versioning
```

---

# 🪣 15. S3 State Bucket Security

If using S3 for Terraform state:

```text
☑ Block public access
☑ Encryption
☑ Versioning
☑ Restricted IAM
☑ Secure transport
☑ Logging/auditing where required
☑ Appropriate locking/concurrency
```

Never make Terraform state public.

---

# 🔑 16. AWS KMS

For sensitive Terraform state, KMS can provide customer-managed encryption controls.

Architecture:

```text
Terraform
   ↓
S3
   ↓
KMS
   ↓
Encrypted State
```

Remember:

```text
S3 permissions
+
KMS permissions
```

may both be required.

---

# 👤 17. Least Privilege

Bad:

```text
AdministratorAccess
```

Better:

```text
Only required resources
Only required actions
Only required environment
```

Example:

```text
GitHub Dev Role
    ↓
Dev Account
    ↓
Specific Terraform resources
```

Production:

```text
GitHub Prod Role
    ↓
Prod Account
    ↓
Approved permissions
```

---

# 🔐 18. CI/CD Identity

Avoid:

```text
GitHub
 ↓
Long-lived AWS Access Key
```

Prefer:

```text
GitHub
 ↓
OIDC
 ↓
STS
 ↓
IAM Role
 ↓
Temporary Credentials
```

This should be the default design for new GitHub-to-AWS automation where feasible.

---

# 🧩 19. Provider Security

Terraform providers are software dependencies.

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

Control:

```text
Provider source
Version constraints
Upgrade process
Lock file
Review
```

---

# 🔒 20. `.terraform.lock.hcl`

Terraform uses the dependency lock file to record selected provider versions and package checksums.

Commit:

```text
.terraform.lock.hcl
```

to Git for normal application repositories.

Do not blindly regenerate and ignore dependency changes.

Review provider upgrades.

---

# 📦 21. Module Security

Modules can introduce:

```text
Misconfiguration
Excessive permissions
Insecure defaults
Supply-chain risk
```

Before using a module:

```text
Check source
Check owner
Review code
Review releases
Check versions
Scan
Test
```

---

# 🏪 22. Private Module Registry

Enterprise pattern:

```text
Private Registry
       │
       ├── VPC
       ├── EC2
       ├── IAM
       └── S3
```

Benefits:

```text
Approved modules
Versioning
Central governance
Reuse
Security standards
```

---

# 🚨 23. Module Supply Chain

Think:

```text
Module
  ↓
Dependency
  ↓
Provider
  ↓
Binary
```

Security:

```text
Trust
+
Version
+
Integrity
+
Review
```

Avoid using unknown modules in production without review.

---

# 🔍 24. Provider & Module Dependency Review

Before upgrade:

```text
Current version
      ↓
Release notes
      ↓
Breaking changes
      ↓
Security advisories
      ↓
Test
      ↓
Plan
      ↓
Review
```

Treat infrastructure dependencies like application dependencies.

---

# 📜 25. Policy as Code

Policy as code turns governance into executable rules.

Example:

```text
RULE:
Production S3 buckets
must have encryption.
```

Another:

```text
RULE:
Production EC2
must not have public IP.
```

Another:

```text
RULE:
All resources require Owner tag.
```

---

# 🧠 26. Policy vs Scanner

Scanner:

```text
Finds known problems
```

Policy:

```text
Enforces organization-specific rules
```

Example:

```text
Checkov
   ↓
Detects insecure configuration

OPA
   ↓
Enforces company policy
```

Use both.

---

# 🟣 27. Open Policy Agent

OPA is a general-purpose policy engine.

Architecture:

```text
Terraform Plan / JSON
        ↓
       OPA
        ↓
   Policy Decision
        ↓
 PASS / FAIL
```

OPA uses Rego for policy definitions.

---

# 📝 28. Rego Concept

Example conceptual policy:

```rego
package terraform.security

deny[msg] {
  resource := input.resource_changes[_]
  resource.type == "aws_instance"
  resource.change.after.associate_public_ip_address == true

  msg := "Production EC2 must not have a public IP"
}
```

Real policy input must match the JSON schema produced by your chosen Terraform plan/policy pipeline.

---

# 🧪 29. Conftest

Conftest can test structured configuration using OPA/Rego.

Concept:

```text
Terraform JSON
      ↓
Conftest
      ↓
Rego
      ↓
Pass / Fail
```

Example command:

```bash
conftest test plan.json
```

---

# 🛡️ 30. Sentinel

Sentinel is HashiCorp's policy-as-code framework for supported HashiCorp products/plans.

Concept:

```text
Terraform Plan
      ↓
Sentinel
      ↓
Policy
      ↓
PASS / FAIL
```

Use Sentinel when it fits your HCP Terraform governance model.

---

# 🧱 31. Policy Categories

Enterprise policies can include:

```text
Security
Compliance
Naming
Tagging
Cost
Networking
Data classification
Regions
Encryption
IAM
```

Example:

```text
Allowed regions:
eu-north-1
eu-west-1
```

---

# 🌍 32. Region Policy

Concept:

```text
resource
    ↓
region
    ↓
Allowed?
 ├── YES → PASS
 └── NO  → FAIL
```

This can prevent accidental deployment into unapproved regions.

---

# 🏷️ 33. Tagging Policy

Required:

```text
Environment
Owner
Project
CostCenter
ManagedBy
```

Policy:

```text
Missing Owner
     ↓
FAIL
```

---

# 💰 34. Cost Policy

Example:

```text
Production
+
Large instance
+
No approval
=
FAIL
```

Cost governance can include:

```text
Instance sizes
Database sizes
NAT Gateway usage
GPU resources
Regions
Storage classes
```

---

# 🔐 35. Encryption Policy

Example:

```text
S3
 ↓
Encryption required
```

```text
EBS
 ↓
Encryption required
```

```text
RDS
 ↓
Encryption required
```

---

# 🌐 36. Network Security Policy

Examples:

```text
No public database
No 0.0.0.0/0 SSH
No unrestricted RDP
Approved CIDRs only
Private subnets for sensitive workloads
```

---

# 👥 37. IAM Security Policy

Examples:

```text
No AdministratorAccess for workloads
No wildcard permissions without exception
No long-lived CI credentials
Restricted trust policies
MFA for human privileged access
```

---

# 🔍 38. Terraform Plan as Security Input

Architecture:

```text
Terraform Configuration
        ↓
terraform plan
        ↓
terraform show -json
        ↓
Security / Policy Engines
        ↓
Decision
```

Command:

```bash
terraform show -json tfplan > plan.json
```

Protect:

```text
plan.json
```

because it can expose infrastructure information.

---

# 🧪 39. Security Pipeline

Recommended:

```text
Checkout
   ↓
Terraform fmt
   ↓
Terraform init
   ↓
Terraform validate
   ↓
TFLint
   ↓
Checkov
   ↓
Trivy
   ↓
Secret Scan
   ↓
Terraform Plan
   ↓
OPA / Policy
   ↓
Review
   ↓
Apply
```

---

# 🔎 40. Secret Scanning

Use secret-scanning tools such as:

```text
GitHub Secret Scanning
Gitleaks
TruffleHog
```

Example:

```bash
gitleaks detect
```

If a credential is discovered:

```text
Revoke
Rotate
Investigate
Remove
```

Do not simply delete the line and assume the credential is safe.

---

# 🚨 41. Credential Exposure Response

If AWS credentials are committed:

```text
1. Revoke / rotate
2. Check CloudTrail
3. Identify affected resources
4. Investigate access
5. Remove credential from active use
6. Clean repository history where appropriate
7. Replace with OIDC/role-based access
```

Treat exposed credentials as compromised.

---

# 🔄 42. Drift Detection

Drift occurs when cloud resources change outside Terraform.

Example:

```text
Terraform
   ↓
S3 encryption = enabled
```

Someone manually changes:

```text
S3 encryption = disabled
```

Now:

```text
Terraform state/config
        ≠
Cloud reality
```

---

# 🔍 43. Drift Management

Possible approaches:

```text
Scheduled plan
HCP Terraform drift detection/features where available
Cloud configuration monitoring
AWS Config
Security Hub
```

The goal:

```text
Detect
 ↓
Investigate
 ↓
Remediate
```

---

# 🏗️ 44. Immutable Infrastructure

Prefer:

```text
Build
 ↓
Test
 ↓
Deploy
```

instead of:

```text
SSH
 ↓
Manual modification
 ↓
Unknown state
```

Terraform should manage desired infrastructure state.

---

# 🔒 45. Terraform State Access

State access should be:

```text
Least privilege
Authenticated
Audited
Encrypted
Restricted
```

Example:

```text
Developer
  ↓
Read access only where needed

CI/CD
  ↓
Specific workspace/state permissions
```

---

# 🧨 46. Dangerous Terraform Patterns

```text
❌ Hardcoded secrets
❌ Public state
❌ Public S3
❌ Open SSH
❌ AdministratorAccess everywhere
❌ Unreviewed modules
❌ Unpinned providers
❌ Ignoring lock file
❌ Blind terraform apply
❌ Ignoring security scanner findings
❌ No drift detection
❌ No audit trail
```

---

# 🧰 47. Security Exception Management

Sometimes a scanner finding is intentional.

Example:

```text
Public S3
```

may be intentional for a static website or CDN origin design, depending on architecture.

Do not simply suppress:

```text
skip everything
```

Use:

```text
Documented exception
Business reason
Owner
Expiration date
Compensating controls
```

---

# 📊 48. Severity

Typical categories:

```text
CRITICAL
HIGH
MEDIUM
LOW
INFO
```

Pipeline policy might be:

```text
Critical → Block
High     → Block
Medium   → Review
Low      → Track
Info     → Inform
```

Your organization should define the actual threshold.

---

# 🏢 49. Enterprise Security Architecture

```text
                    GITHUB
                       │
                       ▼
                Pull Request
                       │
                       ▼
              GitHub Actions
                       │
       ┌───────────────┼────────────────┐
       ▼               ▼                ▼
    Secrets          IaC Scan          Lint
    Scan            Checkov/Trivy     TFLint
       │               │                │
       └───────────────┼────────────────┘
                       ▼
                 Terraform Plan
                       │
             ┌─────────┼─────────┐
             ▼         ▼         ▼
            OPA      Sentinel   Cost
             │         │         │
             └─────────┼─────────┘
                       ▼
                    Approval
                       │
                       ▼
                     Apply
                       │
                       ▼
                     CLOUD
                       │
                ┌──────┼──────┐
                ▼      ▼      ▼
              Config  SIEM   CSPM
```

---

# 📁 50. Project Folder Structure

```text
18-Terraform-Security-and-Policy/
│
├── README.md
│
├── terraform/
│   ├── versions.tf
│   ├── provider.tf
│   ├── main.tf
│   ├── variables.tf
│   ├── outputs.tf
│   └── terraform.tfvars.example
│
├── policies/
│   ├── opa/
│   │   └── security.rego
│   ├── conftest/
│   │   └── main.rego
│   └── sentinel/
│       └── policy.sentinel
│
├── security/
│   ├── checkov.yaml
│   ├── trivy.yaml
│   └── tflint.hcl
│
├── scripts/
│   ├── scan.sh
│   ├── plan-json.sh
│   └── policy-check.sh
│
└── .github/
    └── workflows/
        └── terraform-security.yml
```

---

# ⚙️ 51. Security Workflow Example

```yaml
name: Terraform Security

on:
  pull_request:

permissions:
  contents: read

jobs:
  security:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Terraform Setup
        uses: hashicorp/setup-terraform@v3

      - name: Terraform Init
        run: terraform init

      - name: Terraform Format
        run: terraform fmt -check -recursive

      - name: Terraform Validate
        run: terraform validate

      - name: Checkov
        uses: bridgecrewio/checkov-action@v12
        with:
          directory: .

      - name: Trivy
        uses: aquasecurity/trivy-action@master
        with:
          scan-type: config
          scan-ref: .
```

For production, pin actions to trusted versions/SHAs according to your organization's supply-chain policy.

---

# 🔒 52. CI/CD Security

Protect:

```text
GitHub
 ↓
Workflow
 ↓
Actions
 ↓
OIDC
 ↓
AWS Role
```

Controls:

```text
Minimal permissions
Protected branches
Environment approvals
Trusted actions
Dependency review
OIDC restrictions
Secrets protection
```

---

# 🧩 53. Action Supply Chain

GitHub Actions themselves are dependencies.

Risk:

```text
Third-party action
      ↓
Compromised release
      ↓
Workflow executes malicious code
```

Mitigate with:

```text
Trusted actions
Version pinning
SHA pinning where appropriate
Dependabot
Code review
Minimal permissions
```

---

# 📦 54. Terraform Supply Chain

```text
Terraform
   ↓
Provider
   ↓
Module
   ↓
External Dependency
```

Security:

```text
Approved sources
Lock files
Version constraints
Review
Scanning
```

---

# 🔐 55. Secure Provider Example

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

Commit:

```text
.terraform.lock.hcl
```

Review changes to provider versions and checksums.

---

# 🧪 56. Hands-On Labs

## Lab 01 — Terraform Security Baseline

Create secure Terraform standards.

## Lab 02 — Checkov

Scan Terraform.

## Lab 03 — Trivy

Scan IaC.

## Lab 04 — TFLint

Configure linting.

## Lab 05 — Secret Scan

Use Gitleaks.

## Lab 06 — Secure S3

Fix a public bucket.

## Lab 07 — Secure EC2

Remove unrestricted SSH.

## Lab 08 — Secure EBS

Enable encryption.

## Lab 09 — IAM Least Privilege

Remove wildcard access.

## Lab 10 — Secure State

Build protected remote state.

## Lab 11 — KMS

Encrypt state/storage.

## Lab 12 — Provider Lock

Review `.terraform.lock.hcl`.

## Lab 13 — Module Review

Audit an external module.

## Lab 14 — Policy as Code

Write an OPA policy.

## Lab 15 — Rego

Create a security rule.

## Lab 16 — Conftest

Test Terraform JSON.

## Lab 17 — Sentinel

Create a policy concept.

## Lab 18 — Region Policy

Restrict AWS regions.

## Lab 19 — Tag Policy

Require mandatory tags.

## Lab 20 — Encryption Policy

Require encryption.

## Lab 21 — Network Policy

Block open SSH.

## Lab 22 — IAM Policy

Block wildcard permissions.

## Lab 23 — Cost Policy

Restrict oversized resources.

## Lab 24 — Drift

Detect manual cloud changes.

## Lab 25 — Security Exceptions

Create an approved exception workflow.

## Lab 26 — CI Security Gate

Fail GitHub Actions on high-risk findings.

## Lab 27 — Plan JSON

Generate Terraform plan JSON.

## Lab 28 — Policy Pipeline

Connect plan → OPA.

## Lab 29 — Supply Chain

Secure providers/modules/actions.

## Lab 30 — Enterprise Challenge

Build:

```text
GitHub
  ↓
Terraform
  ↓
Secret Scan
  ↓
TFLint
  ↓
Checkov
  ↓
Trivy
  ↓
Plan
  ↓
OPA/Sentinel
  ↓
Approval
  ↓
Apply
  ↓
AWS
```

with:

```text
OIDC
Least Privilege
Remote State
Encryption
Drift Detection
Audit
```

---

# 🏆 57. Production Checklist

```text
CODE
☑ No secrets
☑ Secure modules
☑ Provider constraints
☑ Lock file committed
☑ Code review

IDENTITY
☑ OIDC
☑ Least privilege
☑ Environment-specific roles
☑ No long-lived CI credentials

STATE
☑ Remote backend
☑ Encryption
☑ Access control
☑ Versioning/recovery
☑ Concurrency/locking

SCANNING
☑ Checkov
☑ Trivy
☑ TFLint
☑ Secret scanning

POLICY
☑ Naming
☑ Tags
☑ Encryption
☑ Network
☑ IAM
☑ Region
☑ Cost

CI/CD
☑ Protected branches
☑ Approval
☑ Security gates
☑ Trusted actions
☑ Minimal permissions

OPERATIONS
☑ Drift detection
☑ Cloud audit
☑ Exceptions documented
☑ Remediation process
```

---

# 🚨 58. Troubleshooting

## Checkov Fails

Check:

```text
Finding ID
Resource
Reason
Documentation
Exception requirement
```

Fix configuration rather than blindly suppressing the check.

---

## Trivy Fails

Check:

```text
Severity
Misconfiguration
Resource
Recommended fix
```

---

## OPA Policy Fails

Check:

```text
Input JSON
Rego rule
Resource path
Condition
Expected behavior
```

---

## Terraform Plan Leaks Data

Check:

```text
Logs
Plan artifacts
State
Output variables
CI logs
```

Mask sensitive values and restrict artifact access.

---

## GitHub OIDC Fails

Check:

```text
id-token: write
AWS OIDC provider
Trust policy
aud
sub
Repository
Environment
Role ARN
```

---

# 🎓 59. Interview Questions

### Beginner

1. What is Terraform security?
2. What is IaC scanning?
3. What is Checkov?
4. What is Trivy?
5. What is TFLint?
6. What is policy as code?
7. What is OPA?
8. What is Sentinel?
9. What is Terraform state security?
10. Why should secrets not be hardcoded?

### Intermediate

11. Checkov vs Trivy?
12. TFLint vs Terraform validate?
13. Scanner vs policy engine?
14. What is Rego?
15. What is Terraform plan JSON?
16. How do you secure Terraform state?
17. How do you secure providers/modules?
18. How do you implement secret scanning?
19. How do you prevent public S3?
20. How do you detect drift?

### Advanced

21. Design an enterprise Terraform security pipeline.
22. How would you implement OPA with Terraform?
23. How would you enforce encryption?
24. How would you enforce region restrictions?
25. How would you prevent wildcard IAM?
26. How would you secure GitHub Actions?
27. How would you design Terraform supply-chain security?
28. How would you manage security exceptions?
29. How would you integrate CSPM with Terraform?
30. Design a complete DevSecOps platform for Terraform.

---

# ⚡ 60. Cheat Sheet

Format:

```bash
terraform fmt -check -recursive
```

Validate:

```bash
terraform validate
```

Plan:

```bash
terraform plan -out=tfplan
```

JSON:

```bash
terraform show -json tfplan > plan.json
```

Checkov:

```bash
checkov -d .
```

Trivy:

```bash
trivy config .
```

TFLint:

```bash
tflint
```

Gitleaks:

```bash
gitleaks detect
```

Conftest:

```bash
conftest test plan.json
```

---

# 🧠 61. Master Security Mental Model

```text
                 TERRAFORM
                     │
        ┌────────────┼────────────┐
        ▼            ▼            ▼
      CODE        MODULES       PROVIDERS
        │            │            │
        └────────────┼────────────┘
                     ▼
                  SCAN
                     │
       ┌─────────────┼─────────────┐
       ▼             ▼             ▼
    CHECKOV        TRIVY        TFLINT
       │             │             │
       └─────────────┼─────────────┘
                     ▼
                  PLAN
                     │
                     ▼
                  POLICY
                     │
               ┌─────┴─────┐
               ▼           ▼
             PASS          FAIL
               │
               ▼
            APPROVAL
               │
               ▼
             APPLY
               │
               ▼
             CLOUD
               │
               ▼
         DRIFT / AUDIT
```

---

# 🗺️ 62. Terraform Roadmap

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
18 Terraform Security & Policy  🟢 ← YOU ARE HERE
19 Multi-Cloud Terraform
20 Enterprise Capstone
```

---

<div align="center">

# 🛡️ SHIFT LEFT • SECURE EVERYTHING • GOVERN EVERYTHING

### VishwaTech Labs

**Terraform • DevSecOps • AWS • Azure • GCP • IAM • GitHub Actions • Docker • Kubernetes • Ansible**

### By Vishwanath Gowda H

⭐ Secure Infrastructure • Policy as Code • Cloud Security • Automation

</div>
