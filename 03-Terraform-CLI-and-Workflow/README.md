<div align="center">

# 🚀 Terraform CLI & Workflow — Complete Masterclass

### 🏗️ Init → Format → Validate → Plan → Apply → State → Destroy | VishwaTech Labs

[![Terraform](https://img.shields.io/badge/Terraform-844FBA?logo=terraform&logoColor=white)](https://developer.hashicorp.com/terraform/docs)
[![CLI](https://img.shields.io/badge/CLI-Commands-blue)](#-terraform-cli-cheat-sheet)
[![IaC](https://img.shields.io/badge/IaC-Infrastructure%20as%20Code-success)](#-terraform-workflow)
[![AWS](https://img.shields.io/badge/AWS-Cloud-FF9900?logo=amazonaws&logoColor=white)](https://aws.amazon.com/)
[![Labs](https://img.shields.io/badge/Labs-30+-purple)](#-hands-on-labs)

**Master the Terraform command line and understand exactly what happens from configuration to infrastructure.**

[📘 Terraform CLI](https://developer.hashicorp.com/terraform/cli) •
[📚 Terraform Commands](https://developer.hashicorp.com/terraform/cli/commands) •
[🏗️ Terraform Language](https://developer.hashicorp.com/terraform/language)

</div>

---

# 🎯 What You Will Learn

This module focuses on the **Terraform CLI and real execution workflow**.

You will master:

```text
terraform init
terraform fmt
terraform validate
terraform plan
terraform apply
terraform show
terraform output
terraform state
terraform destroy
terraform graph
terraform console
terraform providers
terraform version
terraform workspace
terraform import
terraform force-unlock concepts
```

And understand:

```text
Configuration
      ↓
Initialization
      ↓
Validation
      ↓
Planning
      ↓
Approval
      ↓
Apply
      ↓
State
      ↓
Drift Detection
      ↓
Update / Destroy
```

---

# 🧠 1. Terraform CLI Mental Model

Think of Terraform as an engine that compares:

```text
                    Terraform
                       │
        ┌──────────────┼──────────────┐
        ▼              ▼              ▼
 Configuration       State        Provider APIs
        │              │              │
        └──────────────┼──────────────┘
                       ▼
                  Plan / Apply
                       │
                       ▼
                 Cloud Resources
```

Terraform is not simply a command that "creates infrastructure."

It continuously uses configuration, state, and provider information to determine the actions required to reach the desired configuration.

---

# 🔄 2. Complete Terraform Workflow

The standard workflow:

```text
        Write Terraform Code
                │
                ▼
         terraform fmt
                │
                ▼
       terraform init
                │
                ▼
      terraform validate
                │
                ▼
         terraform plan
                │
                ▼
            Review
                │
                ▼
        terraform apply
                │
                ▼
          Infrastructure
                │
                ▼
             State
```

For cleanup:

```text
terraform destroy
```

---

# 📁 3. Example Project

```text
terraform-cli-demo/
│
├── provider.tf
├── main.tf
├── variables.tf
├── terraform.tfvars
├── outputs.tf
├── .gitignore
└── README.md
```

Example:

```hcl
resource "aws_s3_bucket" "demo" {
  bucket = var.bucket_name
}
```

---

# ⚙️ 4. `terraform version`

Check Terraform:

```bash
terraform version
```

Useful for:

```text
Troubleshooting
CI/CD
Provider compatibility
Team standardization
```

Also:

```bash
terraform -help
```

---

# 🧭 5. Terraform Help

General:

```bash
terraform -help
```

Command-specific:

```bash
terraform plan -help
```

Provider-related:

```bash
terraform providers
```

This is an excellent habit for students:

> When you don't know a command, ask Terraform itself.

---

# 📦 6. `terraform init`

Initialize the working directory:

```bash
terraform init
```

It can:

```text
Initialize backend
Download providers
Install modules
Create/update dependency metadata
Prepare the working directory
```

Typical output includes:

```text
Initializing the backend...
Initializing provider plugins...
Terraform has been successfully initialized!
```

---

# 🧩 7. What Happens During `init`?

Conceptually:

```text
Terraform Configuration
        │
        ▼
required_providers
        │
        ▼
Provider Registry
        │
        ▼
Provider Plugin
        │
        ▼
.terraform/
```

If a module is used:

```text
Module Source
      ↓
terraform init
      ↓
Module Download
```

---

# 🔁 8. When Should You Run `terraform init`?

Run it:

```text
☑ New Terraform project
☑ Provider configuration changes
☑ Backend changes
☑ Module changes
☑ Provider version changes
☑ After cloning a repository
```

Common scenario:

```bash
git clone REPOSITORY
cd REPOSITORY
terraform init
```

---

# ✨ 9. `terraform fmt`

Format Terraform files:

```bash
terraform fmt
```

Check only:

```bash
terraform fmt -check
```

Recursive:

```bash
terraform fmt -recursive
```

This is useful for repositories containing nested modules.

---

# 🧹 10. Why Formatting Matters

Without formatting:

```hcl
resource "aws_s3_bucket" "demo" {
bucket="mybucket"
tags={
Name="demo"
}
}
```

After:

```bash
terraform fmt
```

it becomes consistently formatted.

Benefits:

```text
Readable code
Consistent code
Cleaner pull requests
Easy reviews
CI enforcement
```

---

# ✅ 11. `terraform validate`

Run:

```bash
terraform validate
```

It checks the configuration for syntax and internal consistency.

Important:

```text
validate
   ≠
plan
```

`validate` does not prove that:

```text
AWS permissions are correct
Resource quotas are available
Requested resource is available
```

Those issues may appear later during planning or apply.

---

# 🔍 12. `terraform plan`

Run:

```bash
terraform plan
```

Concept:

```text
Configuration
     +
State
     +
Provider Information
     ↓
Terraform Plan
     ↓
Proposed Changes
```

Typical symbols:

```text
+ create
~ update
- destroy
-/+ replace
<= read
```

---

# 🟢 13. Create

Example:

```text
+ aws_s3_bucket.demo
```

Meaning:

```text
Resource does not exist in the desired managed state
        ↓
Terraform plans to create it
```

---

# 🟡 14. Update

Example:

```text
~ aws_s3_bucket.demo
```

Meaning:

```text
Resource exists
     ↓
Configuration changed
     ↓
Terraform plans an update
```

---

# 🔴 15. Destroy

Example:

```text
- aws_s3_bucket.demo
```

Meaning:

```text
Terraform plans to remove the resource
```

Always investigate unexpected destroys.

---

# 🔄 16. Replacement

Some changes cannot be performed in-place.

Terraform may show:

```text
-/+ resource
```

Concept:

```text
Old Resource
     ↓
Destroy
     ↓
New Resource
```

This can cause downtime or data loss depending on the resource.

---

# 🚨 17. Unexpected Destroy

If you see:

```text
-/+ 
```

or:

```text
- resource
```

stop and investigate.

Check:

```text
Configuration
Variables
State
Provider
Resource arguments
Lifecycle behavior
```

Never blindly approve a production plan containing unexpected destruction.

---

# 🚀 18. `terraform apply`

Run:

```bash
terraform apply
```

Terraform calculates a plan and, in normal interactive use, asks for confirmation.

Typical:

```text
Plan: 1 to add, 0 to change, 0 to destroy.

Do you want to perform these actions?
Only 'yes' will be accepted.
```

---

# 📄 19. Saved Plan

For controlled workflows:

```bash
terraform plan -out=tfplan
```

Then:

```bash
terraform apply tfplan
```

Concept:

```text
Plan
 ↓
Review
 ↓
Approved Plan File
 ↓
Apply
```

This is useful for CI/CD because the reviewed plan can be the artifact that is subsequently applied.

---

# ⚠️ 20. Plan Is Not a Permanent Contract

A saved plan should be treated as a plan artifact tied to the configuration/state/backend context in which it was generated.

Do not assume a plan created days ago is automatically appropriate after significant infrastructure or state changes.

---

# 📤 21. `terraform output`

List outputs:

```bash
terraform output
```

Specific:

```bash
terraform output vpc_id
```

Machine-readable JSON:

```bash
terraform output -json
```

Useful for:

```text
Scripts
CI/CD
Automation
Integration
```

---

# 🔎 22. `terraform show`

Show current state:

```bash
terraform show
```

Show a saved plan:

```bash
terraform show tfplan
```

JSON:

```bash
terraform show -json
```

Useful for automation and analysis.

---

# 🗂️ 23. `terraform state list`

List resources tracked in state:

```bash
terraform state list
```

Example:

```text
aws_vpc.main
aws_subnet.public
aws_s3_bucket.demo
```

---

# 🔍 24. `terraform state show`

Inspect one resource:

```bash
terraform state show aws_s3_bucket.demo
```

This is useful for understanding what Terraform currently has recorded for that resource.

---

# ⚠️ 25. Terraform State Commands

State manipulation is powerful.

Commands include:

```bash
terraform state list
terraform state show
terraform state mv
terraform state rm
terraform state pull
terraform state push
```

Use destructive or modifying state commands only when you understand the consequences.

Never casually edit the state file manually.

---

# 🧠 26. `terraform state rm`

Example:

```bash
terraform state rm aws_s3_bucket.demo
```

This removes the resource from Terraform state.

Important:

```text
It does NOT normally destroy the real cloud resource.
```

Concept:

```text
Terraform State
     ↓
Resource removed from tracking

AWS Resource
     ↓
May still exist
```

Use only for deliberate state-management operations.

---

# 🔄 27. `terraform state mv`

Useful when changing resource addresses while preserving management.

Example concept:

```text
aws_instance.web
       ↓
module.web.aws_instance.web
```

Instead of allowing Terraform to think:

```text
destroy old
create new
```

you can move the state address appropriately.

Modern Terraform refactoring can also use `moved` blocks, which are often preferable for declarative configuration changes.

---

# 🧩 28. `moved` Block

Example:

```hcl
moved {
  from = aws_instance.web
  to   = module.web.aws_instance.web
}
```

Concept:

```text
Old Address
     ↓
Moved
     ↓
New Address
```

This helps Terraform understand that a resource was renamed or relocated in configuration.

---

# 📥 29. `terraform import`

Import existing infrastructure into Terraform management.

Modern Terraform supports import blocks as well as the CLI import workflow.

CLI example:

```bash
terraform import aws_s3_bucket.demo existing-bucket-name
```

Concept:

```text
Existing AWS Resource
        ↓
terraform import
        ↓
Terraform State
```

Important:

> Importing state does not automatically create a complete Terraform configuration for the resource.

You must create appropriate configuration.

---

# 🧱 30. Import Workflow

```text
Existing Cloud Resource
          ↓
Write Resource Configuration
          ↓
terraform import
          ↓
State
          ↓
terraform plan
          ↓
Adjust Configuration
          ↓
Plan = expected result
```

---

# 🧠 31. `terraform providers`

Show providers required by the configuration:

```bash
terraform providers
```

Useful when troubleshooting:

```text
Provider version
Provider source
Module dependencies
```

---

# 🕸️ 32. `terraform graph`

Generate a dependency graph:

```bash
terraform graph
```

Concept:

```text
VPC
 │
 ├── Subnet
 │     │
 │     └── EC2
 │
 └── Route Table
```

You can pipe graph output into Graphviz tooling for visualization where installed.

---

# 🧮 33. `terraform console`

Start the Terraform expression console:

```bash
terraform console
```

Example:

```text
> 1 + 2
3
```

With variables/configuration:

```text
> var.environment
```

Useful for testing:

```text
Expressions
Functions
Variables
References
```

Exit:

```text
Ctrl+D
```

---

# 🗃️ 34. `terraform workspace`

Terraform workspaces provide separate state instances for the same configuration within a backend.

List:

```bash
terraform workspace list
```

Create:

```bash
terraform workspace new dev
```

Select:

```bash
terraform workspace select dev
```

Show:

```bash
terraform workspace show
```

---

# ⚠️ 35. Workspace Reality Check

Workspaces are useful, but they are not automatically the best environment strategy.

For serious enterprise separation, consider:

```text
Separate AWS accounts
Separate state
Separate CI/CD environments
Separate credentials
Separate backend keys
```

especially for:

```text
Production
Security boundaries
Compliance boundaries
```

---

# 🔐 36. `terraform force-unlock`

If a backend supports locking and a stale lock remains, Terraform may report a state lock problem.

The command:

```bash
terraform force-unlock LOCK_ID
```

can remove a lock.

⚠️ **Never use it blindly.**

First verify:

```text
Is another Terraform process running?
Is a CI/CD job active?
Is the lock genuinely stale?
```

Force-unlocking while another operation is active can cause state corruption or conflicting changes.

---

# 🧹 37. `terraform refresh` — Important Concept

Modern Terraform workflows should generally use:

```bash
terraform plan
```

or:

```bash
terraform apply
```

to reconcile configuration and state rather than treating the old standalone refresh workflow as a normal daily command.

Concept:

```text
Cloud
 ↓
Refresh/Read
 ↓
State
 ↓
Plan
```

Always use the current Terraform documentation for version-specific behavior.

---

# 🔄 38. Drift

Drift happens when infrastructure changes outside Terraform.

Example:

```text
Terraform
   ↓
Creates EC2
   ↓
Engineer manually changes EC2
   ↓
AWS actual state ≠ Terraform configuration
```

Then:

```bash
terraform plan
```

may detect differences.

---

# 🔍 39. Drift Detection Workflow

```text
Terraform Configuration
        │
        ▼
terraform plan
        │
        ▼
Compare
 ┌──────┴──────┐
 ▼             ▼
Expected     Actual
 │             │
 └──────┬──────┘
        ▼
      Drift
```

---

# 🧪 40. Example Drift Lab

1. Create an AWS resource with Terraform.
2. Change a supported attribute manually in AWS.
3. Run:

```bash
terraform plan
```

4. Observe the difference.
5. Decide whether:
   - Terraform should restore the desired configuration, or
   - The Terraform configuration should be intentionally updated.

---

# 🏷️ 41. Lifecycle Basics

Terraform lifecycle meta-arguments can influence resource behavior.

Example:

```hcl
resource "aws_instance" "demo" {

  lifecycle {
    create_before_destroy = true
  }
}
```

Other lifecycle controls include:

```text
prevent_destroy
ignore_changes
replace_triggered_by
```

Use them deliberately.

---

# 🛡️ 42. `prevent_destroy`

Example:

```hcl
lifecycle {
  prevent_destroy = true
}
```

This can protect important resources from accidental Terraform destruction.

It is not a substitute for:

```text
Backups
IAM controls
Change management
Recovery plans
```

---

# 🔄 43. `create_before_destroy`

Concept:

```text
Old Resource
     │
     │
New Resource
     ↓
Create First
     ↓
Switch / Replace
     ↓
Destroy Old
```

Useful when resource constraints and provider behavior allow it.

---

# 👀 44. `ignore_changes`

Example:

```hcl
lifecycle {
  ignore_changes = [
    tags["ManagedExternally"]
  ]
}
```

This can be useful when another approved system manages a particular attribute.

Avoid using `ignore_changes` just to hide unwanted drift.

---

# 🔥 45. Terraform Command Decision Tree

```text
Need to start a project?
        ↓
terraform init

Need formatting?
        ↓
terraform fmt

Need syntax/config validation?
        ↓
terraform validate

Want to see changes?
        ↓
terraform plan

Ready to execute?
        ↓
terraform apply

Need outputs?
        ↓
terraform output

Need state resources?
        ↓
terraform state list

Need resource details?
        ↓
terraform state show

Need dependency graph?
        ↓
terraform graph

Need expression testing?
        ↓
terraform console

Need to remove infrastructure?
        ↓
terraform destroy
```

---

# 🧪 46. Hands-On Labs

## Lab 01 — Version

```bash
terraform version
```

## Lab 02 — Help

```bash
terraform -help
terraform plan -help
```

## Lab 03 — Init

```bash
terraform init
```

## Lab 04 — Format

```bash
terraform fmt
```

## Lab 05 — Validate

```bash
terraform validate
```

## Lab 06 — Plan

```bash
terraform plan
```

## Lab 07 — Save Plan

```bash
terraform plan -out=tfplan
```

## Lab 08 — Apply Saved Plan

```bash
terraform apply tfplan
```

## Lab 09 — Outputs

```bash
terraform output
terraform output -json
```

## Lab 10 — Show

```bash
terraform show
```

## Lab 11 — State List

```bash
terraform state list
```

## Lab 12 — State Show

```bash
terraform state show RESOURCE
```

## Lab 13 — Providers

```bash
terraform providers
```

## Lab 14 — Graph

```bash
terraform graph
```

## Lab 15 — Console

```bash
terraform console
```

## Lab 16 — Workspace

```bash
terraform workspace list
terraform workspace new dev
terraform workspace show
```

## Lab 17 — Drift

Modify a test resource manually and run:

```bash
terraform plan
```

## Lab 18 — Import

Import an existing test resource and reconcile configuration.

## Lab 19 — State Move

Practice a controlled resource-address refactor using `moved` blocks and compare with `terraform state mv`.

## Lab 20 — Lifecycle

Test:

```text
create_before_destroy
prevent_destroy
ignore_changes
```

## Lab 21 — Destroy

```bash
terraform destroy
```

## Lab 22 — CI Workflow

Create:

```text
fmt
validate
plan
```

in GitHub Actions.

## Lab 23 — Plan Artifact

Generate a saved plan in CI and understand its security/handling requirements.

## Lab 24 — Troubleshoot Lock

Study state locking using a safe test backend/environment.

## Lab 25 — Dependency Graph

Build:

```text
VPC
 ↓
Subnet
 ↓
EC2
```

and inspect the graph.

## Lab 26 — Multi-Resource Plan

Create:

```text
VPC
Subnet
S3
Security Group
```

and analyze the complete plan.

## Lab 27 — Unexpected Destroy

Deliberately introduce a controlled change that causes replacement and understand why.

## Lab 28 — Output Automation

Use:

```bash
terraform output -json
```

from a script.

## Lab 29 — State Investigation

Practice:

```bash
terraform state list
terraform state show
terraform show
```

## Lab 30 — Full Workflow Challenge

Perform:

```text
fmt
 ↓
init
 ↓
validate
 ↓
plan
 ↓
review
 ↓
apply
 ↓
output
 ↓
show
 ↓
destroy
```

---

# 🏢 47. Enterprise CLI Workflow

```text
Developer
   │
   ▼
Git Branch
   │
   ▼
terraform fmt
   │
   ▼
terraform validate
   │
   ▼
Security Scan
   │
   ▼
terraform plan
   │
   ▼
Pull Request
   │
   ▼
Code Review
   │
   ▼
Approved
   │
   ▼
CI/CD Apply
   │
   ▼
Cloud
```

Production should use:

```text
Protected branches
Protected environments
Remote state
State locking/coordination
Short-lived credentials
Least privilege
Plan review
Audit logs
```

---

# 🔐 48. CLI Security

Never expose:

```text
AWS secrets
Terraform state
Provider credentials
Tokens
Private keys
```

through:

```text
Shell history
CI logs
Git
Public artifacts
Debug output
```

Be especially careful with commands that print:

```text
terraform show
terraform output
terraform state pull
```

because state can contain sensitive information.

---

# 🚨 49. Common Errors

## `Error acquiring the state lock`

Check:

```text
Another Terraform process
CI/CD job
Stale lock
Backend configuration
```

Do not immediately force-unlock.

---

## `No configuration files`

Check:

```bash
ls
```

or Windows:

```powershell
dir
```

Make sure you are inside the Terraform project directory.

---

## `Provider configuration not present`

Run:

```bash
terraform init
```

and inspect:

```bash
terraform providers
```

---

## `AccessDenied`

Check:

```bash
aws sts get-caller-identity
```

Then inspect:

```text
IAM
Profile
Role
Region
Permissions
```

---

## Unexpected Resource Replacement

Inspect:

```bash
terraform plan
```

Look for:

```text
-/+
```

Then identify which argument requires replacement.

---

# 🧠 50. Best Practices

```text
☑ Run fmt
☑ Run validate
☑ Review plan
☑ Use saved plans in controlled pipelines
☑ Protect state
☑ Use remote state for teams
☑ Use least-privilege credentials
☑ Never manually edit state
☑ Avoid blind force-unlock
☑ Detect drift
☑ Use modules appropriately
☑ Pin versions deliberately
☑ Use CI/CD
☑ Protect production
```

---

# 🎓 51. Interview Questions

## Beginner

1. What does `terraform init` do?
2. What is `terraform fmt`?
3. What does `terraform validate` check?
4. What is `terraform plan`?
5. What is `terraform apply`?
6. What is `terraform destroy`?
7. What is `terraform output`?
8. What is `terraform show`?
9. What is `terraform state list`?
10. What is `terraform providers`?

## Intermediate

11. What do `+`, `~`, `-`, and `-/+` mean in a plan?
12. What is a saved plan?
13. Why use `terraform plan -out`?
14. What is Terraform drift?
15. How do you inspect a resource in state?
16. What does `terraform state rm` do?
17. What does `terraform state mv` do?
18. What is a `moved` block?
19. What is `terraform import`?
20. What are Terraform workspaces?

## Advanced

21. How would you safely handle a state lock?
22. Why is `force-unlock` dangerous?
23. How would you design a Terraform CI/CD workflow?
24. How would you prevent accidental production destruction?
25. How would you detect and remediate drift?
26. How would you manage resource refactoring without recreation?
27. How would you secure plan artifacts?
28. Why should Terraform state be treated as sensitive?
29. How would you troubleshoot an unexpected replacement?
30. Design an enterprise Terraform execution workflow.

---

# ⚡ 52. Terraform CLI Cheat Sheet

```bash
# Version
terraform version

# Help
terraform -help

# Initialize
terraform init

# Reconfigure backend
terraform init -reconfigure

# Upgrade dependencies/providers
terraform init -upgrade

# Format
terraform fmt
terraform fmt -check
terraform fmt -recursive

# Validate
terraform validate

# Plan
terraform plan

# Save plan
terraform plan -out=tfplan

# Apply
terraform apply
terraform apply tfplan

# Destroy
terraform destroy

# Output
terraform output
terraform output -json

# State
terraform state list
terraform state show RESOURCE
terraform state mv OLD NEW
terraform state rm RESOURCE
terraform state pull

# Inspect
terraform show
terraform show -json

# Providers
terraform providers

# Graph
terraform graph

# Console
terraform console

# Workspace
terraform workspace list
terraform workspace new dev
terraform workspace select dev
terraform workspace show

# Import
terraform import ADDRESS ID
```

---

# 🏆 53. Mastery Checklist

```text
CLI
☑ terraform version
☑ terraform help
☑ terraform init
☑ terraform fmt
☑ terraform validate
☑ terraform plan
☑ terraform apply
☑ terraform destroy

INSPECTION
☑ terraform output
☑ terraform show
☑ terraform providers
☑ terraform graph
☑ terraform console

STATE
☑ terraform state list
☑ terraform state show
☑ terraform state mv
☑ terraform state rm
☑ terraform state pull
☑ moved blocks

ADVANCED
☑ Import
☑ Drift
☑ Lifecycle
☑ Workspaces
☑ Saved plans
☑ State locking concepts

CI/CD
☑ fmt
☑ validate
☑ plan
☑ review
☑ apply
☑ secure credentials
☑ protected production
```

---

# 🗺️ 54. Terraform Roadmap

```text
01 Terraform Fundamentals       🟢
        ↓
02 Terraform Installation       🟢
        ↓
03 Terraform CLI & Workflow     🟢 ← YOU ARE HERE
        ↓
04 Providers & Authentication
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

# 🚀 PLAN • REVIEW • APPLY • AUTOMATE

### VishwaTech Labs

**Terraform • AWS • Azure • GCP • Docker • GitHub Actions • Kubernetes • Ansible • DevSecOps**

### By Vishwanath Gowda H

⭐ Master the CLI. Master Infrastructure as Code.

</div>
