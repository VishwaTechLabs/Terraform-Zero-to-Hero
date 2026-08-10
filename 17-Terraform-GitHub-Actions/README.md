<div align="center">

# 🚀 Terraform + GitHub Actions — Complete CI/CD Masterclass

### 🔥 GitHub → OIDC → Terraform → Plan → Approval → Apply → AWS | VishwaTech Labs

[![Terraform](https://img.shields.io/badge/Terraform-844FBA?logo=terraform&logoColor=white)](https://developer.hashicorp.com/terraform/docs)
[![GitHub Actions](https://img.shields.io/badge/GitHub%20Actions-2088FF?logo=githubactions&logoColor=white)](https://docs.github.com/actions)
[![AWS](https://img.shields.io/badge/AWS-FF9900?logo=amazonaws&logoColor=white)](https://aws.amazon.com/)
[![OIDC](https://img.shields.io/badge/OIDC-Passwordless%20Federation-green)](#-oidc-architecture)
[![Security](https://img.shields.io/badge/Security-DevSecOps-red)](#-security-hardening)
[![Labs](https://img.shields.io/badge/Labs-30+-purple)](#-hands-on-labs)

**Build a production-style Infrastructure CI/CD pipeline using GitHub Actions and Terraform — with pull-request plans, OIDC authentication, environment protection, reusable workflows, remote state, approvals, security scanning and controlled production deployment.**

[📘 GitHub Actions](https://docs.github.com/actions) •
[📘 Terraform](https://developer.hashicorp.com/terraform/docs) •
[📘 AWS OIDC](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_providers_create_oidc.html)

</div>

---

# 🎯 Project Goal

Build this enterprise workflow:

```text
Developer
    │
    ▼
GitHub Repository
    │
    ├── Pull Request
    │       │
    │       ▼
    │    Terraform fmt
    │    Terraform validate
    │    Terraform plan
    │    Security checks
    │       │
    │       ▼
    │    PR Review
    │
    ▼
Merge to Main
    │
    ▼
GitHub Actions
    │
    ▼
OIDC Federation
    │
    ▼
AWS IAM Role
    │
    ▼
Terraform
    │
    ▼
Remote State
    │
    ▼
AWS Infrastructure
```

The key principle:

```text
CODE
 ↓
REVIEW
 ↓
PLAN
 ↓
SECURITY
 ↓
APPROVAL
 ↓
APPLY
```

---

# 🧠 1. What Is CI/CD?

CI = Continuous Integration.

CD = Continuous Delivery / Deployment.

For Terraform:

```text
Code Change
    ↓
Validate
    ↓
Plan
    ↓
Review
    ↓
Apply
```

Instead of every engineer manually running:

```bash
terraform apply
```

we create a controlled automation pipeline.

---

# 🚀 2. Why GitHub Actions + Terraform?

Without CI/CD:

```text
Developer Laptop
      ↓
terraform apply
      ↓
AWS
```

Problems:

```text
Manual mistakes
Credential exposure
No consistent process
Weak auditability
Different CLI versions
No mandatory review
```

With GitHub Actions:

```text
GitHub
  ↓
Automated Checks
  ↓
Terraform Plan
  ↓
Review
  ↓
Controlled Apply
```

---

# 🧩 3. GitHub Actions Building Blocks

A workflow commonly contains:

```text
Workflow
   │
   ├── Trigger
   ├── Jobs
   ├── Steps
   ├── Runners
   ├── Permissions
   ├── Environments
   ├── Secrets
   ├── Variables
   └── Artifacts
```

---

# 📁 4. Workflow File

GitHub Actions workflows live under:

```text
.github/
└── workflows/
    └── terraform.yml
```

Example:

```yaml
name: Terraform CI

on:
  pull_request:

jobs:
  terraform:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Setup Terraform
        uses: hashicorp/setup-terraform@v3

      - name: Terraform Init
        run: terraform init

      - name: Terraform Format Check
        run: terraform fmt -check -recursive

      - name: Terraform Validate
        run: terraform validate
```

Pin or review action versions according to your organization's dependency-management policy.

---

# 🔔 5. Workflow Triggers

Common triggers:

```yaml
on:
  push:
  pull_request:
  workflow_dispatch:
```

Other events include:

```text
workflow_call
schedule
release
issues
workflow_run
```

Use the smallest trigger set necessary.

---

# 🌿 6. Pull Request Workflow

Recommended:

```text
Developer
   ↓
Feature Branch
   ↓
Pull Request
   ↓
Terraform Checks
   ↓
Plan
   ↓
Review
   ↓
Merge
```

A PR should normally perform:

```text
fmt
validate
plan
security checks
```

without automatically applying production infrastructure.

---

# 🧪 7. Terraform Format

Command:

```bash
terraform fmt -check -recursive
```

Purpose:

```text
Consistent formatting
Cleaner reviews
CI quality gate
```

To automatically format locally:

```bash
terraform fmt -recursive
```

---

# ✅ 8. Terraform Validate

Command:

```bash
terraform validate
```

Checks Terraform configuration structure and internal consistency.

Typical order:

```text
terraform init
      ↓
terraform fmt
      ↓
terraform validate
      ↓
terraform plan
```

---

# 🔍 9. Terraform Plan

```bash
terraform plan
```

Purpose:

```text
Preview infrastructure changes
```

Example:

```text
Plan:
  + create
  ~ update
  - destroy
```

A plan should be reviewed before production apply.

---

# 💾 10. Plan Artifacts

A plan can be saved:

```bash
terraform plan -out=tfplan
```

Then:

```bash
terraform show tfplan
```

For CI/CD, treat plan files as sensitive infrastructure artifacts.

Do not expose them publicly.

---

# 🔐 11. GitHub Secrets vs Variables

### Variables

Use for:

```text
Non-sensitive configuration
```

### Secrets

Use for:

```text
Sensitive values
```

But with AWS OIDC:

```text
AWS_ACCESS_KEY_ID
AWS_SECRET_ACCESS_KEY
```

do not need to be stored as long-lived GitHub secrets.

---

# 🔥 12. OIDC Architecture

Instead of:

```text
GitHub
   ↓
Long-lived AWS Keys
   ↓
AWS
```

use:

```text
GitHub Actions
      ↓
OIDC Token
      ↓
AWS STS
      ↓
IAM Role
      ↓
Temporary Credentials
      ↓
AWS
```

This is one of the most important security patterns in modern CI/CD.

---

# 🛡️ 13. Why OIDC?

Benefits:

```text
No long-lived AWS access keys
Short-lived credentials
IAM-based authorization
Repository/branch restrictions
Better auditability
Reduced secret-management burden
```

---

# 🔑 14. GitHub Workflow Permissions

A workflow using OIDC needs:

```yaml
permissions:
  id-token: write
  contents: read
```

Example:

```yaml
jobs:
  terraform:
    permissions:
      id-token: write
      contents: read
```

Do not grant broad permissions unless required.

---

# 🏢 15. AWS IAM OIDC Role

Architecture:

```text
GitHub Repository
       │
       ▼
GitHub OIDC Provider
       │
       ▼
AWS IAM Role
       │
       ▼
Temporary Credentials
```

Trust should be restricted to the intended repository and deployment context.

---

# 🔒 16. Trust Policy

Concept:

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

Replace placeholders.

Do not trust:

```text
repo:*/*
```

unless there is an explicitly justified security design.

---

# 🧩 17. Configure AWS Credentials

A common GitHub Actions pattern is:

```yaml
- name: Configure AWS credentials
  uses: aws-actions/configure-aws-credentials@v4
  with:
    role-to-assume: ${{ vars.AWS_ROLE_ARN }}
    aws-region: ${{ vars.AWS_REGION }}
```

The action uses GitHub's OIDC token to obtain temporary AWS credentials when configured correctly.

---

# ☁️ 18. Verify AWS Identity

Add:

```yaml
- name: Verify AWS identity
  run: aws sts get-caller-identity
```

This is extremely useful during initial setup.

Expected flow:

```text
GitHub
 ↓
OIDC
 ↓
IAM Role
 ↓
STS
 ↓
get-caller-identity
```

---

# 🌍 19. Terraform Backend

Terraform state should not live in the Git repository.

Use a remote backend such as:

```text
HCP Terraform
AWS S3 backend
```

depending on your architecture.

Example HCP Terraform:

```hcl
terraform {
  cloud {
    organization = "example-org"

    workspaces {
      name = "network-prod"
    }
  }
}
```

---

# 🪣 20. S3 Backend Architecture

Conceptually:

```text
GitHub Actions
      │
      ▼
Terraform
      │
      ├──────────────► AWS Resources
      │
      ▼
Remote State
      │
      ▼
S3
```

Use appropriate state protection and concurrency controls for your chosen backend.

---

# 🔐 21. State Security

Terraform state can contain sensitive infrastructure information.

Never:

```text
Commit state to Git
Upload state to public storage
Print secrets
Share state casually
```

Use:

```text
Encryption
Least privilege
Versioning
Locking/concurrency
Audit
```

---

# 🏗️ 22. CI Workflow

A good PR workflow:

```text
PR
 │
 ├── checkout
 ├── setup Terraform
 ├── init
 ├── fmt
 ├── validate
 ├── security scan
 └── plan
```

Example:

```yaml
name: Terraform PR

on:
  pull_request:

permissions:
  contents: read

jobs:
  plan:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - uses: hashicorp/setup-terraform@v3

      - run: terraform init

      - run: terraform fmt -check -recursive

      - run: terraform validate

      - run: terraform plan
```

Add cloud authentication only when the plan genuinely requires it.

---

# 🚀 23. Deployment Workflow

Production:

```text
main
 ↓
GitHub Actions
 ↓
OIDC
 ↓
Terraform Init
 ↓
Terraform Plan
 ↓
Approval
 ↓
Terraform Apply
```

Example apply command:

```bash
terraform apply -auto-approve tfplan
```

Use `-auto-approve` only in a controlled CI workflow where the plan has already passed the intended review/approval process.

---

# 🛑 24. Why Approval Matters

Without approval:

```text
Commit
 ↓
Apply
 ↓
Production
```

With approval:

```text
Commit
 ↓
Plan
 ↓
Review
 ↓
Approval
 ↓
Apply
```

Production infrastructure should have an intentional change-control model.

---

# 🌎 25. GitHub Environments

Typical environments:

```text
dev
stage
prod
```

Example:

```yaml
jobs:
  apply:
    environment: production
```

GitHub environment protection can be used to enforce:

```text
Required reviewers
Environment-specific secrets/variables
Deployment controls
```

Configure these controls in the repository settings.

---

# 🔐 26. Environment-Specific Roles

Strong pattern:

```text
GitHub
 │
 ├── dev branch/environment
 │       ↓
 │    Dev Role
 │
 └── main/prod environment
         ↓
      Prod Role
```

Never give development workflows unnecessary production permissions.

---

# 🏢 27. Multi-Account Architecture

Example:

```text
GitHub
  │
  ├── Dev AWS Account
  │      └── DevRole
  │
  ├── Stage AWS Account
  │      └── StageRole
  │
  └── Prod AWS Account
         └── ProdRole
```

Each role should have:

```text
Different trust
Different permissions
Different blast radius
```

---

# 🔄 28. Reusable Workflows

Reusable workflow:

```yaml
on:
  workflow_call:
```

Caller:

```yaml
jobs:
  terraform:
    uses: ./.github/workflows/terraform-reusable.yml
```

Useful for:

```text
Standardization
DRY CI/CD
Multiple environments
Multiple repositories
```

---

# 🧩 29. Workflow Inputs

Reusable workflows can accept:

```text
environment
region
working-directory
role-arn
Terraform version
```

Concept:

```yaml
on:
  workflow_call:
    inputs:
      environment:
        required: true
        type: string
```

---

# 🔢 30. Matrix Strategy

Matrix can test multiple combinations.

Example:

```yaml
strategy:
  matrix:
    terraform:
      - "1.8.x"
      - "1.9.x"
```

Use matrix testing carefully for infrastructure because each combination may have cloud-side effects if it performs real plans/applies.

---

# 🔒 31. Concurrency

Prevent conflicting runs:

```yaml
concurrency:
  group: terraform-production
  cancel-in-progress: false
```

For Terraform, concurrency should be designed around:

```text
Workspace
Environment
State
Deployment
```

Do not blindly cancel an active production deployment.

---

# 📦 32. Artifacts

Artifacts can store:

```text
Plan output
Test reports
Security reports
Logs
Generated documentation
```

Example:

```yaml
- uses: actions/upload-artifact@v4
  with:
    name: terraform-plan
    path: tfplan
```

Be careful: Terraform plan files may contain sensitive information.

---

# 🛡️ 33. Security Scanning

Add infrastructure security tools such as:

```text
Checkov
tfsec / Trivy IaC scanning
TFLint
OPA
Conftest
Terraform validate
```

Tool selection should match your organization's standards.

---

# 🔍 34. TFLint

Purpose:

```text
Terraform linting
Provider-specific checks
Potential configuration mistakes
```

Typical flow:

```text
fmt
 ↓
validate
 ↓
tflint
 ↓
plan
```

---

# 🛡️ 35. Checkov

Checkov can scan Terraform configuration for security/compliance issues.

Example:

```bash
checkov -d .
```

Typical pipeline:

```text
Terraform
   ↓
Checkov
   ↓
Findings
   ↓
Fix
```

---

# 🔐 36. Trivy IaC

Trivy can scan infrastructure-as-code configurations.

Example:

```bash
trivy config .
```

Possible findings:

```text
Public S3
Open Security Group
Weak encryption
Misconfiguration
```

---

# 🧪 37. Security Gate

Example conceptual pipeline:

```text
Terraform fmt
       ↓
Terraform validate
       ↓
TFLint
       ↓
Checkov / Trivy
       ↓
Terraform plan
       ↓
Review
```

A failed security gate should normally stop the deployment according to your policy.

---

# 📋 38. Pull Request Comment

A useful PR experience:

```text
Terraform Plan

+ aws_vpc.main
+ aws_subnet.public
~ aws_security_group.web
- aws_old_resource
```

Reviewers should be able to understand:

```text
What changes?
Why?
How much?
Any security risk?
```

---

# 🧮 39. Plan Exit Codes

Terraform supports detailed exit codes:

```bash
terraform plan -detailed-exitcode
```

Typical meaning:

```text
0 = no changes
1 = error
2 = changes present
```

This can be useful in CI logic.

---

# 🏗️ 40. Complete PR Workflow

```text
                    PULL REQUEST
                         │
                         ▼
                  GitHub Actions
                         │
              ┌──────────┼──────────┐
              ▼          ▼          ▼
             FMT      VALIDATE   SECURITY
              │          │          │
              └──────────┼──────────┘
                         ▼
                       PLAN
                         │
                         ▼
                    PR REVIEW
                         │
                  ┌──────┴──────┐
                  ▼             ▼
                FAIL          PASS
                  │             │
                  ▼             ▼
                FIX           MERGE
                                │
                                ▼
                              APPLY
```

---

# 🔥 41. Complete Production Architecture

```text
Developer
    │
    ▼
Feature Branch
    │
    ▼
Pull Request
    │
    ▼
GitHub Actions
    │
    ├── fmt
    ├── validate
    ├── TFLint
    ├── Checkov
    ├── Trivy
    └── plan
            │
            ▼
       Code Review
            │
            ▼
          Merge
            │
            ▼
     Production Environment
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
      ┌─────┴─────┐
      ▼           ▼
   State         AWS
      │           │
      ▼           ▼
Remote Backend  Resources
```

---

# 📁 42. Project Folder Structure

```text
17-Terraform-GitHub-Actions-CI-CD/
│
├── README.md
│
├── .github/
│   └── workflows/
│       ├── terraform-pr.yml
│       ├── terraform-apply.yml
│       ├── terraform-security.yml
│       └── terraform-reusable.yml
│
├── terraform/
│   ├── versions.tf
│   ├── provider.tf
│   ├── variables.tf
│   ├── locals.tf
│   ├── main.tf
│   ├── outputs.tf
│   └── terraform.tfvars.example
│
├── scripts/
│   ├── terraform-plan.sh
│   ├── terraform-apply.sh
│   └── security-scan.sh
│
└── policies/
    ├── naming/
    ├── security/
    └── tagging/
```

---

# ⚙️ 43. Complete PR Workflow Example

```yaml
name: Terraform PR

on:
  pull_request:

permissions:
  contents: read

jobs:
  terraform:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Setup Terraform
        uses: hashicorp/setup-terraform@v3

      - name: Terraform Init
        run: terraform init

      - name: Terraform Format
        run: terraform fmt -check -recursive

      - name: Terraform Validate
        run: terraform validate

      - name: Terraform Plan
        run: terraform plan
```

For real cloud-backed plans, configure the appropriate authentication and backend access.

---

# 🚀 44. Complete Apply Workflow Example

```yaml
name: Terraform Apply

on:
  push:
    branches:
      - main

permissions:
  id-token: write
  contents: read

jobs:
  apply:
    runs-on: ubuntu-latest
    environment: production

    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Setup Terraform
        uses: hashicorp/setup-terraform@v3

      - name: Configure AWS
        uses: aws-actions/configure-aws-credentials@v4
        with:
          role-to-assume: ${{ vars.AWS_ROLE_ARN }}
          aws-region: ${{ vars.AWS_REGION }}

      - name: Verify Identity
        run: aws sts get-caller-identity

      - name: Terraform Init
        run: terraform init

      - name: Terraform Plan
        run: terraform plan -out=tfplan

      - name: Terraform Apply
        run: terraform apply -auto-approve tfplan
```

This is a teaching example. Production pipelines should add the organization's required security gates, approval model, locking/concurrency and plan/apply controls.

---

# 🛑 45. Important Plan/Apply Design

There are two common models.

### Model A — Plan and Apply in One Run

```text
Plan
 ↓
Approval
 ↓
Apply
```

### Model B — Separate PR Plan and Main Apply

```text
PR
 ↓
Plan
 ↓
Review
 ↓
Merge
 ↓
Main
 ↓
Fresh Plan
 ↓
Apply
```

Model B is common because the infrastructure is re-planned after merge.

Do not blindly apply an old plan if the underlying state/configuration has changed.

---

# 🧠 46. Why OIDC Is Better Than AWS Keys

Old:

```text
GitHub Secret
     ↓
AWS Access Key
     ↓
AWS
```

Modern:

```text
GitHub
   ↓
OIDC
   ↓
STS
   ↓
Temporary Role Credentials
   ↓
AWS
```

Security principle:

```text
Short-lived
+
Least privilege
+
Federated identity
```

---

# 🔐 47. GitHub Actions Hardening

```text
☑ OIDC
☑ Minimal GITHUB_TOKEN permissions
☑ Pin/verify action versions according to policy
☑ Protected branches
☑ Protected environments
☑ Required reviewers
☑ Least-privilege AWS roles
☑ Separate dev/prod roles
☑ Avoid secrets in logs
☑ Dependency updates
☑ Security scanning
☑ Concurrency controls
```

---

# 🧨 48. Dangerous Patterns

Avoid:

```text
❌ AWS access keys in GitHub secrets when OIDC is available
❌ Action with excessive permissions
❌ Production apply on every feature branch
❌ Unrestricted OIDC trust
❌ terraform.tfstate in Git
❌ terraform.tfvars containing secrets
❌ Printing environment variables
❌ Public plan artifacts
❌ Auto-applying unreviewed PRs
❌ One AWS role for every environment
```

---

# 🌍 49. Multi-Environment CI/CD

```text
             GitHub
                │
       ┌────────┼────────┐
       ▼        ▼        ▼
      DEV     STAGE     PROD
       │        │        │
    DevRole  StageRole ProdRole
       │        │        │
       ▼        ▼        ▼
      AWS      AWS       AWS
```

Use separate:

```text
IAM roles
AWS accounts where appropriate
Terraform state
GitHub environments
approval controls
```

---

# 🔄 50. GitHub Actions + HCP Terraform

Two major architectures:

### Architecture A — GitHub Actions Executes Terraform

```text
GitHub
 ↓
GitHub Actions
 ↓
Terraform CLI
 ↓
AWS
```

### Architecture B — GitHub Triggers HCP Terraform

```text
GitHub
 ↓
HCP Terraform
 ↓
Remote Terraform Run
 ↓
AWS
```

Both are valid depending on governance, collaboration and platform requirements.

---

# 🧩 51. GitHub Actions + HCP Terraform

A mature organization may use:

```text
GitHub
  │
  ▼
PR
  │
  ▼
HCP Terraform
  │
  ├── Remote State
  ├── Variables
  ├── RBAC
  ├── Policy
  └── Runs
  │
  ▼
AWS
```

GitHub Actions may still perform:

```text
Linting
Security scanning
Documentation
Unit checks
```

while HCP Terraform owns infrastructure execution.

---

# 📊 52. Deployment Status

A good pipeline should expose:

```text
Plan
Security
Approval
Apply
Result
```

Example:

```text
Terraform PR        ✅
Security Scan       ✅
Plan                ✅
Review              ✅
Production Approval ⏳
Apply               ⏳
```

This gives teams operational visibility.

---

# 🧪 53. Hands-On Labs

## Lab 01 — First Workflow

Create a GitHub Actions workflow.

## Lab 02 — Terraform fmt

Automate formatting checks.

## Lab 03 — Terraform Validate

Add validation.

## Lab 04 — Terraform Plan

Run plan on PR.

## Lab 05 — PR Checks

Protect pull requests.

## Lab 06 — AWS OIDC

Configure GitHub-to-AWS federation.

## Lab 07 — Verify Identity

Run `aws sts get-caller-identity`.

## Lab 08 — Terraform Apply

Deploy a test resource.

## Lab 09 — GitHub Environment

Create `dev` and `prod`.

## Lab 10 — Production Approval

Require a reviewer.

## Lab 11 — Separate IAM Roles

Create DevRole and ProdRole.

## Lab 12 — Remote State

Configure HCP Terraform or S3 backend.

## Lab 13 — Security Scan

Add Checkov.

## Lab 14 — Trivy

Add IaC scanning.

## Lab 15 — TFLint

Add Terraform linting.

## Lab 16 — Artifacts

Upload a test report.

## Lab 17 — Reusable Workflow

Use `workflow_call`.

## Lab 18 — Workflow Inputs

Pass environment/region.

## Lab 19 — Matrix

Test multiple Terraform configurations.

## Lab 20 — Concurrency

Prevent conflicting production runs.

## Lab 21 — Plan Exit Codes

Implement detailed exit-code handling.

## Lab 22 — PR Plan Comment

Publish a controlled plan summary.

## Lab 23 — Multi-Account

Deploy to separate AWS accounts.

## Lab 24 — Security Gate

Fail pipeline on critical IaC findings.

## Lab 25 — HCP Integration

Trigger HCP Terraform from Git workflow.

## Lab 26 — Module Pipeline

Validate private Terraform modules.

## Lab 27 — Dependency Security

Scan GitHub Actions dependencies.

## Lab 28 — Production Pipeline

Build full plan → approval → apply.

## Lab 29 — Disaster Recovery

Test failed deployment recovery.

## Lab 30 — Enterprise Challenge

Build:

```text
GitHub
   │
   ▼
Pull Request
   │
   ├── fmt
   ├── validate
   ├── lint
   ├── security
   └── plan
          │
          ▼
        Review
          │
          ▼
        Merge
          │
          ▼
      Production
       Environment
          │
          ▼
         OIDC
          │
          ▼
       AWS Role
          │
          ▼
       Terraform
          │
          ▼
          AWS
```

---

# 🏆 54. Production Checklist

```text
SOURCE CONTROL
☑ Protected main branch
☑ Pull request reviews
☑ CODEOWNERS where useful
☑ Versioned workflows

AUTHENTICATION
☑ OIDC
☑ No long-lived AWS keys
☑ Separate roles per environment
☑ Least privilege

TERRAFORM
☑ fmt
☑ validate
☑ plan
☑ remote state
☑ state security
☑ controlled apply

SECURITY
☑ Checkov / Trivy
☑ TFLint
☑ Minimal GitHub permissions
☑ Action dependency management

PRODUCTION
☑ GitHub environment
☑ Required reviewers
☑ Approval
☑ Concurrency
☑ Audit trail

OPERATIONS
☑ Logs
☑ Artifacts
☑ Notifications
☑ Rollback/recovery strategy
```

---

# 🚨 55. Troubleshooting

## OIDC AccessDenied

Check:

```text
GitHub workflow permissions
id-token: write
OIDC provider
AWS role trust policy
aud claim
sub claim
Repository
Branch/environment
```

---

## Terraform Init Fails

Check:

```text
Backend
Credentials
State location
Network
Terraform version
Provider versions
```

---

## Terraform Plan Fails

Check:

```text
AWS identity
IAM permissions
Variables
Provider
State
Resource dependencies
```

---

## Apply Fails

Check:

```text
AWS service error
IAM permission
Quota
State
Resource dependency
Concurrent run
```

---

## Environment Approval Not Appearing

Check:

```text
Job environment name
Required reviewers
Repository settings
Workflow permissions
Branch rules
```

---

# 🎓 56. Interview Questions

### Beginner

1. What is GitHub Actions?
2. What is a workflow?
3. What is a job?
4. What is a step?
5. What is a runner?
6. What is a GitHub environment?
7. What are GitHub secrets?
8. What is Terraform plan?
9. What is Terraform apply?
10. What is CI/CD?

### Intermediate

11. How do GitHub Actions authenticate to AWS?
12. What is OIDC?
13. Why is OIDC better than long-lived AWS keys?
14. How do you protect production?
15. How do you implement Terraform PR plans?
16. What is a reusable workflow?
17. What is a matrix?
18. What is concurrency?
19. How do you secure Terraform state?
20. How do you add Checkov/Trivy?

### Advanced

21. Design GitHub → OIDC → AWS Terraform architecture.
22. How would you separate dev/stage/prod?
23. How would you implement cross-account deployment?
24. How would you secure GitHub Actions itself?
25. How would you prevent an untrusted PR from accessing production?
26. How would you design plan/apply separation?
27. How would you handle failed deployments?
28. How would you integrate HCP Terraform?
29. How would you implement policy-as-code?
30. Design an enterprise Infrastructure CI/CD platform.

---

# ⚡ 57. Cheat Sheet

Workflow:

```text
PR
 ↓
fmt
 ↓
validate
 ↓
security
 ↓
plan
 ↓
review
 ↓
merge
 ↓
approval
 ↓
apply
```

OIDC:

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

Terraform:

```bash
terraform fmt -check -recursive
terraform init
terraform validate
terraform plan
terraform apply
```

AWS identity:

```bash
aws sts get-caller-identity
```

Security:

```bash
checkov -d .
trivy config .
```

---

# 🧠 58. Master Mental Model

```text
                  GITHUB
                     │
               Pull Request
                     │
                     ▼
              GITHUB ACTIONS
                     │
       ┌─────────────┼─────────────┐
       ▼             ▼             ▼
      FMT          SECURITY       TEST
       │             │             │
       └─────────────┼─────────────┘
                     ▼
                   PLAN
                     │
                     ▼
                  REVIEW
                     │
                     ▼
                  MERGE
                     │
                     ▼
                ENVIRONMENT
                     │
                     ▼
                    OIDC
                     │
                     ▼
                 AWS IAM
                     │
                     ▼
                  TERRAFORM
                     │
             ┌───────┴───────┐
             ▼               ▼
           STATE            AWS
             │               │
             ▼               ▼
          REMOTE          RESOURCES
```

---

# 🗺️ 59. Terraform Roadmap

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
17 Terraform + GitHub Actions   🟢 ← YOU ARE HERE
18 Terraform Security & Policy
19 Multi-Cloud Terraform
20 Enterprise Capstone
```

---

<div align="center">

# 🚀 INFRASTRUCTURE AS CODE + CI/CD

### VishwaTech Labs

**Terraform • GitHub Actions • AWS • Azure • GCP • IAM • Docker • Kubernetes • Ansible • DevSecOps**

### By Vishwanath Gowda H

⭐ Automation • Security • Cloud • Infrastructure as Code

</div>
