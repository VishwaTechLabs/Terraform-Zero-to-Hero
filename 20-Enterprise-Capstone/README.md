<div align="center">

# 🏆 Terraform Enterprise Capstone — Real-World Cloud Platform

### 🌍 AWS + Azure + GCP • HCP Terraform • GitHub Actions • OIDC • Security • Policy • DevSecOps

[![Terraform](https://img.shields.io/badge/Terraform-844FBA?logo=terraform&logoColor=white)](https://developer.hashicorp.com/terraform/docs)
[![AWS](https://img.shields.io/badge/AWS-FF9900?logo=amazonaws&logoColor=white)](https://aws.amazon.com/)
[![Azure](https://img.shields.io/badge/Azure-0078D4?logo=microsoftazure&logoColor=white)](https://azure.microsoft.com/)
[![GCP](https://img.shields.io/badge/GCP-4285F4?logo=googlecloud&logoColor=white)](https://cloud.google.com/)
[![GitHub Actions](https://img.shields.io/badge/GitHub%20Actions-2088FF?logo=githubactions&logoColor=white)](https://docs.github.com/actions)
[![HCP Terraform](https://img.shields.io/badge/HCP%20Terraform-844FBA)](https://developer.hashicorp.com/terraform/cloud-docs)
[![DevSecOps](https://img.shields.io/badge/DevSecOps-Secure%20by%20Design-red)](#-security-architecture)
[![Capstone](https://img.shields.io/badge/Project-Enterprise%20Capstone-gold)](#-capstone-objective)

**The final Terraform project: design, secure, automate and govern a production-style multi-cloud infrastructure platform using Infrastructure as Code.**

</div>

---

# 🎯 1. Capstone Objective

This project combines the entire Terraform learning path:

```text
Terraform Fundamentals
        ↓
Providers
        ↓
Variables / Outputs
        ↓
Modules
        ↓
State
        ↓
AWS
        ↓
IAM / Security
        ↓
HCP Terraform
        ↓
GitHub Actions
        ↓
OIDC
        ↓
Security Scanning
        ↓
Policy as Code
        ↓
Multi-Cloud
        ↓
Enterprise Architecture
```

The final target:

```text
                         GITHUB
                            │
                            ▼
                     PULL REQUEST
                            │
                            ▼
                    GITHUB ACTIONS
                            │
        ┌───────────────────┼───────────────────┐
        ▼                   ▼                   ▼
      FORMAT             SECURITY              PLAN
        │                   │                   │
        └───────────────────┼───────────────────┘
                            ▼
                         REVIEW
                            │
                            ▼
                         MERGE
                            │
                            ▼
                    APPROVAL / POLICY
                            │
                            ▼
                    FEDERATED IDENTITY
                            │
          ┌─────────────────┼─────────────────┐
          ▼                 ▼                 ▼
         AWS               AZURE              GCP
          │                 │                 │
        VPC/VPC           VNet              VPC
        IAM/RBAC          RBAC              IAM
        Compute           Compute           Compute
        Storage           Storage           Storage
          │                 │                 │
          └─────────────────┼─────────────────┘
                            ▼
                     OBSERVABILITY
                            │
                            ▼
                       GOVERNANCE
```

---

# 🧠 2. Business Scenario

You are the Terraform platform team for:

```text
VishwaTech Global Platform
```

The company operates workloads across:

```text
AWS
Azure
GCP
```

Requirements:

```text
☑ Infrastructure must be managed as code
☑ Developers use GitHub
☑ CI/CD must be automated
☑ Production requires approval
☑ Long-lived cloud keys are prohibited where federation is available
☑ Terraform state must be centralized and protected
☑ Security scanning is mandatory
☑ Policies must be automated
☑ Infrastructure must be reusable
☑ Dev / Stage / Prod must be separated
☑ Cloud access must follow least privilege
☑ Resources must be tagged/labeled
☑ Infrastructure changes must be auditable
```

---

# 🏗️ 3. Target Architecture

```text
                              USERS
                                │
                                ▼
                             GITHUB
                                │
                    ┌───────────┴───────────┐
                    ▼                       ▼
              Pull Requests             Main Branch
                    │                       │
                    ▼                       ▼
              CI VALIDATION            DEPLOYMENT
                    │                       │
          ┌─────────┼─────────┐             │
          ▼         ▼         ▼             ▼
        FMT      SECURITY    PLAN          OIDC
          │         │         │             │
          └─────────┼─────────┘             ▼
                    ▼                   CLOUD ROLE
                 REVIEW                    │
                    │                      ▼
                    ▼                  TERRAFORM
                  MERGE                     │
                    │              ┌───────┼───────┐
                    ▼              ▼       ▼       ▼
               HCP TERRAFORM      AWS    AZURE    GCP
                    │
          ┌─────────┼─────────┐
          ▼         ▼         ▼
       State      Runs      Policy
```

---

# 🌍 4. Cloud Architecture

## AWS

```text
AWS
│
├── VPC
├── Subnets
├── Security Groups
├── IAM
├── EC2
├── S3
├── KMS
└── Logging
```

## Azure

```text
Azure
│
├── Resource Group
├── VNet
├── Subnets
├── NSG
├── RBAC
├── Virtual Machine
├── Storage
└── Key Vault
```

## GCP

```text
GCP
│
├── Project
├── VPC
├── Subnets
├── Firewall
├── IAM
├── Compute Engine
├── Cloud Storage
└── Cloud KMS
```

---

# 🧩 5. Terraform Architecture

```text
root/
│
├── environments/
│   ├── dev/
│   ├── stage/
│   └── prod/
│
├── modules/
│   ├── aws/
│   ├── azure/
│   └── gcp/
│
├── policies/
│
├── security/
│
├── scripts/
│
└── .github/
    └── workflows/
```

---

# 📁 6. Complete Repository Structure

```text
20-Terraform-Enterprise-Capstone/
│
├── README.md
├── LICENSE
├── .gitignore
├── CHANGELOG.md
│
├── docs/
│   ├── architecture.md
│   ├── security.md
│   ├── networking.md
│   ├── operations.md
│   └── disaster-recovery.md
│
├── environments/
│   │
│   ├── dev/
│   │   ├── aws/
│   │   │   ├── versions.tf
│   │   │   ├── provider.tf
│   │   │   ├── backend.tf
│   │   │   ├── variables.tf
│   │   │   ├── main.tf
│   │   │   └── outputs.tf
│   │   │
│   │   ├── azure/
│   │   └── gcp/
│   │
│   ├── stage/
│   │   ├── aws/
│   │   ├── azure/
│   │   └── gcp/
│   │
│   └── prod/
│       ├── aws/
│       ├── azure/
│       └── gcp/
│
├── modules/
│   ├── aws/
│   │   ├── network/
│   │   ├── security/
│   │   ├── compute/
│   │   ├── storage/
│   │   └── logging/
│   │
│   ├── azure/
│   │   ├── network/
│   │   ├── security/
│   │   ├── compute/
│   │   ├── storage/
│   │   └── logging/
│   │
│   └── gcp/
│       ├── network/
│       ├── security/
│       ├── compute/
│       ├── storage/
│       └── logging/
│
├── policies/
│   ├── opa/
│   ├── conftest/
│   └── sentinel/
│
├── security/
│   ├── checkov/
│   ├── trivy/
│   ├── tflint/
│   └── secrets/
│
├── scripts/
│   ├── fmt.sh
│   ├── validate.sh
│   ├── security-scan.sh
│   ├── plan.sh
│   └── destroy.sh
│
└── .github/
    └── workflows/
        ├── terraform-pr.yml
        ├── terraform-security.yml
        ├── terraform-deploy.yml
        └── terraform-reusable.yml
```

---

# 🔐 7. Identity Architecture

The capstone must not depend on committed cloud credentials.

Target:

```text
GitHub Actions
       │
       ▼
     OIDC
       │
 ┌─────┼──────────┐
 ▼     ▼          ▼
AWS   Azure       GCP
Role  Federated   Federated
      Identity    Identity
```

For local development:

```text
Developer
   ↓
Cloud CLI / supported identity
   ↓
Terraform
```

Production:

```text
CI/CD
   ↓
Federated identity
   ↓
Temporary credentials
```

---

# 🛡️ 8. Least Privilege

Create separate deployment identities:

```text
VishwaTechTerraformDev
VishwaTechTerraformStage
VishwaTechTerraformProd
```

Permissions should be scoped to:

```text
Required account/project/subscription
Required resources
Required actions
Required environment
```

Never use:

```text
Administrator everywhere
```

as the permanent solution.

---

# 🌿 9. Environment Strategy

```text
DEV
│
├── Frequent deployment
├── Lower protection
└── Developer access

STAGE
│
├── Testing
├── Security validation
└── Controlled access

PROD
│
├── Strong approval
├── Restricted access
├── Security gates
└── Audit
```

---

# 💾 10. State Strategy

Use separate state boundaries:

```text
DEV
├── AWS Network State
├── AWS Application State
├── Azure Network State
├── Azure Application State
├── GCP Network State
└── GCP Application State

STAGE
└── Similar boundaries

PROD
└── Similar boundaries
```

Do not create one giant state file for the entire enterprise by default.

---

# 🔒 11. State Security Requirements

```text
☑ Remote state
☑ Encryption
☑ Least privilege
☑ Versioning/recovery
☑ Concurrency/locking
☑ Auditability
☑ Restricted access
☑ No state in Git
```

Possible platform:

```text
HCP Terraform
```

or an appropriately secured cloud backend.

---

# ☁️ 12. HCP Terraform Architecture

```text
Organization
│
├── Project: Development
│   ├── aws-dev
│   ├── azure-dev
│   └── gcp-dev
│
├── Project: Staging
│   ├── aws-stage
│   ├── azure-stage
│   └── gcp-stage
│
└── Project: Production
    ├── aws-prod
    ├── azure-prod
    └── gcp-prod
```

Use:

```text
Teams
RBAC
Variable Sets
Remote State
Runs
Policies
Private Modules
```

according to your HCP Terraform plan and organization design.

---

# 🚀 13. CI/CD Architecture

```text
Pull Request
     │
     ▼
GitHub Actions
     │
     ├── Checkout
     ├── Terraform fmt
     ├── Terraform validate
     ├── TFLint
     ├── Checkov
     ├── Trivy
     ├── Secret scan
     └── Terraform plan
              │
              ▼
          PR Review
              │
              ▼
            Merge
              │
              ▼
        Production Gate
              │
              ▼
             OIDC
              │
              ▼
          Terraform Apply
```

---

# 🔥 14. Pull Request Workflow

PR must run:

```text
fmt
 ↓
validate
 ↓
lint
 ↓
security
 ↓
plan
```

Expected result:

```text
Terraform CI        ✅
Terraform Security  ✅
Terraform Plan      ✅
```

Only then should the PR become eligible for merge according to repository rules.

---

# 🛑 15. Production Deployment

Production should use:

```text
main
 ↓
GitHub Environment
 ↓
Required Approval
 ↓
OIDC
 ↓
Terraform
 ↓
Apply
```

Use environment-specific permissions and cloud identities.

---

# 🔐 16. Security Architecture

```text
                 TERRAFORM SECURITY
                         │
        ┌────────────────┼────────────────┐
        ▼                ▼                ▼
      SOURCE           CI/CD             CLOUD
        │                │                │
        ▼                ▼                ▼
   Secret Scan       Checkov           IAM/RBAC
   Lock File         Trivy             Encryption
   Modules           TFLint            Logging
   Providers         OPA               Monitoring
        │                │                │
        └────────────────┼────────────────┘
                         ▼
                       POLICY
```

---

# 🧪 17. Security Gates

Recommended:

```text
Terraform fmt
Terraform validate
TFLint
Checkov
Trivy
Secret scanning
Policy as code
```

Example:

```text
Critical finding
      ↓
Pipeline FAIL
```

Exceptions must be documented and approved.

---

# 📜 18. Policy as Code

Enforce:

```text
☑ Approved regions
☑ Required tags
☑ Encryption
☑ No public databases
☑ Restricted SSH
☑ IAM least privilege
☑ Approved resource sizes
☑ Logging
```

Possible engines:

```text
OPA
Conftest
Sentinel
```

Choose based on platform and governance requirements.

---

# 🏷️ 19. Global Metadata Standard

Every resource should have common logical metadata:

```text
Environment
Application
Owner
Project
CostCenter
ManagedBy
DataClassification
```

Cloud mapping:

```text
AWS → Tags
Azure → Tags
GCP → Labels / Tags as supported
```

---

# 🌍 20. Region Governance

Example:

```text
AWS
eu-north-1

Azure
North Europe

GCP
europe-north1
```

The policy engine should understand provider-specific location names.

---

# 🛡️ 21. Network Security

Required design:

```text
PUBLIC
│
├── Load Balancer
└── Public endpoints only where required

PRIVATE
│
├── Application
├── Kubernetes
└── Internal services

DATA
│
└── Database / sensitive services
```

Never expose databases directly to the Internet without an explicit, justified architecture.

---

# 🔐 22. Encryption

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

Requirements:

```text
Encryption at rest
Encryption in transit
Key access control
Key rotation strategy
Audit
```

---

# 📊 23. Observability

Central architecture:

```text
AWS Logs
Azure Logs
GCP Logs
     │
     ▼
Central Monitoring / SIEM
```

Examples:

```text
AWS CloudTrail
Azure Activity Log
GCP Cloud Audit Logs
```

---

# 🧩 24. Module Standards

Every reusable module should contain:

```text
README.md
main.tf
variables.tf
outputs.tf
versions.tf
```

Recommended:

```text
examples/
tests/
```

where practical.

---

# 📦 25. Module Design Rules

```text
☑ Clear inputs
☑ Clear outputs
☑ Secure defaults
☑ No hardcoded secrets
☑ Provider constraints
☑ Documentation
☑ Versioning
☑ Examples
☑ Tests where appropriate
```

---

# 🧠 26. Multi-Cloud Abstraction Rule

Do not pretend:

```text
EC2 = Azure VM = GCP VM
```

Instead:

```text
Common Business Requirement
          │
          ▼
Cloud-Specific Implementation
```

Example:

```text
"Deploy application compute"

AWS   → EC2
Azure → VM
GCP   → Compute Engine
```

---

# 🔄 27. Change Management

Every infrastructure change:

```text
Issue
 ↓
Branch
 ↓
Code
 ↓
Pull Request
 ↓
Automated Checks
 ↓
Security
 ↓
Plan
 ↓
Review
 ↓
Approval
 ↓
Apply
```

This creates traceability.

---

# 🚨 28. Drift Management

Possible sources:

```text
Manual console changes
Emergency changes
External automation
Cloud-native services
```

Detection:

```text
Scheduled plan
Cloud configuration monitoring
HCP Terraform capabilities where available
```

Response:

```text
Detect
 ↓
Investigate
 ↓
Reconcile
 ↓
Document
```

---

# 💰 29. Cost Governance

Common requirements:

```text
Mandatory CostCenter
Maximum instance classes
Approved regions
Storage lifecycle
Idle resource detection
Production approval
```

Terraform is not a complete FinOps platform, but it can enforce infrastructure guardrails.

---

# 🧨 30. Disaster Recovery

Document:

```text
State recovery
Repository recovery
Cloud credential recovery
Module recovery
Pipeline recovery
Application recovery
```

Questions:

```text
What if GitHub is unavailable?
What if HCP Terraform is unavailable?
What if state is damaged?
What if a cloud account is compromised?
What if deployment fails?
```

---

# 🔥 31. Failure Scenario

Scenario:

```text
Production Terraform Apply
          ↓
Security Group change
          ↓
Unexpected failure
```

Response:

```text
1. Stop further changes
2. Inspect run logs
3. Inspect Terraform state
4. Check cloud state
5. Identify partial changes
6. Recover/reconcile
7. Fix code
8. Re-plan
9. Obtain approval
10. Re-apply
```

Never assume `terraform apply` is an automatic rollback mechanism.

---

# 🧪 32. Testing Strategy

Terraform testing can include:

```text
Formatting
Validation
Linting
Static security scanning
Policy testing
Plan validation
Module tests
Integration testing
Cloud verification
```

Pipeline:

```text
Code
 ↓
Unit-like checks
 ↓
Security
 ↓
Plan
 ↓
Integration
 ↓
Apply
```

---

# 📈 33. Enterprise Repository Governance

Recommended:

```text
CODEOWNERS
Branch protection
Pull request templates
Issue templates
Security policy
Dependency updates
Action version management
Release process
```

---

# 📁 34. Recommended GitHub Files

```text
.github/
│
├── CODEOWNERS
├── pull_request_template.md
├── workflows/
│   ├── terraform-pr.yml
│   ├── terraform-security.yml
│   ├── terraform-deploy.yml
│   └── terraform-reusable.yml
│
└── dependabot.yml
```

---

# 🧩 35. Terraform Standards

Recommended:

```text
terraform fmt
terraform validate
provider constraints
lock file
modules
remote state
secure variables
outputs
documentation
```

---

# 🔐 36. Security Standards

```text
NO HARDCODED CREDENTIALS
NO PUBLIC STATE
NO UNRESTRICTED ADMIN
NO UNREVIEWED PRODUCTION APPLY
NO UNTRUSTED MODULES
NO UNCONTROLLED ACTIONS
NO UNDOCUMENTED EXCEPTIONS
```

---

# 🏗️ 37. Capstone Deliverables

Students must deliver:

```text
☑ GitHub repository
☑ Architecture diagram
☑ Terraform modules
☑ AWS environment
☑ Azure environment
☑ GCP environment
☑ Remote state
☑ GitHub Actions
☑ OIDC
☑ Security scanning
☑ Policy as code
☑ Production approval
☑ Documentation
☑ Troubleshooting guide
☑ Demo
```

---

# 🧪 38. Capstone Milestones

## Milestone 1

Terraform repository:

```text
Structure
Providers
Variables
Outputs
```

## Milestone 2

AWS:

```text
Network
IAM
Compute
Storage
```

## Milestone 3

Azure:

```text
Resource Group
VNet
RBAC
Compute
Storage
```

## Milestone 4

GCP:

```text
Project
VPC
IAM
Compute
Storage
```

## Milestone 5

Modules:

```text
Reusable
Versioned
Documented
```

## Milestone 6

State:

```text
Remote
Encrypted
Restricted
```

## Milestone 7

CI/CD:

```text
GitHub Actions
PR
Plan
Apply
```

## Milestone 8

Identity:

```text
OIDC
Least Privilege
```

## Milestone 9

Security:

```text
Checkov
Trivy
TFLint
Secret Scan
```

## Milestone 10

Governance:

```text
OPA / Sentinel
Tags
Regions
Encryption
```

## Milestone 11

Production:

```text
Approval
Audit
Monitoring
Recovery
```

---

# 🏆 39. Final Demo

Student should demonstrate:

```text
1. Create branch
2. Change Terraform
3. Open PR
4. GitHub Actions starts
5. fmt passes
6. validate passes
7. security scan passes
8. plan generated
9. reviewer reviews
10. merge
11. production approval
12. OIDC authentication
13. Terraform apply
14. AWS/Azure/GCP resources created
15. Show state
16. Show logs
17. Show security controls
18. Show policy result
```

---

# 🎓 40. Evaluation Rubric

| Area | Weight |
|---|---:|
| Terraform structure | 10% |
| Modules | 10% |
| AWS implementation | 10% |
| Azure implementation | 10% |
| GCP implementation | 10% |
| State management | 10% |
| GitHub Actions | 10% |
| OIDC / Identity | 10% |
| Security / Policy | 10% |
| Documentation / Demo | 10% |
| **Total** | **100%** |

---

# 🚨 41. Common Capstone Mistakes

```text
❌ One huge main.tf
❌ One state for everything
❌ Hardcoded credentials
❌ Administrator permissions
❌ No security scan
❌ No approval
❌ No provider lock file
❌ No documentation
❌ Public storage
❌ Public databases
❌ No tagging
❌ No recovery strategy
❌ No module boundaries
```

---

# 🔍 42. Troubleshooting Matrix

| Problem | First Checks |
|---|---|
| Provider authentication | Identity, role, federation |
| Terraform init | Backend, provider, network |
| Plan fails | Variables, IAM, state |
| Apply fails | Cloud error, quota, dependency |
| OIDC fails | Trust policy, audience, subject |
| Security scan fails | Finding, resource, policy |
| Module fails | Inputs, outputs, provider |
| State conflict | Workspace, backend, concurrent run |
| Azure permission denied | RBAC, subscription, scope |
| GCP permission denied | IAM, project, organization policy |

---

# 🧠 43. Enterprise Interview Questions

### Architecture

1. Design Terraform for 500 AWS accounts.
2. How would you structure 1000 workspaces?
3. How would you separate environments?
4. How would you manage multi-cloud state?
5. How would you design Terraform modules?

### Security

6. How would you eliminate long-lived cloud credentials?
7. How would you secure Terraform state?
8. How would you implement policy as code?
9. How would you secure GitHub Actions?
10. How would you manage Terraform supply chain risk?

### Operations

11. How do you detect drift?
12. How do you recover state?
13. How do you handle failed applies?
14. How do you manage emergency changes?
15. How do you design disaster recovery?

### Multi-Cloud

16. How would you authenticate to AWS, Azure and GCP?
17. How would you avoid over-abstraction?
18. How would you handle cross-cloud dependencies?
19. How would you standardize tags?
20. How would you govern regions?

### Platform Engineering

21. How would you build an internal Terraform platform?
22. HCP Terraform vs GitHub Actions — when would you use each?
23. How would you create self-service infrastructure?
24. How would you control module versions?
25. How would you implement enterprise RBAC?

### Senior/Architect

26. Design a global Terraform platform.
27. Design a secure multi-cloud landing zone.
28. Design Terraform for regulated workloads.
29. Design zero-trust CI/CD for infrastructure.
30. Design Terraform governance for a Fortune-500-style organization.

---

# ⚡ 44. Final Command Cheat Sheet

```bash
terraform init
terraform fmt -recursive
terraform fmt -check -recursive
terraform validate
terraform plan
terraform plan -out=tfplan
terraform show tfplan
terraform show -json tfplan > plan.json
terraform apply
terraform destroy
```

Security:

```bash
checkov -d .
trivy config .
tflint
gitleaks detect
```

Git:

```bash
git status
git add .
git commit -m "Add enterprise Terraform capstone"
git push
```

---

# 🏅 45. Final Architecture

```text
                           🌍 ENTERPRISE PLATFORM
                                      │
                                      ▼
                                  GITHUB
                                      │
                                      ▼
                              PULL REQUEST
                                      │
                         ┌────────────┼────────────┐
                         ▼            ▼            ▼
                       FMT         SECURITY       PLAN
                         │            │            │
                         └────────────┼────────────┘
                                      ▼
                                  CODE REVIEW
                                      │
                                      ▼
                                    MERGE
                                      │
                                      ▼
                              HCP TERRAFORM
                                      │
                    ┌─────────────────┼─────────────────┐
                    ▼                 ▼                 ▼
                 REMOTE STATE       POLICY             RBAC
                    │                 │                 │
                    └─────────────────┼─────────────────┘
                                      ▼
                                  APPROVAL
                                      │
                                      ▼
                                     OIDC
                                      │
                    ┌─────────────────┼─────────────────┐
                    ▼                 ▼                 ▼
                   AWS              AZURE              GCP
                    │                 │                 │
                  VPC/IAM          VNet/RBAC          VPC/IAM
                  EC2/S3           VM/Storage         Compute/GCS
                  KMS/Logs         Key Vault/Logs      KMS/Logs
                    │                 │                 │
                    └─────────────────┼─────────────────┘
                                      ▼
                               OBSERVABILITY
                                      │
                                      ▼
                                  GOVERNANCE
```

---

# 🗺️ 46. Complete Terraform Roadmap — FINISHED

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
17 GitHub Actions CI/CD         🟢
18 Security & Policy            🟢
19 Multi-Cloud Terraform        🟢
20 Enterprise Capstone          🟢 ← COMPLETE
```

---

# 🎉 47. Congratulations

You have now completed the full Terraform learning path:

```text
BEGINNER
   ↓
Terraform Fundamentals
   ↓
INTERMEDIATE
   ↓
AWS Infrastructure
   ↓
ADVANCED
   ↓
Modules + State + Security
   ↓
EXPERT
   ↓
HCP Terraform + GitHub Actions
   ↓
DEVSECOPS
   ↓
Multi-Cloud
   ↓
🏆 ENTERPRISE CAPSTONE
```

---

<div align="center">

# 🏆 TERRAFORM ZERO → HERO → ENTERPRISE ARCHITECT

### VishwaTech Labs

**Terraform • AWS • Azure • GCP • HCP Terraform • GitHub Actions • IAM • DevSecOps • Docker • Kubernetes • Ansible**

### By Vishwanath Gowda H

### 🚀 BUILD • SECURE • AUTOMATE • GOVERN

</div>
