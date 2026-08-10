<div align="center">

# 🧱 Terraform Resources & Data Sources — Complete Masterclass

### ☁️ Build • Read • Reference • Connect • Manage Real Infrastructure | VishwaTech Labs

[![Terraform](https://img.shields.io/badge/Terraform-844FBA?logo=terraform&logoColor=white)](https://developer.hashicorp.com/terraform/docs)
[![AWS](https://img.shields.io/badge/AWS-Cloud-FF9900?logo=amazonaws&logoColor=white)](https://aws.amazon.com/)
[![Resources](https://img.shields.io/badge/Terraform-Resources-blue)](#-resources)
[![Data Sources](https://img.shields.io/badge/Terraform-Data%20Sources-green)](#-data-sources)
[![Labs](https://img.shields.io/badge/Labs-30+-success)](#-hands-on-labs)

**Understand the heart of Terraform: resources create/manage infrastructure, while data sources read information that already exists.**

[📘 Resources](https://developer.hashicorp.com/terraform/language/resources) •
[📖 Data Sources](https://developer.hashicorp.com/terraform/language/data-sources) •
[🧩 AWS Provider](https://registry.terraform.io/providers/hashicorp/aws/latest)

</div>

---

# 🎯 What You Will Learn

This module explains:

```text
Resources
Data Sources
Resource Types
Resource Arguments
Resource Attributes
References
Resource Addressing
Dependencies
Implicit Dependencies
Explicit Dependencies
count
for_each
Resource Iteration
Data Source Filtering
Locals + Resources
Variables + Resources
Outputs + Resources
Resource Lifecycle Concepts
Import Concepts
Real AWS Resources
Hands-on Labs
Troubleshooting
Interview Questions
```

The core mental model:

```text
                 Terraform
                     │
          ┌──────────┴──────────┐
          ▼                     ▼
      Resources             Data Sources
          │                     │
       Manage                Read
          │                     │
          ▼                     ▼
     Cloud APIs          Existing Information
```

---

# 🧠 1. What Is a Resource?

A resource block tells Terraform that it should manage a particular infrastructure object.

Example:

```hcl
resource "aws_s3_bucket" "demo" {
  bucket = var.bucket_name
}
```

Breakdown:

```text
resource
   ↓
aws_s3_bucket
   ↓
demo
   ↓
Arguments
```

---

# 🧩 2. Resource Block Anatomy

```hcl
resource "aws_instance" "web" {
  ami           = var.ami_id
  instance_type = var.instance_type

  tags = {
    Name = "web-server"
  }
}
```

Structure:

```text
resource "TYPE" "NAME" {
    ARGUMENTS
}
```

Terraform address:

```text
aws_instance.web
```

---

# 🔤 3. Resource Type

Example:

```hcl
resource "aws_instance" "web" {}
```

Resource type:

```text
aws_instance
```

It is implemented by:

```text
hashicorp/aws
```

provider.

---

# 🏷️ 4. Resource Name

Example:

```hcl
resource "aws_instance" "web" {}
```

Local Terraform name:

```text
web
```

Full resource address:

```text
aws_instance.web
```

The local name does not have to match the AWS resource name.

---

# ⚙️ 5. Resource Arguments

Arguments configure a resource.

Example:

```hcl
resource "aws_instance" "web" {
  ami           = var.ami_id
  instance_type = var.instance_type
}
```

Here:

```text
ami
instance_type
```

are arguments.

---

# 📤 6. Resource Attributes

After a resource exists, the provider exposes attributes.

Example:

```hcl
output "instance_id" {
  value = aws_instance.web.id
}
```

Here:

```text
aws_instance.web.id
```

is a resource attribute reference.

---

# 🔗 7. Resource References

Example:

```hcl
resource "aws_instance" "web" {
  subnet_id = aws_subnet.public.id
}
```

Terraform understands:

```text
aws_instance.web
       ↓
depends on
       ↓
aws_subnet.public
```

This is an **implicit dependency**.

---

# 🧠 8. Implicit Dependency

A reference creates a dependency automatically.

Example:

```hcl
resource "aws_subnet" "public" {
  vpc_id = aws_vpc.main.id
}
```

Terraform sees:

```text
aws_subnet.public
        ↓
requires
        ↓
aws_vpc.main
```

Terraform can therefore build the dependency graph.

---

# 🔗 9. Explicit Dependency

Sometimes Terraform cannot infer a dependency from a reference.

Use:

```hcl
depends_on = [
  aws_iam_role_policy.example
]
```

Example:

```hcl
resource "aws_instance" "app" {
  ami           = var.ami_id
  instance_type = "t3.micro"

  depends_on = [
    aws_iam_role_policy.example
  ]
}
```

Use `depends_on` only when the dependency is real but not naturally represented by a reference.

---

# ⚠️ 10. Avoid Unnecessary `depends_on`

Bad:

```hcl
depends_on = [
  aws_vpc.main
]
```

when the resource already references:

```hcl
vpc_id = aws_vpc.main.id
```

Terraform already understands that dependency.

Prefer:

```text
Explicit data reference
        ↓
Automatic dependency
```

over unnecessary:

```text
depends_on everywhere
```

---

# 🏗️ 11. Data Sources

A data source reads information from an external system.

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

Concept:

```text
Resource
   ↓
Manage/Create

Data Source
   ↓
Read/Discover
```

---

# 🆚 12. Resource vs Data Source

| Feature | Resource | Data Source |
|---|---|---|
| Purpose | Manage infrastructure | Read information |
| Creates | Often | No |
| Updates | Often | No direct management |
| Deletes | Often | No |
| Read | Yes | Yes |
| Example | `aws_vpc` | `aws_vpc` data source |

Mental model:

```text
resource = "I manage this"

data     = "I need information about this"
```

---

# 🔍 13. AWS Caller Identity Data Source

```hcl
data "aws_caller_identity" "current" {}
```

Output:

```hcl
output "account_id" {
  value = data.aws_caller_identity.current.account_id
}
```

Useful for:

```text
Account-aware naming
Security policies
Cross-account logic
Debugging
```

---

# 🌐 14. Existing VPC Data Source

Example:

```hcl
data "aws_vpc" "default" {
  default = true
}
```

Use:

```hcl
output "default_vpc_id" {
  value = data.aws_vpc.default.id
}
```

This does not create the VPC.

It reads an existing VPC.

---

# 🔎 15. Data Source Filtering

Data sources often support filters.

Example concept:

```hcl
data "aws_ami" "ubuntu" {
  most_recent = true

  owners = ["099720109477"]

  filter {
    name   = "name"
    values = ["ubuntu/images/hvm-ssd-gp3/*"]
  }
}
```

Always verify the filter syntax and AMI ownership for your target region and operating system.

---

# 🧠 16. Data Source Workflow

```text
Terraform
   ↓
Data Source
   ↓
Provider API
   ↓
Find Existing Information
   ↓
Return Attributes
   ↓
Terraform Configuration
```

---

# 🪣 17. S3 Resource

```hcl
resource "aws_s3_bucket" "demo" {
  bucket = var.bucket_name

  tags = {
    Environment = var.environment
  }
}
```

Terraform manages the bucket.

---

# 🪣 18. Existing S3 Bucket Data

Depending on the provider's supported data source, you can read information about an existing bucket rather than create it.

Concept:

```text
Existing Bucket
      ↓
Data Source
      ↓
Bucket Attributes
```

Always consult the current provider documentation for the exact data source available and its arguments.

---

# 🌐 19. VPC Resource

```hcl
resource "aws_vpc" "main" {
  cidr_block = "10.0.0.0/16"

  tags = {
    Name = "main-vpc"
  }
}
```

Terraform manages the VPC.

---

# 🌐 20. Subnet Resource

```hcl
resource "aws_subnet" "public" {
  vpc_id     = aws_vpc.main.id
  cidr_block = "10.0.1.0/24"

  tags = {
    Name = "public-subnet"
  }
}
```

Dependency:

```text
VPC
 ↓
Subnet
```

---

# 🛡️ 21. Security Group Resource

Example:

```hcl
resource "aws_security_group" "web" {
  name        = "web-sg"
  description = "Web security group"
  vpc_id      = aws_vpc.main.id
}
```

Use current AWS security-group rule resources/patterns appropriate to your provider version and security design.

---

# 🖥️ 22. EC2 Resource

```hcl
resource "aws_instance" "web" {
  ami           = var.ami_id
  instance_type = var.instance_type

  subnet_id = aws_subnet.public.id

  tags = {
    Name = "web-server"
  }
}
```

Dependency graph:

```text
VPC
 ↓
Subnet
 ↓
EC2
```

---

# 🧠 23. Resource Dependency Graph

Example:

```text
                 VPC
                  │
           ┌──────┴──────┐
           ▼             ▼
        Subnet        Security Group
           │             │
           └──────┬──────┘
                  ▼
                EC2
```

Terraform calculates this graph before execution.

---

# 🔁 24. Resource Lifecycle

A resource may go through:

```text
Configuration
      ↓
Plan
      ↓
Create
      ↓
Read
      ↓
Update
      ↓
Replace
      ↓
Destroy
```

Not every change requires replacement.

---

# 🟢 25. Create

If a resource does not exist:

```text
Plan:
+ create
```

Example:

```text
+ aws_vpc.main
```

---

# 🟡 26. Update

If a resource can be changed in place:

```text
Plan:
~ update
```

Example:

```text
~ aws_instance.web
```

The exact behavior depends on the resource/provider.

---

# 🔴 27. Destroy

If Terraform determines that the resource should no longer exist:

```text
Plan:
- destroy
```

---

# 🔄 28. Replace

Some changes require recreation:

```text
-/+ replace
```

Concept:

```text
Old Resource
    ↓
Destroy/Create or Create/Destroy
    ↓
New Resource
```

The exact ordering can be affected by lifecycle configuration and provider constraints.

---

# 🔢 29. `count`

`count` creates multiple instances based on a number.

Example:

```hcl
resource "aws_instance" "web" {
  count = 2

  ami           = var.ami_id
  instance_type = "t3.micro"
}
```

Addresses:

```text
aws_instance.web[0]
aws_instance.web[1]
```

---

# ⚠️ 30. `count` Index Stability

Example:

```text
count = 3

[0]
[1]
[2]
```

If list-based logic changes, indexes can shift.

For identity-based collections, `for_each` is often more stable.

---

# 🔁 31. `for_each`

Example:

```hcl
resource "aws_s3_bucket" "environment" {
  for_each = toset([
    "dev",
    "staging",
    "prod"
  ])

  bucket = "my-project-${each.key}"
}
```

Addresses:

```text
aws_s3_bucket.environment["dev"]
aws_s3_bucket.environment["staging"]
aws_s3_bucket.environment["prod"]
```

---

# 🆚 32. `count` vs `for_each`

| Feature | count | for_each |
|---|---|---|
| Input | Number / countable | Map or set |
| Address | `[0]` | `["key"]` |
| Best for | Similar anonymous instances | Identity-based instances |
| Stable keys | ❌ Index based | ✅ Key based |
| Complex per-item values | Limited | Excellent |

Rule of thumb:

```text
Need N similar resources?
        ↓
count

Need named/identity-based resources?
        ↓
for_each
```

---

# 🧩 33. `each.key` and `each.value`

Map:

```hcl
variable "buckets" {
  type = map(string)

  default = {
    logs = "logs"
    data = "data"
  }
}
```

Resource:

```hcl
resource "aws_s3_bucket" "example" {
  for_each = var.buckets

  bucket = "my-project-${each.value}"
}
```

Use:

```text
each.key
each.value
```

---

# 📋 34. Resource Addressing

Basic:

```text
aws_instance.web
```

With count:

```text
aws_instance.web[0]
```

With for_each:

```text
aws_instance.web["prod"]
```

Module:

```text
module.network.aws_vpc.main
```

Nested module:

```text
module.network.module.subnet.aws_subnet.public
```

Resource addresses are important for:

```text
State
Import
Refactoring
Targeted inspection
Troubleshooting
```

---

# 🎯 35. Resource Targeting Warning

Terraform supports:

```bash
terraform plan -target=aws_instance.web
```

and:

```bash
terraform apply -target=aws_instance.web
```

This is an advanced recovery/debugging mechanism.

Do not use targeting as a normal way to apply only "the resource I want."

It can produce an incomplete view of the required changes.

---

# 🔄 36. Resource References and Expressions

Example:

```hcl
resource "aws_vpc" "main" {
  cidr_block = var.vpc_cidr
}

resource "aws_subnet" "public" {
  vpc_id     = aws_vpc.main.id
  cidr_block = var.subnet_cidr
}
```

Terraform sees:

```text
var.vpc_cidr
       ↓
VPC
       ↓
VPC ID
       ↓
Subnet
```

---

# 🧠 37. Data Source + Resource

A common real-world pattern:

```text
Existing VPC
     ↓
Data Source
     ↓
VPC ID
     ↓
New Subnet
```

Example concept:

```hcl
data "aws_vpc" "existing" {
  default = true
}

resource "aws_subnet" "new" {
  vpc_id     = data.aws_vpc.existing.id
  cidr_block = "172.31.100.0/24"
}
```

Always verify that the chosen CIDR does not overlap the existing VPC.

---

# 🧱 38. Resource + Variable + Output

Complete pattern:

```text
Variable
   ↓
Resource
   ↓
Attribute
   ↓
Output
```

Example:

```hcl
variable "bucket_name" {
  type = string
}

resource "aws_s3_bucket" "demo" {
  bucket = var.bucket_name
}

output "bucket_arn" {
  value = aws_s3_bucket.demo.arn
}
```

---

# 🔗 39. Resource + Data Source + Local

```text
Data Source
     ↓
Local Calculation
     ↓
Resource
     ↓
Output
```

This pattern becomes very powerful in production Terraform modules.

---

# 🏢 40. Real Enterprise Example

Imagine:

```text
Existing AWS Network Team VPC
          ↓
       Data Source
          ↓
      VPC ID
          ↓
Application Terraform
          ↓
Subnet / Security Group / EC2
```

This allows:

```text
Network team
    ↓
Owns VPC

Application team
    ↓
Consumes VPC
```

This is a common separation-of-responsibility model.

---

# 🧪 41. Complete AWS Example

## `variables.tf`

```hcl
variable "vpc_cidr" {
  type    = string
  default = "10.0.0.0/16"
}

variable "subnet_cidr" {
  type    = string
  default = "10.0.1.0/24"
}

variable "environment" {
  type    = string
  default = "dev"
}
```

---

## `vpc.tf`

```hcl
resource "aws_vpc" "main" {
  cidr_block = var.vpc_cidr

  tags = {
    Name        = "main-vpc"
    Environment = var.environment
  }
}
```

---

## `subnet.tf`

```hcl
resource "aws_subnet" "public" {
  vpc_id     = aws_vpc.main.id
  cidr_block = var.subnet_cidr

  tags = {
    Name        = "public-subnet"
    Environment = var.environment
  }
}
```

---

## `outputs.tf`

```hcl
output "vpc_id" {
  value = aws_vpc.main.id
}

output "subnet_id" {
  value = aws_subnet.public.id
}
```

---

# 🚀 42. Execute the Example

```bash
terraform init
```

```bash
terraform fmt
```

```bash
terraform validate
```

```bash
terraform plan
```

```bash
terraform apply
```

Then:

```bash
terraform output
```

Cleanup:

```bash
terraform destroy
```

---

# 🧪 43. Hands-On Labs

## Lab 01 — S3 Resource

Create an S3 bucket.

## Lab 02 — VPC Resource

Create a VPC.

## Lab 03 — Subnet

Create a subnet connected to the VPC.

## Lab 04 — Security Group

Create a security group in the VPC.

## Lab 05 — EC2

Create an EC2 instance.

## Lab 06 — Resource Reference

Connect:

```text
VPC → Subnet
```

## Lab 07 — Multiple References

Connect:

```text
VPC → Subnet → EC2
```

## Lab 08 — Explicit Dependency

Create a controlled `depends_on` example.

## Lab 09 — Data Source

Read the AWS caller identity.

## Lab 10 — Default VPC Data Source

Read the default VPC.

## Lab 11 — AMI Data Source

Find an approved AMI using filters.

## Lab 12 — Data Source + Resource

Create a subnet inside an existing VPC.

## Lab 13 — Resource Output

Output a VPC ID.

## Lab 14 — Data Source Output

Output the AWS account ID.

## Lab 15 — Count

Create two test resources with `count`.

## Lab 16 — Count Address

Inspect:

```bash
terraform state list
```

## Lab 17 — for_each Set

Create multiple test buckets/resources using keys.

## Lab 18 — for_each Map

Use per-environment values.

## Lab 19 — Count vs for_each

Implement the same design both ways and compare state addresses.

## Lab 20 — Resource Addressing

Practice identifying:

```text
resource
resource[index]
resource["key"]
```

## Lab 21 — Dependency Graph

Run:

```bash
terraform graph
```

## Lab 22 — Lifecycle

Test a replacement scenario safely.

## Lab 23 — Data Discovery

Use a data source to locate existing infrastructure.

## Lab 24 — Existing VPC

Deploy into a pre-existing VPC.

## Lab 25 — Multi-Resource Architecture

Build:

```text
VPC
├── Subnet
├── Security Group
└── EC2
```

## Lab 26 — Environment Map

Create resources using:

```hcl
for_each
```

from a map.

## Lab 27 — Import Concept

Import an existing test resource and inspect its state.

## Lab 28 — Resource Refactor

Move a resource address using a `moved` block.

## Lab 29 — Dependency Troubleshooting

Create and diagnose an incorrect dependency design.

## Lab 30 — Enterprise Challenge

Design:

```text
Existing Network
      ↓
Data Source
      ↓
Application Resources
      ↓
Outputs
```

---

# 🔐 44. Resource Security

Always consider:

```text
IAM permissions
Encryption
Network exposure
Security Groups
Logging
Tags
Backups
Deletion protection
Secrets
State exposure
```

Terraform makes infrastructure reproducible.

It does not automatically make infrastructure secure.

---

# 🛡️ 45. Secure Resource Design

Example:

```text
Public Internet
      ↓
Load Balancer
      ↓
Private Application
      ↓
Database
```

Avoid:

```text
Internet
   ↓
Database
```

when the architecture does not require public access.

---

# 🏷️ 46. Standard Tags

Use locals:

```hcl
locals {
  common_tags = {
    Project     = "VishwaTechLabs"
    Environment = var.environment
    ManagedBy   = "Terraform"
  }
}
```

Then:

```hcl
tags = local.common_tags
```

Enterprise tags can include:

```text
Application
Owner
CostCenter
Environment
Project
ManagedBy
DataClassification
```

---

# 🔄 47. Resource Replacement Safety

Before applying:

```bash
terraform plan
```

Look for:

```text
-/+
```

Ask:

```text
Why is Terraform replacing this?
Will data be lost?
Will there be downtime?
Can create_before_destroy help?
Is a backup required?
```

---

# ⚠️ 48. Common Errors

## Invalid Resource Type

Example:

```text
Invalid resource type
```

Check:

```text
Provider
Provider version
Resource documentation
Provider source
```

---

## Resource Not Found

For data sources:

```text
No matching resource found
```

Check:

```text
Region
Account
Filters
IDs
Tags
Permissions
```

---

## Dependency Cycle

Terraform may report:

```text
Cycle
```

Meaning:

```text
A → B
B → A
```

Break the circular dependency.

---

## Invalid for_each

`for_each` requires an appropriate collection such as a map or set.

Check:

```hcl
for_each = var.resources
```

and ensure the value has a suitable type.

---

## Duplicate Resource Address

You cannot normally define two blocks with the same resource type/name in the same module:

```text
aws_s3_bucket.demo
aws_s3_bucket.demo
```

Rename or restructure.

---

# 🎓 49. Interview Questions

### Beginner

1. What is a Terraform resource?
2. What is a resource type?
3. What is a resource name?
4. What is a resource argument?
5. What is a resource attribute?
6. What is a data source?
7. Resource vs data source?
8. What is a resource reference?
9. What is an implicit dependency?
10. What is `depends_on`?

### Intermediate

11. What is `count`?
12. What is `for_each`?
13. `count` vs `for_each`?
14. What is resource addressing?
15. What is `each.key`?
16. What is `each.value`?
17. What is a data source filter?
18. How do data sources help consume existing infrastructure?
19. What is a resource replacement?
20. What does `-/+` mean?

### Advanced

21. How would you design Terraform around a centrally managed VPC?
22. When should you use `depends_on`?
23. Why can excessive `depends_on` be harmful?
24. How would you choose between `count` and `for_each`?
25. How do resource addresses affect state?
26. How would you safely refactor a resource address?
27. How would you diagnose a dependency cycle?
28. How would you troubleshoot a data source returning no results?
29. How would you prevent accidental replacement of critical resources?
30. Design an enterprise resource/data-source architecture.

---

# ⚡ 50. Cheat Sheet

```bash
# Initialize
terraform init

# Format
terraform fmt

# Validate
terraform validate

# Plan
terraform plan

# Apply
terraform apply

# State resources
terraform state list

# Resource details
terraform state show RESOURCE

# Providers
terraform providers

# Dependency graph
terraform graph

# Outputs
terraform output

# Destroy
terraform destroy
```

Terraform blocks:

```hcl
# Resource
resource "TYPE" "NAME" {
}

# Data Source
data "TYPE" "NAME" {
}

# Explicit Dependency
depends_on = [
  resource.address
]

# Count
count = 2

# for_each
for_each = var.items

# Reference
aws_vpc.main.id

# Data Reference
data.aws_vpc.existing.id
```

---

# 🏆 51. Mastery Checklist

```text
RESOURCES
☑ Resource block
☑ Resource type
☑ Resource name
☑ Arguments
☑ Attributes
☑ References
☑ Resource addressing
☑ Resource lifecycle
☑ Replacement

DEPENDENCIES
☑ Implicit dependency
☑ Explicit dependency
☑ depends_on
☑ Dependency graph
☑ Dependency cycles

DATA SOURCES
☑ Data source
☑ Filters
☑ Existing infrastructure
☑ Data attributes
☑ Data source + resource

ITERATION
☑ count
☑ for_each
☑ each.key
☑ each.value
☑ Stable resource identity

ARCHITECTURE
☑ Variables
☑ Locals
☑ Resources
☑ Data sources
☑ Outputs
☑ Modules
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
06 Resources & Data Sources     🟢 ← YOU ARE HERE
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

# 🧱 MANAGE WHAT YOU OWN • READ WHAT ALREADY EXISTS

### VishwaTech Labs

**Terraform • AWS • Azure • GCP • Docker • GitHub Actions • Kubernetes • Ansible • DevSecOps**

### By Vishwanath Gowda H

⭐ Resources Build. Data Sources Discover. Dependencies Connect.

</div>
