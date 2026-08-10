<div align="center">

# 🎛️ Terraform Variables & Outputs — Complete Masterclass

### 🧩 Make Terraform Reusable • Dynamic • Secure • Production-Ready | VishwaTech Labs

[![Terraform](https://img.shields.io/badge/Terraform-844FBA?logo=terraform&logoColor=white)](https://developer.hashicorp.com/terraform/docs)
[![HCL](https://img.shields.io/badge/HCL-Configuration-blue)](https://developer.hashicorp.com/terraform/language)
[![AWS](https://img.shields.io/badge/AWS-Cloud-FF9900?logo=amazonaws&logoColor=white)](https://aws.amazon.com/)
[![Labs](https://img.shields.io/badge/Labs-30+-success)](#-hands-on-labs)
[![Security](https://img.shields.io/badge/Sensitive%20Values-Secure-red)](#-sensitive-variables)

**Learn how to turn hard-coded Terraform configurations into reusable infrastructure using variables, types, validation, `.tfvars`, environment variables, locals, and outputs.**

[📘 Input Variables](https://developer.hashicorp.com/terraform/language/values/variables) •
[📤 Outputs](https://developer.hashicorp.com/terraform/language/values/outputs) •
[🧠 Locals](https://developer.hashicorp.com/terraform/language/values/locals)

</div>

---

# 🎯 What You Will Learn

This module teaches:

```text
Variables
   ↓
Types
   ↓
Defaults
   ↓
Descriptions
   ↓
Validation
   ↓
Variable Files
   ↓
Environment Variables
   ↓
Sensitive Values
   ↓
Locals
   ↓
Expressions
   ↓
Outputs
   ↓
Complex Values
   ↓
Reusable Terraform
```

You will work with:

```text
string
number
bool
list
set
map
object
tuple
optional attributes
null
```

and:

```text
var.*
local.*
output.*
```

---

# 🧠 1. Why Variables?

Without variables:

```hcl
resource "aws_s3_bucket" "demo" {
  bucket = "vishwatech-dev-bucket"
}
```

Now the same configuration is tied to one value.

With a variable:

```hcl
resource "aws_s3_bucket" "demo" {
  bucket = var.bucket_name
}
```

Now:

```text
dev
staging
prod
```

can use the same Terraform configuration.

---

# 🏗️ 2. Hard-Coded vs Reusable

### ❌ Hard-coded

```hcl
provider "aws" {
  region = "us-east-1"
}
```

### ✅ Variable-driven

```hcl
provider "aws" {
  region = var.aws_region
}
```

Variable:

```hcl
variable "aws_region" {
  type    = string
  default = "us-east-1"
}
```

---

# 📦 3. Variable Anatomy

Basic:

```hcl
variable "environment" {
  type        = string
  description = "Deployment environment"
  default     = "dev"
}
```

Parts:

```text
variable
   ↓
Name
   ↓
Type
   ↓
Description
   ↓
Default
   ↓
Validation / Sensitive / Other Settings
```

---

# 🔤 4. String Variables

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

Override:

```bash
terraform plan -var="environment=staging"
```

---

# 🔢 5. Number Variables

```hcl
variable "instance_count" {
  type    = number
  default = 2
}
```

Use:

```hcl
count = var.instance_count
```

---

# ✅ 6. Boolean Variables

```hcl
variable "enable_monitoring" {
  type    = bool
  default = true
}
```

Use:

```hcl
count = var.enable_monitoring ? 1 : 0
```

Concept:

```text
true
 ↓
Create

false
 ↓
Don't create
```

---

# 📋 7. List Variables

```hcl
variable "availability_zones" {
  type = list(string)

  default = [
    "us-east-1a",
    "us-east-1b"
  ]
}
```

Access:

```hcl
var.availability_zones[0]
```

---

# 🧮 8. Set Variables

```hcl
variable "environments" {
  type = set(string)

  default = [
    "dev",
    "staging",
    "prod"
  ]
}
```

A set is useful when:

```text
Uniqueness matters
Ordering does not
```

---

# 🗺️ 9. Map Variables

```hcl
variable "instance_types" {
  type = map(string)

  default = {
    dev  = "t3.micro"
    prod = "t3.medium"
  }
}
```

Access:

```hcl
var.instance_types[var.environment]
```

---

# 🧱 10. Object Variables

Objects define structured data.

```hcl
variable "server" {
  type = object({
    name          = string
    instance_type = string
    environment   = string
  })
}
```

Example value:

```hcl
server = {
  name          = "web01"
  instance_type = "t3.micro"
  environment   = "dev"
}
```

---

# 🧩 11. Tuple Variables

Tuple elements can have different types.

```hcl
variable "server_definition" {
  type = tuple([
    string,
    number,
    bool
  ])
}
```

Example:

```hcl
server_definition = [
  "web01",
  2,
  true
]
```

---

# 🧠 12. Choosing a Type

Use:

```text
string
```

for:

```text
Names
Regions
Environment
IDs
```

Use:

```text
number
```

for:

```text
Counts
Ports
Sizes
```

Use:

```text
bool
```

for:

```text
Enable / Disable
```

Use:

```text
list / set
```

for:

```text
Collections
```

Use:

```text
map
```

for:

```text
Key → Value
```

Use:

```text
object
```

for:

```text
Structured configuration
```

---

# 🎯 13. Required Variables

If no default exists:

```hcl
variable "environment" {
  type = string
}
```

Terraform requires a value.

If you run:

```bash
terraform plan
```

Terraform may prompt for it in interactive use.

For CI/CD, supply variables explicitly through approved mechanisms.

---

# 🏷️ 14. Default Values

```hcl
variable "environment" {
  type    = string
  default = "dev"
}
```

Now:

```bash
terraform plan
```

can use:

```text
dev
```

unless overridden.

---

# 📖 15. Variable Descriptions

Good:

```hcl
variable "aws_region" {
  description = "AWS region where infrastructure will be deployed"
  type        = string
  default     = "us-east-1"
}
```

Descriptions make modules easier to understand.

---

# 🔐 16. Sensitive Variables

Example:

```hcl
variable "database_password" {
  type      = string
  sensitive = true
}
```

Terraform will redact the value in many CLI displays.

Important:

> `sensitive = true` does not magically encrypt the value or remove it from all storage.

Sensitive values can still exist in state.

Therefore:

```text
Protect state
Use secure backends
Restrict access
Avoid secrets in Git
```

---

# 🧪 17. Variable Validation

Example:

```hcl
variable "environment" {
  type = string

  validation {
    condition = contains(
      ["dev", "staging", "prod"],
      var.environment
    )

    error_message = "Environment must be dev, staging, or prod."
  }
}
```

Now:

```text
dev       ✅
staging   ✅
prod      ✅
test      ❌
```

---

# 🔢 18. Numeric Validation

```hcl
variable "instance_count" {
  type = number

  validation {
    condition     = var.instance_count >= 1 && var.instance_count <= 10
    error_message = "Instance count must be between 1 and 10."
  }
}
```

This protects against invalid input.

---

# 🌎 19. Region Validation

```hcl
variable "aws_region" {
  type = string

  validation {
    condition = contains(
      ["us-east-1", "us-west-2", "eu-north-1"],
      var.aws_region
    )

    error_message = "Use an approved AWS region."
  }
}
```

Useful for enterprise governance.

---

# 📝 20. `terraform.tfvars`

Example:

```hcl
environment = "dev"

aws_region = "us-east-1"

instance_count = 2
```

Terraform automatically loads:

```text
terraform.tfvars
```

---

# 📄 21. Custom `.tfvars`

Example:

```text
dev.tfvars
staging.tfvars
prod.tfvars
```

Use:

```bash
terraform plan -var-file="dev.tfvars"
```

Apply:

```bash
terraform apply -var-file="dev.tfvars"
```

---

# 🏢 22. Environment Variable Inputs

Terraform supports environment variables using:

```text
TF_VAR_
```

Example:

```bash
export TF_VAR_environment=dev
```

Linux/macOS.

PowerShell:

```powershell
$env:TF_VAR_environment="dev"
```

Terraform reads:

```text
TF_VAR_environment
```

as:

```hcl
var.environment
```

---

# 🥇 23. Variable Precedence

Terraform can receive variable values from multiple sources.

Common sources include:

```text
1. Default values
2. Environment variables
3. *.tfvars
4. terraform.tfvars.json / auto-loaded variable files
5. *.auto.tfvars
6. -var
7. -var-file
```

Exact precedence and automatic loading behavior should be checked against the current Terraform documentation.

The important operational rule:

```text
Explicit CLI values
        ↓
usually override
        ↓
file/environment/default values
```

Do not rely on memory for complex precedence cases—verify with the current Terraform documentation.

---

# ⚠️ 24. Never Commit Secrets

Avoid:

```text
password
token
private key
API key
access key
database secret
```

inside:

```text
terraform.tfvars
*.tfvars
main.tf
variables.tf
```

If a `.tfvars` file contains secrets, add it to `.gitignore`.

---

# 🧩 25. `locals`

Locals allow reusable expressions inside a module.

Example:

```hcl
locals {
  name_prefix = "vishwatech-${var.environment}"
}
```

Use:

```hcl
resource "aws_s3_bucket" "demo" {
  bucket = "${local.name_prefix}-bucket"
}
```

Concept:

```text
Variable
   ↓
Expression
   ↓
Local
   ↓
Resource
```

---

# 🔄 26. Variable vs Local

### Variable

Input from outside:

```text
User
CI/CD
tfvars
Environment
```

### Local

Calculated inside configuration:

```text
Input
 ↓
Expression
 ↓
Local
```

Think:

```text
variable = INPUT
local    = CALCULATION / REUSE
```

---

# 📤 27. Outputs

Outputs expose useful values after Terraform operations.

Example:

```hcl
output "bucket_name" {
  value = aws_s3_bucket.demo.bucket
}
```

Run:

```bash
terraform output
```

---

# 🪣 28. S3 Output Example

```hcl
resource "aws_s3_bucket" "demo" {
  bucket = var.bucket_name
}

output "bucket_name" {
  value = aws_s3_bucket.demo.bucket
}

output "bucket_arn" {
  value = aws_s3_bucket.demo.arn
}
```

---

# 🔐 29. Sensitive Outputs

Example:

```hcl
output "database_password" {
  value     = var.database_password
  sensitive = true
}
```

Terraform may redact it in normal CLI output.

Again:

```text
Sensitive output
      ≠
Secret removed from state
```

Protect the state.

---

# 🧱 30. Complex Outputs

Example:

```hcl
output "server_details" {
  value = {
    environment = var.environment
    region      = var.aws_region
    count       = var.instance_count
  }
}
```

Output:

```text
server_details = {
  environment = "dev"
  region      = "us-east-1"
  count       = 2
}
```

---

# 📋 31. List Output

```hcl
output "availability_zones" {
  value = var.availability_zones
}
```

---

# 🗺️ 32. Map Output

```hcl
output "instance_types" {
  value = var.instance_types
}
```

---

# 🔗 33. Resource Output

```hcl
output "vpc_id" {
  value = aws_vpc.main.id
}
```

Useful for:

```text
Modules
CI/CD
Other systems
Operators
Troubleshooting
```

---

# 🧩 34. Module Outputs

A child module can expose:

```hcl
output "vpc_id" {
  value = aws_vpc.main.id
}
```

Root module:

```hcl
output "network_id" {
  value = module.vpc.vpc_id
}
```

Architecture:

```text
Child Module
    ↓
Output
    ↓
Root Module
    ↓
Output / Resource
```

---

# 🔁 35. Passing Variables to Modules

Child module:

```hcl
variable "environment" {
  type = string
}
```

Root:

```hcl
module "vpc" {
  source = "./modules/vpc"

  environment = var.environment
}
```

Flow:

```text
Root Variable
      ↓
Module Input
      ↓
Child Resource
```

---

# 🧠 36. Optional Object Attributes

Modern Terraform supports optional object attributes.

Example:

```hcl
variable "server" {
  type = object({
    name          = string
    instance_type = string
    monitoring    = optional(bool, true)
  })
}
```

Now `monitoring` can have a default.

This is useful for reusable modules.

---

# 🧮 37. `null`

Terraform uses `null` to represent absence of a value.

Example:

```hcl
variable "description" {
  type    = string
  default = null
}
```

Depending on the resource/provider argument, `null` can allow Terraform to behave as though the argument was omitted.

---

# 🧪 38. Practical Project Structure

```text
terraform-variables-demo/
│
├── provider.tf
├── main.tf
├── variables.tf
├── locals.tf
├── outputs.tf
├── terraform.tfvars
├── dev.tfvars
├── prod.tfvars
├── .gitignore
└── README.md
```

---

# 🧪 39. Real AWS Example

## `variables.tf`

```hcl
variable "aws_region" {
  description = "AWS deployment region"
  type        = string
  default     = "us-east-1"
}

variable "environment" {
  description = "Deployment environment"
  type        = string

  validation {
    condition     = contains(["dev", "staging", "prod"], var.environment)
    error_message = "Environment must be dev, staging, or prod."
  }
}

variable "bucket_name" {
  description = "Globally unique S3 bucket name"
  type        = string
}
```

---

# 🧪 40. `provider.tf`

```hcl
terraform {
  required_providers {
    aws = {
      source = "hashicorp/aws"
    }
  }
}

provider "aws" {
  region = var.aws_region
}
```

---

# 🧪 41. `locals.tf`

```hcl
locals {
  common_tags = {
    Environment = var.environment
    ManagedBy   = "Terraform"
    Project     = "VishwaTechLabs"
  }
}
```

---

# 🧪 42. `main.tf`

```hcl
resource "aws_s3_bucket" "demo" {
  bucket = var.bucket_name

  tags = local.common_tags
}
```

---

# 🧪 43. `outputs.tf`

```hcl
output "bucket_name" {
  description = "S3 bucket name"
  value       = aws_s3_bucket.demo.bucket
}

output "bucket_arn" {
  description = "S3 bucket ARN"
  value       = aws_s3_bucket.demo.arn
}
```

---

# 🧪 44. `terraform.tfvars`

```hcl
aws_region  = "us-east-1"
environment = "dev"
bucket_name = "replace-with-your-globally-unique-name"
```

---

# 🚀 45. Execute

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

Then:

```bash
terraform apply
```

Outputs:

```bash
terraform output
```

Cleanup:

```bash
terraform destroy
```

---

# 🔥 46. Command-Line Override

Default:

```hcl
environment = "dev"
```

Override:

```bash
terraform plan \
  -var="environment=staging"
```

Another:

```bash
terraform plan \
  -var="environment=prod"
```

This allows the same code to support multiple environments.

---

# 🏢 47. Environment Files

Example:

```text
env/
├── dev.tfvars
├── staging.tfvars
└── prod.tfvars
```

Run:

```bash
terraform plan -var-file="env/dev.tfvars"
```

Then:

```bash
terraform plan -var-file="env/staging.tfvars"
```

Then:

```bash
terraform plan -var-file="env/prod.tfvars"
```

Use protected CI/CD workflows for production.

---

# 🔐 48. Secure Secret Pattern

Avoid:

```text
secret in Git
```

Prefer:

```text
Secret Manager
      ↓
CI/CD Secret Store
      ↓
Temporary Runtime Input
      ↓
Terraform
```

Possible enterprise integrations:

```text
AWS Secrets Manager
AWS Systems Manager Parameter Store
HashiCorp Vault
GitHub Actions Secrets
Cloud-native secret stores
```

The correct pattern depends on whether Terraform actually needs to manage or consume the secret.

---

# ⚠️ 49. Important State Warning

Suppose:

```hcl
variable "db_password" {
  type      = string
  sensitive = true
}
```

and:

```hcl
resource "some_database" "db" {
  password = var.db_password
}
```

The value may still be stored in Terraform state if required by the provider/resource.

Therefore:

```text
sensitive = true
        ↓
CLI Redaction
        ≠
State Encryption
```

Protect:

```text
Backend
State
Access
Backups
Logs
```

---

# 🧪 50. Hands-On Labs

## Lab 01 — String Variable

Create:

```text
environment
```

---

## Lab 02 — Number Variable

Create:

```text
instance_count
```

---

## Lab 03 — Boolean Variable

Create:

```text
enable_monitoring
```

---

## Lab 04 — List

Create:

```text
availability_zones
```

---

## Lab 05 — Set

Create a set of environments.

---

## Lab 06 — Map

Create:

```text
environment → instance type
```

---

## Lab 07 — Object

Create a server configuration object.

---

## Lab 08 — Tuple

Create a tuple containing:

```text
name
count
enabled
```

---

## Lab 09 — Required Variable

Create a variable without a default.

---

## Lab 10 — Default Variable

Create a variable with a default.

---

## Lab 11 — CLI Override

Use:

```bash
-var
```

---

## Lab 12 — `.tfvars`

Create:

```text
terraform.tfvars
```

---

## Lab 13 — Environment File

Create:

```text
dev.tfvars
prod.tfvars
```

---

## Lab 14 — `TF_VAR_`

Set a variable using an environment variable.

---

## Lab 15 — Validation

Restrict:

```text
environment = dev/staging/prod
```

---

## Lab 16 — Numeric Validation

Restrict instance count.

---

## Lab 17 — Sensitive Variable

Create a sensitive test variable and observe CLI redaction.

---

## Lab 18 — Locals

Create common tags using locals.

---

## Lab 19 — Outputs

Output:

```text
bucket name
bucket ARN
region
environment
```

---

## Lab 20 — Complex Output

Create a map output.

---

## Lab 21 — Module Input

Pass a variable to a child module.

---

## Lab 22 — Module Output

Return a VPC ID from a child module.

---

## Lab 23 — Optional Object Attribute

Create a structured server variable.

---

## Lab 24 — `null`

Test an optional configuration value.

---

## Lab 25 — Multi-Environment

Use:

```text
dev
staging
prod
```

---

## Lab 26 — Tagging

Build standardized tags from variables and locals.

---

## Lab 27 — Secret Review

Identify which values must never enter Git.

---

## Lab 28 — Output JSON

Run:

```bash
terraform output -json
```

---

## Lab 29 — Variable Debugging

Intentionally supply an invalid value and troubleshoot validation.

---

## Lab 30 — Production Challenge

Build:

```text
Variables
    ↓
Validation
    ↓
Locals
    ↓
AWS Resources
    ↓
Outputs
```

using one reusable configuration.

---

# 🏢 51. Enterprise Variable Architecture

```text
                    Terraform Module
                           │
             ┌─────────────┼─────────────┐
             ▼             ▼             ▼
         Variables       Locals       Outputs
             │             │             │
             ▼             ▼             ▼
         User Input     Derived Data   Results
             │             │             │
             └─────────────┼─────────────┘
                           ▼
                       Resources
```

---

# 🌍 52. Environment Architecture

```text
                 Same Terraform Code
                         │
          ┌──────────────┼──────────────┐
          ▼              ▼              ▼
        DEV            STAGING          PROD
          │              │              │
       dev.tfvars    staging.tfvars   prod.tfvars
          │              │              │
          ▼              ▼              ▼
       AWS Dev        AWS Stage        AWS Prod
```

For stronger isolation, combine variable separation with:

```text
Separate AWS accounts
Separate state
Separate credentials
Separate CI/CD environments
```

---

# 🔐 53. Variable Security Checklist

```text
☑ Strong type constraints
☑ Validation
☑ No hard-coded secrets
☑ Sensitive flag where appropriate
☑ Secure secret source
☑ Protected state
☑ Protected tfvars
☑ Gitignore sensitive files
☑ Review outputs
☑ Avoid leaking values into logs
```

---

# 🚨 54. Common Errors

## Missing Variable

```text
No value for required variable
```

Fix:

```bash
-var
-var-file
TF_VAR_
```

or approved variable configuration.

---

## Invalid Variable

Example:

```text
environment = test
```

when validation allows only:

```text
dev
staging
prod
```

Fix the input.

---

## Variable Type Error

Example:

```hcl
instance_count = "two"
```

when:

```hcl
type = number
```

Use:

```hcl
instance_count = 2
```

---

## Sensitive Value Appears in State

Remember:

```text
sensitive
    ↓
Redaction
```

not:

```text
sensitive
    ↓
No state storage
```

Protect your backend.

---

# 🎓 55. Interview Questions

### Beginner

1. What is a Terraform variable?
2. Why do we use variables?
3. What is a default value?
4. What is a variable description?
5. What is a string variable?
6. What is a number variable?
7. What is a boolean variable?
8. What is a list?
9. What is a set?
10. What is a map?

### Intermediate

11. What is an object variable?
12. What is a tuple?
13. What is variable validation?
14. What is `terraform.tfvars`?
15. What is `*.tfvars`?
16. What is `TF_VAR_`?
17. What is a local value?
18. Variable vs local?
19. What is an output?
20. What is a sensitive variable?

### Advanced

21. What happens to sensitive values in state?
22. How would you manage secrets securely?
23. How do you design variables for reusable modules?
24. How do you manage Dev/Staging/Prod variables?
25. How does variable precedence work?
26. How would you validate enterprise-approved regions?
27. How would you pass variables into modules?
28. How would you expose values from modules?
29. How would you avoid secret leakage through outputs?
30. Design a production Terraform variable architecture.

---

# ⚡ 56. Cheat Sheet

```bash
# Plan with variable
terraform plan \
  -var="environment=dev"

# Plan with variable file
terraform plan \
  -var-file="dev.tfvars"

# Apply with variable file
terraform apply \
  -var-file="dev.tfvars"

# Outputs
terraform output
terraform output bucket_name
terraform output -json

# Environment variable
export TF_VAR_environment=dev

# PowerShell
$env:TF_VAR_environment="dev"
```

Terraform syntax:

```hcl
# Variable
var.environment

# Local
local.common_tags

# Output
output "name" {
  value = ...
}
```

---

# 🏆 57. Mastery Checklist

```text
VARIABLES
☑ string
☑ number
☑ bool
☑ list
☑ set
☑ map
☑ object
☑ tuple
☑ optional
☑ null

INPUT
☑ Defaults
☑ Required variables
☑ CLI variables
☑ tfvars
☑ auto tfvars
☑ TF_VAR_
☑ Variable precedence
☑ Validation

LOCALS
☑ Local values
☑ Derived values
☑ Common tags
☑ Reusable expressions

OUTPUTS
☑ Simple outputs
☑ Resource outputs
☑ Complex outputs
☑ Sensitive outputs
☑ Module outputs
☑ JSON outputs

SECURITY
☑ Sensitive values
☑ State protection
☑ Secret management
☑ Gitignore
☑ No hard-coded credentials

MODULES
☑ Module inputs
☑ Module outputs
☑ Reusable configuration
☑ Environment design
```

---

# 🗺️ 58. Terraform Roadmap

```text
01 Terraform Fundamentals       🟢
        ↓
02 Terraform Installation       🟢
        ↓
03 Terraform CLI & Workflow     🟢
        ↓
04 Providers & Authentication   🟢
        ↓
05 Variables & Outputs          🟢 ← YOU ARE HERE
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

# 🎛️ INPUT → PROCESS → OUTPUT

### VishwaTech Labs

**Terraform • AWS • Azure • GCP • Docker • GitHub Actions • Kubernetes • Ansible • DevSecOps**

### By Vishwanath Gowda H

⭐ Write Once • Parameterize Everything • Reuse Everywhere

</div>
