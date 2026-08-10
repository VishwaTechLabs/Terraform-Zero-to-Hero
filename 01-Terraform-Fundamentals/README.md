<div align="center">

# 🏗️ Terraform Fundamentals — Zero-to-Hero

### ☁️ Infrastructure as Code | VishwaTech Labs

[![Terraform](https://img.shields.io/badge/Terraform-844FBA?logo=terraform&logoColor=white)](https://developer.hashicorp.com/terraform/docs)
[![HCL](https://img.shields.io/badge/HCL-Configuration-blue)](https://developer.hashicorp.com/terraform/language)
[![AWS](https://img.shields.io/badge/AWS-Cloud-FF9900?logo=amazonaws&logoColor=white)](https://aws.amazon.com/)
[![Azure](https://img.shields.io/badge/Azure-Cloud-0078D4?logo=microsoftazure&logoColor=white)](https://azure.microsoft.com/)
[![GCP](https://img.shields.io/badge/GCP-Cloud-4285F4?logo=googlecloud&logoColor=white)](https://cloud.google.com/)
[![Labs](https://img.shields.io/badge/Labs-20+-success)](#-hands-on-labs)

**Learn Terraform from the absolute beginning and build a strong foundation for AWS, Azure, GCP, DevOps, CI/CD, Kubernetes, and Cloud Security.**

[📘 Terraform Documentation](https://developer.hashicorp.com/terraform/docs) •
[📚 Terraform Language](https://developer.hashicorp.com/terraform/language) •
[☁️ Providers](https://registry.terraform.io/)

</div>

---

# 🎯 What You Will Learn

Terraform is an Infrastructure as Code (IaC) tool.

Instead of manually creating:

```text
VPC
Subnet
EC2
S3
IAM
Load Balancer
Database
```

through cloud consoles, Terraform lets you define infrastructure as code:

```text
Terraform Code
      ↓
Terraform
      ↓
Provider
      ↓
Cloud API
      ↓
Infrastructure
```

This module teaches:

- What Terraform is
- Why Infrastructure as Code matters
- Terraform architecture
- Declarative configuration
- HCL
- Providers
- Resources
- Data sources
- Variables
- Outputs
- State
- Desired vs actual state
- Terraform workflow
- `init`
- `fmt`
- `validate`
- `plan`
- `apply`
- `destroy`
- Terraform Registry
- Basic AWS project
- Best practices
- 20+ hands-on labs
- Interview questions

---

# 🧠 1. What Is Terraform?

Terraform is an Infrastructure as Code tool created by HashiCorp.

It allows infrastructure to be defined in configuration files and managed through a repeatable workflow.

Example:

```hcl
resource "aws_s3_bucket" "demo" {
  bucket = "my-terraform-demo-bucket"
}
```

Concept:

```text
HCL
 ↓
Terraform
 ↓
AWS Provider
 ↓
AWS API
 ↓
S3 Bucket
```

---

# 🤔 2. Why Terraform?

Traditional approach:

```text
Login to AWS Console
        ↓
Click VPC
        ↓
Click Subnet
        ↓
Click Route Table
        ↓
Click Security Group
        ↓
Click EC2
        ↓
Repeat manually
```

Terraform:

```text
Write Code
    ↓
terraform plan
    ↓
terraform apply
    ↓
Infrastructure
```

Benefits:

```text
Automation
Repeatability
Version Control
Consistency
Reviewability
Collaboration
Disaster Recovery
Multi-Cloud
```

---

# 🏗️ 3. Infrastructure as Code

IaC means managing infrastructure through machine-readable configuration instead of manually performing every infrastructure operation.

Example:

```text
Traditional
   ↓
Manual Console

IaC
   ↓
Code
   ↓
Version Control
   ↓
Automation
```

IaC can be:

```text
Declarative
Imperative
```

Terraform is primarily **declarative**.

---

# 🧠 4. Declarative vs Imperative

## Imperative

You describe:

```text
Do this
Then this
Then this
Then this
```

Example concept:

```text
Create VPC
Create subnet
Attach gateway
Create route
```

## Declarative

You describe:

```text
I want this final infrastructure.
```

Terraform determines the actions required to reach the desired state.

```text
Desired State
      ↓
Terraform
      ↓
Required Changes
```

---

# ☁️ 5. Terraform Is Not AWS

Important distinction:

```text
Terraform
   ↓
Automation / IaC Tool

AWS
   ↓
Cloud Platform
```

Terraform communicates with AWS through the AWS provider.

Similarly:

```text
Terraform → Azure Provider → Azure
Terraform → Google Provider → GCP
Terraform → Kubernetes Provider → Kubernetes
```

---

# 🔌 6. Terraform Provider

A provider is a plugin that allows Terraform to communicate with an external API.

Examples:

```text
AWS
Azure
Google Cloud
Kubernetes
GitHub
Docker
Cloudflare
```

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

provider "aws" {
  region = "us-east-1"
}
```

Always choose a provider version strategy appropriate for your project.

---

# 📦 7. Resource

A resource represents infrastructure that Terraform manages.

Example:

```hcl
resource "aws_s3_bucket" "demo" {
  bucket = "my-terraform-demo-bucket"
}
```

Structure:

```text
resource
   │
   ├── Type
   │     ↓
   │   aws_s3_bucket
   │
   └── Local Name
         ↓
        demo
```

Terraform resource address:

```text
aws_s3_bucket.demo
```

---

# 🔍 8. Resource Address

If you write:

```hcl
resource "aws_s3_bucket" "demo" {
  bucket = "my-bucket"
}
```

Terraform identifies it as:

```text
aws_s3_bucket.demo
```

This address is used in:

```text
Plan
State
References
Commands
Dependencies
```

---

# 📚 9. Data Source

A data source reads information that already exists.

Concept:

```text
Resource
   ↓
Terraform creates/manages it

Data Source
   ↓
Terraform reads information
```

Example:

```hcl
data "aws_caller_identity" "current" {}
```

Then:

```hcl
output "account_id" {
  value = data.aws_caller_identity.current.account_id
}
```

---

# 📝 10. HCL

Terraform configuration is commonly written in HashiCorp Configuration Language (HCL).

Example:

```hcl
resource "aws_s3_bucket" "demo" {
  bucket = "my-demo-bucket"

  tags = {
    Name        = "My Demo Bucket"
    Environment = "dev"
  }
}
```

HCL is designed to be:

```text
Human-readable
Structured
Declarative
Machine-processable
```

---

# 📁 11. Terraform File Structure

Basic project:

```text
terraform-project/
│
├── provider.tf
├── main.tf
├── variables.tf
├── outputs.tf
├── terraform.tfvars
├── versions.tf
├── .gitignore
└── README.md
```

Terraform loads `.tf` files in the working directory as a configuration.

The filenames are mainly an organizational convention.

---

# 🧩 12. `provider.tf`

Example:

```hcl
provider "aws" {
  region = "us-east-1"
}
```

Purpose:

```text
Configure provider
      ↓
Terraform knows how to communicate with AWS
```

---

# 🏗️ 13. `main.tf`

Usually contains primary resources.

Example:

```hcl
resource "aws_s3_bucket" "demo" {
  bucket = "my-terraform-demo-bucket"
}
```

The name `main.tf` is a convention, not a special Terraform requirement.

---

# 🎛️ 14. `variables.tf`

Variables make configurations reusable.

Example:

```hcl
variable "aws_region" {
  description = "AWS region"
  type        = string
  default     = "us-east-1"
}
```

Use:

```hcl
provider "aws" {
  region = var.aws_region
}
```

---

# 📤 15. `outputs.tf`

Outputs expose useful values.

Example:

```hcl
output "bucket_name" {
  value = aws_s3_bucket.demo.bucket
}
```

After apply:

```text
bucket_name = "my-terraform-demo-bucket"
```

---

# 🧾 16. `terraform.tfvars`

Values can be supplied through a `.tfvars` file.

Example:

```hcl
aws_region = "us-east-1"
```

Then:

```hcl
variable "aws_region" {
  type = string
}
```

Terraform automatically loads:

```text
terraform.tfvars
*.auto.tfvars
```

according to Terraform's variable-loading rules.

Do not commit sensitive values.

---

# 🔐 17. Terraform Authentication

Terraform needs credentials to call cloud APIs.

For AWS, common approaches include:

```text
AWS CLI profile
Environment variables
IAM role
GitHub OIDC
AWS SSO / IAM Identity Center
AssumeRole
```

Avoid:

```hcl
provider "aws" {
  access_key = "..."
  secret_key = "..."
}
```

Hard-coded credentials are insecure.

---

# 🛡️ 18. Recommended AWS Authentication

For local development:

```text
AWS CLI / IAM Identity Center
```

For EC2:

```text
IAM Role
```

For GitHub Actions:

```text
GitHub OIDC
        ↓
AWS IAM Role
```

For cross-account:

```text
AssumeRole
```

---

# 🗂️ 19. Terraform State

Terraform maintains state describing the infrastructure it manages.

Common local state:

```text
terraform.tfstate
```

Concept:

```text
Configuration
     │
     ▼
Terraform
     │
     ├── Desired State
     │
     └── State
            │
            ▼
       Cloud Resources
```

State is critical.

---

# ⚠️ 20. Never Treat `terraform.tfstate` as Disposable

State can contain sensitive information depending on the resources/configuration.

Never casually commit:

```text
terraform.tfstate
terraform.tfstate.*
```

to Git.

Add:

```gitignore
.terraform/
*.tfstate
*.tfstate.*
*.tfvars
*.tfvars.json
```

Only ignore sensitive variable files if your project actually stores secrets there.

---

# ☁️ 21. Remote State

For team environments, state is commonly stored remotely.

AWS example:

```text
S3
  ↓
Terraform State
```

A robust production design may also use state locking/coordination supported by the chosen Terraform backend and current Terraform version.

Benefits:

```text
Centralized state
Team collaboration
Durability
Access control
Backup strategy
```

---

# 🔄 22. Terraform Workflow

The fundamental workflow:

```text
Write
  ↓
terraform fmt
  ↓
terraform init
  ↓
terraform validate
  ↓
terraform plan
  ↓
terraform apply
  ↓
Infrastructure
```

When finished:

```text
terraform destroy
```

---

# 🚀 23. `terraform init`

Initialize a Terraform working directory:

```bash
terraform init
```

It can:

```text
Initialize backend
Download providers
Install modules
Prepare working directory
```

Run it after:

```text
New project
Provider changes
Backend changes
Module changes
```

---

# ✨ 24. `terraform fmt`

Format Terraform code:

```bash
terraform fmt
```

Check without changing:

```bash
terraform fmt -check
```

Use in CI.

---

# ✅ 25. `terraform validate`

Validate configuration syntax and internal consistency:

```bash
terraform validate
```

Typical CI flow:

```text
fmt
 ↓
validate
```

---

# 🔍 26. `terraform plan`

Preview changes:

```bash
terraform plan
```

Concept:

```text
Current State
     +
Configuration
     ↓
Terraform Plan
     ↓
Proposed Changes
```

Example:

```text
+ create
~ update
- destroy
```

---

# 🚀 27. `terraform apply`

Apply the planned infrastructure changes:

```bash
terraform apply
```

Terraform asks for confirmation in normal interactive usage.

For automated environments, use approval controls and carefully designed CI/CD workflows rather than blindly applying every change.

---

# 💥 28. `terraform destroy`

Destroy infrastructure managed by the configuration:

```bash
terraform destroy
```

⚠️ Dangerous in real environments.

Before destroying:

```text
Is this production?
Is data backed up?
Are dependencies understood?
Is the target workspace/account correct?
```

---

# 🧠 29. Terraform Desired State

Suppose your configuration says:

```text
1 VPC
2 Subnets
1 EC2
1 S3
```

But AWS currently has:

```text
1 VPC
1 Subnet
1 EC2
1 S3
```

Terraform detects:

```text
Missing subnet
```

and plans the required change.

Concept:

```text
Configuration
     ↓
Desired State

Cloud
     ↓
Actual State

Terraform
     ↓
Reconciliation
```

---

# 🔄 30. Idempotency

A major IaC concept is repeatability.

If the infrastructure already matches the configuration:

```bash
terraform plan
```

should generally report no changes.

Concept:

```text
Apply
 ↓
Infrastructure

Apply again
 ↓
No unnecessary changes
```

---

# 🔗 31. Resource Dependencies

Terraform can understand dependencies from references.

Example:

```hcl
resource "aws_s3_bucket" "demo" {
  bucket = var.bucket_name
}

output "bucket_arn" {
  value = aws_s3_bucket.demo.arn
}
```

Terraform understands:

```text
S3 Bucket
    ↓
Output
```

---

# 🕸️ 32. Dependency Graph

Terraform builds a dependency graph:

```text
VPC
 │
 ├── Subnet
 │     │
 │     └── EC2
 │
 └── Route Table
```

Then Terraform determines a valid execution order.

Visualize:

```bash
terraform graph
```

---

# 🧩 33. Explicit Dependency

Sometimes you may need:

```hcl
depends_on = [
  aws_s3_bucket.demo
]
```

Use explicit dependencies only when Terraform cannot infer the dependency through configuration references.

Avoid adding `depends_on` everywhere.

---

# 🏷️ 34. Terraform Variables

Variables improve reuse.

Example:

```hcl
variable "environment" {
  type    = string
  default = "dev"
}
```

Use:

```hcl
tags = {
  Environment = var.environment
}
```

Now:

```text
dev
staging
prod
```

can use the same configuration pattern.

---

# 📐 35. Variable Types

Common Terraform types:

```text
string
number
bool
list
set
map
object
tuple
```

Example:

```hcl
variable "instance_count" {
  type    = number
  default = 2
}
```

---

# 🎯 36. Variable Validation

Example:

```hcl
variable "environment" {
  type = string

  validation {
    condition     = contains(["dev", "staging", "prod"], var.environment)
    error_message = "Environment must be dev, staging, or prod."
  }
}
```

This prevents invalid values.

---

# 📤 37. Outputs

Outputs are useful for:

```text
IP address
DNS name
VPC ID
Subnet IDs
Bucket name
Load balancer DNS
```

Example:

```hcl
output "vpc_id" {
  value = aws_vpc.main.id
}
```

---

# 🔢 38. `count`

Create multiple similar resources:

```hcl
resource "aws_s3_bucket" "demo" {
  count = 2

  bucket = "my-demo-${count.index}"
}
```

Access:

```text
aws_s3_bucket.demo[0]
aws_s3_bucket.demo[1]
```

Use `count` when instances are naturally indexed.

---

# 🔁 39. `for_each`

For keyed collections:

```hcl
resource "aws_s3_bucket" "demo" {
  for_each = toset(["logs", "backups"])

  bucket = "my-${each.key}"
}
```

Access:

```text
aws_s3_bucket.demo["logs"]
aws_s3_bucket.demo["backups"]
```

`for_each` is often preferable when resource identity should be tied to meaningful keys.

---

# 🧮 40. Terraform Expressions

Terraform supports expressions such as:

```hcl
"${var.environment}-app"
```

and modern interpolation-free expressions:

```hcl
name = "${var.environment}-app"
```

Common expression tools:

```text
Conditional expressions
Functions
For expressions
Splat expressions
References
Operators
```

---

# 🧰 41. Terraform Functions

Examples:

```hcl
lower(var.environment)
upper(var.environment)
length(var.names)
join("-", var.names)
lookup(var.tags, "Environment", "unknown")
```

Terraform has many built-in functions.

Official reference:

[Terraform Functions](https://developer.hashicorp.com/terraform/language/functions)

---

# 🧱 42. Terraform Modules

A module is a reusable Terraform configuration.

Concept:

```text
Root Module
    │
    ├── VPC Module
    ├── EC2 Module
    └── S3 Module
```

Example:

```hcl
module "vpc" {
  source = "./modules/vpc"
}
```

Modules improve:

```text
Reuse
Consistency
Organization
Enterprise standards
```

---

# 📦 43. Terraform Registry

The Terraform Registry provides providers and modules.

Official:

[Terraform Registry](https://registry.terraform.io/)

Example module concept:

```hcl
module "vpc" {
  source = "terraform-aws-modules/vpc/aws"
}
```

Always review:

```text
Version
Source
Documentation
Inputs
Outputs
Security
Maintenance
```

before using a third-party module.

---

# 🧪 44. First AWS Lab — S3

Project:

```text
terraform-s3-demo/
│
├── provider.tf
├── main.tf
└── outputs.tf
```

Provider:

```hcl
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 6.0"
    }
  }
}

provider "aws" {
  region = "us-east-1"
}
```

Resource:

```hcl
resource "aws_s3_bucket" "demo" {
  bucket = "replace-with-a-unique-bucket-name"
}
```

Output:

```hcl
output "bucket_name" {
  value = aws_s3_bucket.demo.bucket
}
```

Use a globally unique bucket name appropriate to your AWS account.

---

# 🧪 45. Run the First Lab

```bash
terraform init
```

Then:

```bash
terraform fmt
```

Then:

```bash
terraform validate
```

Then:

```bash
terraform plan
```

Finally:

```bash
terraform apply
```

Verify the bucket in AWS.

When the lab is finished:

```bash
terraform destroy
```

---

# 🧪 46. Second Lab — Variables

`variables.tf`:

```hcl
variable "bucket_name" {
  description = "Globally unique S3 bucket name"
  type        = string
}
```

`terraform.tfvars`:

```hcl
bucket_name = "replace-with-your-unique-name"
```

`main.tf`:

```hcl
resource "aws_s3_bucket" "demo" {
  bucket = var.bucket_name
}
```

---

# 🧪 47. Third Lab — Tags

```hcl
resource "aws_s3_bucket" "demo" {
  bucket = var.bucket_name

  tags = {
    Name        = "VishwaTechLabs-Demo"
    Environment = "dev"
    ManagedBy   = "Terraform"
  }
}
```

Enterprise tagging helps with:

```text
Cost allocation
Ownership
Environment identification
Automation
Governance
```

---

# 🧪 48. Fourth Lab — VPC

Basic resource:

```hcl
resource "aws_vpc" "main" {
  cidr_block = "10.0.0.0/16"

  tags = {
    Name      = "vishwatechlabs-vpc"
    ManagedBy = "Terraform"
  }
}
```

Architecture:

```text
AWS
 │
 └── VPC
      │
      └── 10.0.0.0/16
```

This is only the beginning; production VPCs normally include subnets, route tables, gateways, security controls, and other components.

---

# 🏠 49. VPC Analogy

For teaching students:

```text
Office Building
      ↓
VPC
```

```text
Rooms
      ↓
Subnets
```

```text
Main Entrance
      ↓
Internet Gateway
```

```text
Security Guard
      ↓
Security Group
```

```text
Building Security Policy
      ↓
Network ACL
```

This analogy helps beginners understand cloud networking.

---

# 🧪 50. Fifth Lab — VPC + Subnet

Example:

```hcl
resource "aws_vpc" "main" {
  cidr_block = "10.0.0.0/16"
}

resource "aws_subnet" "public" {
  vpc_id     = aws_vpc.main.id
  cidr_block = "10.0.1.0/24"

  tags = {
    Name = "public-subnet"
  }
}
```

Terraform sees:

```text
VPC
 ↓
Subnet
```

because:

```hcl
vpc_id = aws_vpc.main.id
```

creates an implicit dependency.

---

# 🔐 51. Terraform Security Principles

```text
☑ Never hard-code cloud credentials
☑ Protect state
☑ Use remote state carefully
☑ Use least-privilege IAM
☑ Review plans
☑ Protect production
☑ Use version control
☑ Scan Terraform code
☑ Pin provider/module versions appropriately
☑ Avoid secrets in source code
```

---

# 🔍 52. Terraform Security Scanning

Terraform configurations can be scanned with tools such as:

```text
Checkov
tfsec
Trivy
KICS
```

Examples:

```text
IaC Scan
   ↓
Misconfiguration
   ↓
Security Finding
   ↓
Fix
```

Integrate scanning into CI/CD.

---

# 🧪 53. Hands-On Labs

## Lab 01 — Install Terraform

Verify:

```bash
terraform version
```

## Lab 02 — First Configuration

Create:

```text
provider.tf
main.tf
```

## Lab 03 — Terraform Init

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

## Lab 07 — Apply

```bash
terraform apply
```

## Lab 08 — Destroy

```bash
terraform destroy
```

## Lab 09 — S3

Create an S3 bucket.

## Lab 10 — Variables

Parameterize the bucket name.

## Lab 11 — Outputs

Output the bucket ARN.

## Lab 12 — Tags

Add standard tags.

## Lab 13 — VPC

Create a VPC.

## Lab 14 — Subnet

Create a subnet.

## Lab 15 — Dependency

Create resources with implicit dependencies.

## Lab 16 — Data Source

Read AWS account identity.

## Lab 17 — `count`

Create multiple resources.

## Lab 18 — `for_each`

Create keyed resources.

## Lab 19 — Module

Create a local reusable module.

## Lab 20 — Security Scan

Scan Terraform configuration using an IaC security tool.

---

# 🚨 54. Troubleshooting

## Provider Not Found

Run:

```bash
terraform init
```

Check:

```text
required_providers
source
version
```

---

## Authentication Failure

Check:

```bash
aws sts get-caller-identity
```

Then verify:

```text
AWS profile
Environment
IAM permissions
Region
SSO/session
```

---

## Bucket Name Already Exists

S3 bucket names are globally unique.

Use a unique name.

---

## `terraform plan` Wants Unexpected Changes

Check:

```text
State
Configuration
Variables
Provider version
Drift
Manual cloud changes
```

---

## State Lock / Backend Problem

Check:

```text
Backend configuration
Permissions
Concurrent Terraform operations
Locking mechanism
```

Do not delete or manually modify state to "fix" a problem without understanding the impact.

---

# 🏢 55. Enterprise Terraform Workflow

```text
Developer
    ↓
Git Branch
    ↓
Pull Request
    ↓
Terraform fmt
    ↓
Terraform validate
    ↓
Security Scan
    ↓
Terraform Plan
    ↓
Code Review
    ↓
Approval
    ↓
Terraform Apply
    ↓
Cloud Infrastructure
```

Production should use controlled approvals and protected credentials.

---

# 🌍 56. Multi-Cloud Vision

Terraform can manage:

```text
AWS
 │
 ├── VPC
 ├── EC2
 └── S3

Azure
 │
 ├── VNet
 ├── VM
 └── Storage

GCP
 │
 ├── VPC
 ├── Compute
 └── Cloud Storage
```

Concept:

```text
Terraform
   │
   ├── AWS Provider
   ├── Azure Provider
   └── Google Provider
```

Terraform does not make all cloud services identical; each provider exposes cloud-specific resources and APIs.

---

# 🎓 57. Interview Questions

## Beginner

1. What is Terraform?
2. What is Infrastructure as Code?
3. Terraform vs CloudFormation?
4. Declarative vs imperative?
5. What is HCL?
6. What is a provider?
7. What is a resource?
8. What is a data source?
9. What is a variable?
10. What is an output?

## Intermediate

11. What is Terraform state?
12. Why is state important?
13. Why should state not be committed to Git?
14. What is `terraform init`?
15. What is `terraform plan`?
16. What is `terraform apply`?
17. What is `terraform destroy`?
18. What is `depends_on`?
19. `count` vs `for_each`?
20. What is a module?

## Advanced

21. How would you design remote state?
22. How would you secure Terraform state?
23. How would you implement Terraform in GitHub Actions?
24. How would you authenticate Terraform to AWS without static keys?
25. How would you handle multi-account AWS?
26. How would you handle multi-cloud?
27. How would you detect Terraform drift?
28. How would you implement policy/security scanning?
29. How would you protect production `apply`?
30. Design an enterprise Terraform CI/CD architecture.

---

# ⚡ 58. Terraform Cheat Sheet

```bash
# Version
terraform version

# Initialize
terraform init

# Format
terraform fmt

# Check formatting
terraform fmt -check

# Validate
terraform validate

# Plan
terraform plan

# Apply
terraform apply

# Destroy
terraform destroy

# Show state
terraform show

# List resources
terraform state list

# Show resource
terraform state show RESOURCE

# Graph
terraform graph
```

---

# 🏆 59. Fundamentals Mastery Checklist

```text
☑ Terraform
☑ IaC
☑ Declarative model
☑ HCL
☑ Providers
☑ Resources
☑ Data sources
☑ Variables
☑ Outputs
☑ State
☑ Desired state
☑ Actual state
☑ Dependency graph
☑ Modules
☑ Registry
☑ terraform init
☑ terraform fmt
☑ terraform validate
☑ terraform plan
☑ terraform apply
☑ terraform destroy
☑ AWS authentication
☑ S3
☑ VPC
☑ Subnets
☑ Tags
☑ Security scanning
```

---

# 🗺️ 60. Terraform Zero-to-Hero Roadmap

```text
01 Terraform Fundamentals       ← 🟢 YOU ARE HERE
        ↓
02 Terraform Installation
        ↓
03 Terraform CLI & Workflow
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
11 Modules
        ↓
12 AWS VPC Project
        ↓
13 AWS EC2 Project
        ↓
14 AWS S3 Project
        ↓
15 IAM & Security
        ↓
16 Terraform Cloud / HCP Terraform
        ↓
17 Terraform + GitHub Actions
        ↓
18 Terraform Security / Policy
        ↓
19 Multi-Cloud Terraform
        ↓
20 Enterprise Capstone
```

---

<div align="center">

# 🏗️ CODE YOUR INFRASTRUCTURE. AUTOMATE YOUR CLOUD.

### VishwaTech Labs

**Terraform • AWS • Azure • GCP • Docker • GitHub Actions • Kubernetes • Ansible • DevSecOps**

### By Vishwanath Gowda H

⭐ Plan → Review → Apply → Automate

</div>
