<div align="center">

# 🧠 Terraform State & State Management — Complete Masterclass

### 🔐 The Source of Terraform's Infrastructure Memory | VishwaTech Labs

[![Terraform](https://img.shields.io/badge/Terraform-844FBA?logo=terraform&logoColor=white)](https://developer.hashicorp.com/terraform/docs)
[![State](https://img.shields.io/badge/State-Management-blue)](#-what-is-terraform-state)
[![Security](https://img.shields.io/badge/State-Security-red)](#-state-security)
[![Labs](https://img.shields.io/badge/Labs-30+-success)](#-hands-on-labs)
[![Production](https://img.shields.io/badge/Production-Ready-purple)](#-enterprise-state-architecture)

**Understand Terraform State from absolute zero to enterprise production architecture.**

[📘 State](https://developer.hashicorp.com/terraform/language/state) •
[🔧 CLI State Commands](https://developer.hashicorp.com/terraform/cli/commands/state) •
[📥 Import](https://developer.hashicorp.com/terraform/language/import)

</div>

---

# 🎯 What You Will Learn

```text
What is Terraform State?
Why State Exists
terraform.tfstate
State Structure
Resource Tracking
Resource Addresses
State vs Configuration
State vs Infrastructure
terraform show
terraform state list
terraform state show
terraform state mv
terraform state rm
terraform state pull
terraform state push
State Locking
State Drift
Refresh Concepts
Import
State Security
Sensitive Values
Backups
Remote State
State Backend Concepts
Team Collaboration
State Recovery
State Migration
State Refactoring
State Splitting
State Isolation
CI/CD
Production Architecture
30 Hands-on Labs
Troubleshooting
Interview Questions
```

---

# 🧠 1. What Is Terraform State?

Terraform state is the information Terraform uses to track infrastructure objects it manages.

Conceptually:

```text
Terraform Configuration
        +
Provider APIs
        +
Terraform State
        ↓
Terraform Plan
```

State helps Terraform understand:

```text
What Terraform manages
        ↓
Which real-world object corresponds
        ↓
What attributes are known
        ↓
What changes may be required
```

---

# 🏗️ 2. Why Does Terraform Need State?

Imagine Terraform created:

```text
AWS VPC
AWS Subnet
AWS EC2
AWS S3 Bucket
```

Terraform needs a record connecting:

```text
Terraform Address
        ↓
Real Infrastructure Object
```

For example:

```text
aws_vpc.main
      ↓
VPC ID: vpc-xxxxxxxx
```

Without state, managing complex infrastructure efficiently would be much harder.

---

# 🔗 3. Configuration vs State vs Infrastructure

Three important concepts:

```text
             Terraform
                 │
       ┌─────────┼─────────┐
       ▼         ▼         ▼
 Configuration State   Infrastructure
       │         │         │
       │         │         │
      Desired   Memory     Actual
       │         │         │
       └─────────┼─────────┘
                 ▼
              Plan
```

### Configuration

What you declare.

### State

What Terraform remembers about managed objects.

### Infrastructure

What actually exists in the provider.

---

# 📄 4. `terraform.tfstate`

Local Terraform commonly creates:

```text
terraform.tfstate
```

It contains structured state information.

Typical workflow:

```bash
terraform init
terraform plan
terraform apply
```

After apply:

```text
terraform.tfstate
```

may be updated.

---

# ⚠️ 5. Never Commit State to Git

Do not normally commit:

```text
terraform.tfstate
terraform.tfstate.*
```

to source control.

Why?

State can contain:

```text
Resource attributes
IDs
Configuration-derived values
Sensitive values
Provider data
```

Even when Terraform marks a value as sensitive, that does not mean the value is absent from state.

---

# 🛡️ 6. `.gitignore`

Example:

```gitignore
.terraform/
*.tfstate
*.tfstate.*
crash.log
crash.*.log
*.tfvars
*.tfvars.json
```

However, do not blindly ignore every `.tfvars` file if your repository intentionally contains non-sensitive example variable files.

A safer team policy is:

```text
Never commit secrets
Protect state
Review variable files
```

---

# 🧩 7. State File Conceptual Structure

Terraform state contains information representing managed resources.

Conceptually:

```text
State
│
├── Terraform metadata
│
├── Provider information
│
├── Resources
│   ├── VPC
│   ├── Subnet
│   ├── EC2
│   └── S3
│
└── Resource Instances
```

Do not manually edit the JSON state file unless you have a very specific recovery procedure and understand the consequences.

Prefer Terraform state commands or supported migration mechanisms.

---

# 🏷️ 8. Resource Addresses in State

Example configuration:

```hcl
resource "aws_vpc" "main" {
  cidr_block = "10.0.0.0/16"
}
```

Address:

```text
aws_vpc.main
```

With:

```hcl
count = 2
```

addresses become:

```text
aws_vpc.main[0]
aws_vpc.main[1]
```

With:

```hcl
for_each = {
  dev  = {}
  prod = {}
}
```

addresses become:

```text
aws_vpc.main["dev"]
aws_vpc.main["prod"]
```

---

# 🔍 9. List Resources in State

Run:

```bash
terraform state list
```

Example:

```text
aws_vpc.main
aws_subnet.public
aws_instance.web
aws_s3_bucket.logs
```

This is one of the most useful state troubleshooting commands.

---

# 🔎 10. Show a Resource in State

Run:

```bash
terraform state show aws_vpc.main
```

This displays the state information Terraform has for that resource.

Useful for:

```text
Troubleshooting
Resource inspection
Address verification
Migration planning
```

---

# 📊 11. `terraform show`

Run:

```bash
terraform show
```

This displays the current state or a plan in a human-readable form depending on what you provide.

For JSON:

```bash
terraform show -json
```

Useful for automation and analysis.

---

# 📥 12. Pull State

For supported backends:

```bash
terraform state pull
```

This retrieves the current state representation.

Be careful:

```text
State may contain sensitive information.
```

Do not casually paste state output into:

```text
GitHub issues
Chat
Logs
Screenshots
Public websites
```

---

# 📤 13. Push State

Terraform provides:

```bash
terraform state push
```

This is an advanced operation.

It can overwrite remote state and therefore should be treated as a recovery/admin operation, not a normal workflow.

Before using it:

```text
Backup
Review
Verify
Coordinate
```

---

# 🔄 14. State Refresh Concept

Terraform needs current information from providers when planning.

Modern Terraform workflows should be understood in terms of:

```text
Configuration
   +
State
   +
Provider observations
   ↓
Plan
```

Do not build operational processes around the old standalone `terraform refresh` workflow.

Use current Terraform plan/apply behavior and provider refresh semantics as documented for your Terraform version.

---

# 🌪️ 15. What Is Drift?

Drift occurs when real infrastructure changes outside Terraform's expected configuration/state workflow.

Example:

```text
Terraform
   ↓
EC2 instance_type = t3.micro
```

Someone manually changes it in AWS:

```text
t3.medium
```

Now:

```text
Configuration ≠ Actual Infrastructure
```

Terraform may detect a difference during planning.

---

# 🔍 16. Drift Detection

Run:

```bash
terraform plan
```

Terraform compares:

```text
Configuration
State
Provider observations
```

and determines whether changes are required.

Potential result:

```text
~ update
```

or another appropriate action.

---

# 🧠 17. State Drift vs Configuration Drift

### State drift

Terraform's stored understanding differs from reality.

### Configuration drift

Real infrastructure differs from the declared desired configuration.

In practice, Terraform planning and provider refresh behavior work together to reconcile Terraform's understanding with observed infrastructure.

---

# 🛠️ 18. Drift Example

Configuration:

```hcl
resource "aws_instance" "web" {
  instance_type = "t3.micro"
}
```

Manual AWS change:

```text
t3.micro
   ↓
t3.medium
```

Run:

```bash
terraform plan
```

Terraform may propose:

```text
~ instance_type = "t3.medium" -> "t3.micro"
```

Exact behavior depends on the provider and resource.

---

# 🚨 19. Why Manual Changes Are Dangerous

If Terraform is the owner:

```text
Terraform
   ↓
Source of desired configuration
```

Manual changes create:

```text
Two sources of truth
```

Better:

```text
Git
 ↓
Terraform
 ↓
Infrastructure
```

If emergency manual changes are unavoidable:

```text
Document
Review
Reconcile
```

---

# 🔐 20. State Security

Treat Terraform state as sensitive infrastructure data.

Protect:

```text
Confidentiality
Integrity
Availability
```

### Confidentiality

Restrict who can read state.

### Integrity

Prevent unauthorized modification.

### Availability

Ensure state can be recovered.

---

# 🏦 21. Remote State

For team environments, local state is usually not sufficient.

Instead:

```text
Developer
     │
CI/CD ───────┐
     │       │
     ▼       ▼
      Remote Backend
            │
            ▼
          State
```

Benefits can include:

```text
Centralized state
Team collaboration
Access control
Durability
Locking support
```

The exact features depend on the selected backend.

---

# ☁️ 22. AWS S3 Backend Concept

A common AWS architecture is:

```text
Terraform
   ↓
S3 Backend
   ↓
terraform.tfstate
```

Use an appropriate locking strategy supported by the current Terraform/S3 backend capabilities and your Terraform version.

Do not assume old DynamoDB locking guidance is always the current recommendation.

---

# 🔐 23. Remote State Security

For an AWS backend, consider:

```text
S3 encryption
Bucket access policies
IAM least privilege
Versioning
Restricted network/access paths
CloudTrail
Backup/recovery
State locking
```

Never make a state bucket public.

---

# 🧱 24. State Isolation

Do not put everything into one giant state file.

Better:

```text
Networking State
      │
      ├── VPC
      ├── Subnets
      └── Routing

Application State
      │
      ├── EC2
      ├── ECS/EKS
      └── Load Balancer

Data State
      │
      ├── Database
      └── Storage
```

This can improve:

```text
Blast-radius control
Team ownership
Performance
Security
Change isolation
```

---

# 🏢 25. Environment Isolation

A production design may separate:

```text
DEV
 │
 └── State

STAGING
 │
 └── State

PROD
 │
 └── State
```

Often, separate cloud accounts are even better:

```text
AWS Dev Account
AWS Stage Account
AWS Prod Account
```

Each environment can then have independently protected state.

---

# 🔒 26. State Locking

State locking helps prevent multiple Terraform operations from modifying the same state concurrently.

Concept:

```text
Developer A
    │
    ▼
 State Lock 🔒
    │
    ├── Terraform Apply
    │
    ▼
 Unlock
```

Developer B:

```text
Terraform Apply
      ↓
State already locked
      ↓
Wait / fail according to backend behavior
```

Locking support depends on the backend.

---

# ⚠️ 27. Why Locking Matters

Without appropriate coordination:

```text
Engineer A
    ↓
reads state

Engineer B
    ↓
reads state

Engineer A
    ↓
writes

Engineer B
    ↓
writes
```

Potential result:

```text
State conflict
Lost update
Corruption risk
Unexpected infrastructure actions
```

Use a backend and workflow that provide appropriate locking/concurrency control.

---

# 🧪 28. State Lock Testing

Never experiment with destructive locking operations against production.

Use:

```text
Dedicated lab backend
Two terminals
Two test users/workflows
```

Observe:

```text
Lock acquired
Operation running
Second operation blocked/rejected
Lock released
```

---

# 📥 29. Importing Existing Infrastructure

Suppose AWS already contains:

```text
VPC
```

but Terraform does not manage it.

Modern Terraform supports configuration-driven import.

Concept:

```text
Existing Resource
      ↓
Import
      ↓
Terraform State
      ↓
Configuration
      ↓
Managed Resource
```

Reference:

[Terraform Import](https://developer.hashicorp.com/terraform/language/import)

---

# 🧩 30. Import Block

Example concept:

```hcl
import {
  to = aws_vpc.main
  id = "vpc-xxxxxxxx"
}
```

Then define the corresponding resource:

```hcl
resource "aws_vpc" "main" {
  # configuration
}
```

The exact resource arguments required depend on the provider/resource.

---

# 🔎 31. Import Is Not Magic

Importing a resource into state does not automatically mean your configuration is correct.

After import:

```bash
terraform plan
```

You must reconcile:

```text
Existing Infrastructure
        ↓
State
        ↓
Configuration
```

Goal:

```text
terraform plan
       ↓
No unexpected changes
```

---

# 🧠 32. `terraform state mv`

This command changes a resource's address in state.

Example:

```bash
terraform state mv \
  aws_instance.old \
  aws_instance.web
```

Use cases:

```text
Refactoring
Renaming
Moving resources
Changing module structure
```

For configuration refactoring, prefer declarative `moved` blocks when appropriate.

---

# 🗑️ 33. `terraform state rm`

Example:

```bash
terraform state rm aws_instance.web
```

This removes the object from Terraform state.

Important:

```text
State removal
      ≠
Infrastructure destruction
```

The real resource may continue to exist.

After removing it from state:

```text
Terraform no longer manages that object
```

unless it is later imported or otherwise managed.

---

# ⚠️ 34. State RM Warning

Never use:

```bash
terraform state rm
```

just because you want to delete infrastructure.

To destroy infrastructure:

```bash
terraform destroy
```

or an appropriate targeted/configuration change when justified.

---

# 🧰 35. Useful State Commands

```bash
terraform state list
terraform state show ADDRESS
terraform state mv SOURCE DESTINATION
terraform state rm ADDRESS
terraform state pull
terraform state push FILE
```

Always verify the command against the current Terraform CLI documentation before performing sensitive state administration.

---

# 🧠 36. State vs `terraform show`

### State list

```bash
terraform state list
```

Answers:

```text
What addresses are in state?
```

### State show

```bash
terraform state show aws_vpc.main
```

Answers:

```text
What does Terraform know about this object?
```

### Show

```bash
terraform show
```

Answers:

```text
What does the current state/plan contain?
```

---

# 🧱 37. State and Modules

Suppose:

```hcl
module "network" {
  source = "./modules/network"
}
```

Resource address:

```text
module.network.aws_vpc.main
```

With `for_each`:

```text
module.network["prod"].aws_vpc.main
```

State tracks the complete address.

---

# 🔄 38. Refactoring State

Suppose:

```text
aws_instance.web
```

moves into:

```text
module.app.aws_instance.web
```

If the underlying infrastructure should remain the same, use a controlled state/address migration.

A declarative approach:

```hcl
moved {
  from = aws_instance.web
  to   = module.app.aws_instance.web
}
```

Then:

```bash
terraform plan
```

should show an address move rather than unnecessary destruction/recreation when the migration is correct.

---

# 🧠 39. State Splitting

Large state:

```text
Everything
   ↓
One huge state file
```

can become difficult to manage.

Possible split:

```text
network.tfstate
security.tfstate
data.tfstate
application.tfstate
```

Benefits:

```text
Smaller blast radius
Faster operations
Separate ownership
Reduced contention
```

But splitting also introduces dependencies and operational complexity.

Do it deliberately.

---

# 🔗 40. Remote State Consumption

Terraform can consume outputs from another state using a supported remote-state data source.

Concept:

```text
Network State
      │
      ▼
VPC ID Output
      │
      ▼
Application Configuration
```

Use this carefully because it creates a coupling between state configurations.

Prefer well-defined interfaces and modules when they provide a cleaner architecture.

---

# 🏢 41. Enterprise State Architecture

```text
                         Git
                          │
                          ▼
                    Terraform Code
                          │
                 ┌────────┴────────┐
                 ▼                 ▼
               CI/CD           Developers
                 │                 │
                 └────────┬────────┘
                          ▼
                    Remote Backend
                          │
                    ┌─────┴─────┐
                    ▼           ▼
                 State       Locking
                    │
             ┌──────┴──────┐
             ▼             ▼
           DEV           PROD
          State          State
```

---

# 🔐 42. Enterprise Security Controls

```text
☑ Private state storage
☑ Encryption at rest
☑ Encryption in transit
☑ IAM least privilege
☑ MFA where appropriate
☑ Audit logging
☑ State versioning
☑ Backup/recovery
☑ Locking/concurrency
☑ Separate production state
☑ CI/CD approvals
☑ No state in Git
☑ Restricted state downloads
```

---

# 🚨 43. State as a Secret Boundary

Remember:

```text
Terraform configuration
        ↓
may contain references

Terraform state
        ↓
may contain resulting values
```

A resource can expose sensitive information through state even if the source configuration looks harmless.

Therefore:

```text
State access = privileged access
```

---

# 🧪 44. State Recovery Strategy

If state is damaged:

```text
STOP
 ↓
Do not run random apply
 ↓
Preserve evidence
 ↓
Identify backend/version
 ↓
Find latest valid state version/backup
 ↓
Review differences
 ↓
Restore carefully
 ↓
Run plan
 ↓
Validate
```

Never improvise against production state.

---

# 🩺 45. Troubleshooting Workflow

When Terraform behaves unexpectedly:

```text
1. terraform version
        ↓
2. terraform init
        ↓
3. terraform state list
        ↓
4. terraform state show ADDRESS
        ↓
5. terraform plan
        ↓
6. inspect provider/account/region
        ↓
7. inspect dependencies
        ↓
8. inspect recent state changes
        ↓
9. review backend/locking
        ↓
10. make the smallest safe change
```

---

# ⚠️ 46. Common State Errors

## State Lock Error

Possible causes:

```text
Another Terraform operation is running
Stale lock
Backend issue
Network interruption
```

Do not blindly force-unlock production.

---

# 🔓 47. Force Unlock

Terraform provides:

```bash
terraform force-unlock LOCK_ID
```

This is an advanced recovery operation.

Use only when:

```text
You have confirmed the lock is stale
No valid Terraform operation is running
You understand the backend
```

A wrong force-unlock can allow concurrent operations.

---

# 💥 48. State Corruption

Possible causes:

```text
Interrupted writes
Backend problems
Concurrent operations
Manual state editing
Incorrect state push
Tooling errors
```

Recovery:

```text
Stop
Backup
Inspect
Restore known-good version
Plan
Validate
```

---

# 🧪 49. Hands-On Labs

## Lab 01 — Create Local State

Create a simple S3/VPC resource and inspect:

```text
terraform.tfstate
```

## Lab 02 — List State

```bash
terraform state list
```

## Lab 03 — Show State

```bash
terraform state show RESOURCE
```

## Lab 04 — Show JSON

```bash
terraform show -json
```

## Lab 05 — Pull State

```bash
terraform state pull
```

## Lab 06 — Identify Resource Addresses

Practice:

```text
resource
count
for_each
module
```

## Lab 07 — Create Drift

Change a safe test resource outside Terraform.

## Lab 08 — Detect Drift

Run:

```bash
terraform plan
```

## Lab 09 — Reconcile Drift

Apply the desired configuration.

## Lab 10 — Import Existing Resource

Import a test resource using configuration-driven import.

## Lab 11 — Verify Import

Run:

```bash
terraform plan
```

## Lab 12 — State Move

Move a test resource address.

## Lab 13 — Moved Block

Refactor an address declaratively.

## Lab 14 — State Remove

Remove a disposable test object from state and verify infrastructure remains.

## Lab 15 — Remote Backend

Configure a non-production remote backend.

## Lab 16 — State Versioning

Enable backend/object versioning where supported.

## Lab 17 — Locking

Test concurrent operations safely.

## Lab 18 — Multiple Environments

Create isolated state for:

```text
dev
stage
prod
```

## Lab 19 — State Isolation

Split network and application state.

## Lab 20 — Remote State Consumption

Expose a VPC ID from network state and consume it.

## Lab 21 — State Security

Restrict backend access with IAM.

## Lab 22 — State Encryption

Verify encryption at rest.

## Lab 23 — Audit

Inspect backend access/audit logs.

## Lab 24 — Recovery Drill

Restore a disposable test state from a known-good version.

## Lab 25 — Lock Recovery

Simulate a stale lock in a lab and recover safely.

## Lab 26 — Module Refactor

Move a resource into a module using a moved block.

## Lab 27 — Count → for_each

Migrate addresses without replacing the underlying test resources.

## Lab 28 — State Inspection

Compare:

```text
configuration
state
provider reality
```

## Lab 29 — CI/CD State

Run Terraform from GitHub Actions using remote state.

## Lab 30 — Enterprise Challenge

Build:

```text
Git
 ↓
GitHub Actions
 ↓
Terraform
 ↓
Remote State
 ↓
Locking
 ↓
AWS
```

with:

```text
Separate Dev/Prod state
IAM controls
Encryption
Audit
Recovery
```

---

# 🏆 50. Production State Checklist

Before production:

```text
☑ Remote backend
☑ State locking/concurrency control
☑ Encryption
☑ Access control
☑ Versioning/backups
☑ Audit logging
☑ Environment isolation
☑ No state in Git
☑ CI/CD controls
☑ Recovery procedure
☑ Tested restore
☑ Least privilege
☑ State ownership defined
```

---

# 🚫 51. Never Do These in Production

```text
❌ Commit terraform.tfstate to Git
❌ Make state storage public
❌ Share state casually
❌ Manually edit state JSON without a recovery plan
❌ Force-unlock blindly
❌ Push arbitrary state
❌ Run concurrent applies without coordination
❌ Delete state to "fix" Terraform
❌ Use state rm as a destroy command
❌ Ignore state backup/recovery
```

---

# 🧠 52. State Mental Model

Remember this:

```text
                 DESIRED
              Configuration
                    │
                    ▼
                Terraform
                    │
             ┌──────┴──────┐
             ▼             ▼
          STATE         PROVIDER
             │             │
             └──────┬──────┘
                    ▼
                  PLAN
                    │
                    ▼
                  APPLY
                    │
                    ▼
             REAL INFRASTRUCTURE
```

State is the bridge between:

```text
Terraform configuration
        ↕
Managed infrastructure
```

---

# 🎓 53. Interview Questions

### Beginner

1. What is Terraform state?
2. Why does Terraform need state?
3. What is `terraform.tfstate`?
4. Why should state not be committed to Git?
5. What is a resource address?
6. What is `terraform state list`?
7. What is `terraform state show`?
8. What is drift?
9. What is state locking?
10. What is a remote backend?

### Intermediate

11. State vs configuration?
12. State vs infrastructure?
13. What is `terraform state mv`?
14. What is `terraform state rm`?
15. What is `terraform state pull`?
16. What is `terraform state push`?
17. What is import?
18. How do you detect drift?
19. Why is state sensitive?
20. Why do teams use remote state?

### Advanced

21. How would you secure an S3 Terraform backend?
22. How would you design Dev/Stage/Prod state isolation?
23. How would you recover from state corruption?
24. When is `force-unlock` appropriate?
25. How would you migrate local state to remote state?
26. How would you split a large state file?
27. How would you refactor resources without replacement?
28. How would you use moved blocks?
29. How would you design state for GitHub Actions?
30. Design an enterprise Terraform state architecture.

---

# ⚡ 54. Cheat Sheet

```bash
# List resources
terraform state list

# Show resource state
terraform state show ADDRESS

# Show state/plan
terraform show

# JSON
terraform show -json

# Pull state
terraform state pull

# Move address
terraform state mv SOURCE DESTINATION

# Remove from state
terraform state rm ADDRESS

# Push state - advanced/recovery only
terraform state push FILE

# Force unlock - recovery only
terraform force-unlock LOCK_ID

# Plan
terraform plan

# Apply
terraform apply

# Destroy
terraform destroy
```

Import concept:

```hcl
import {
  to = aws_vpc.main
  id = "vpc-xxxxxxxx"
}
```

Move concept:

```hcl
moved {
  from = aws_instance.old
  to   = module.app.aws_instance.web
}
```

---

# 🧪 55. Golden State Workflow

```text
                Developer
                    │
                    ▼
                 GitHub
                    │
                    ▼
              GitHub Actions
                    │
                    ▼
                Terraform
                    │
                    ▼
             Remote Backend
                    │
              ┌─────┴─────┐
              ▼           ▼
            State       Lock
              │
              ▼
           AWS APIs
              │
              ▼
       Cloud Infrastructure
```

---

# 🗺️ 56. Terraform Roadmap

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
09 State & State Management     🟢 ← YOU ARE HERE
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

# 🧠 NO STATE → NO MEMORY

### VishwaTech Labs

**Terraform • AWS • Azure • GCP • Docker • GitHub Actions • Kubernetes • Ansible • DevSecOps**

### By Vishwanath Gowda H

⭐ Protect the State. Protect the Infrastructure.

</div>
