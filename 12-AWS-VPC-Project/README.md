<div align="center">

# 🧩 Terraform Modules — Complete Masterclass

### ♻️ Build Once • Reuse Everywhere • Version Everything | VishwaTech Labs

[![Terraform](https://img.shields.io/badge/Terraform-844FBA?logo=terraform&logoColor=white)](https://developer.hashicorp.com/terraform/docs)
[![Modules](https://img.shields.io/badge/Modules-Reusable%20Infrastructure-blue)](#-what-is-a-terraform-module)
[![AWS](https://img.shields.io/badge/AWS-Cloud-FF9900?logo=amazonaws&logoColor=white)](https://aws.amazon.com/)
[![Registry](https://img.shields.io/badge/Registry-Modules-success)](#-terraform-registry-modules)
[![Labs](https://img.shields.io/badge/Labs-30+-purple)](#-hands-on-labs)

**Learn how to transform Terraform configurations into reusable, versioned, testable infrastructure building blocks.**

[📘 Modules](https://developer.hashicorp.com/terraform/language/modules) •
[📦 Module Sources](https://developer.hashicorp.com/terraform/language/modules/sources) •
[🏛️ Registry](https://registry.terraform.io/)

</div>

---

# 🎯 What You Will Learn

```text
What Is a Module?
Root Module
Child Module
Module Structure
main.tf
variables.tf
outputs.tf
locals.tf
README.md
Module Inputs
Module Outputs
Module Composition
Local Modules
Git Modules
Registry Modules
Module Versioning
Source Addresses
Module Providers
Provider Aliases
count with Modules
for_each with Modules
Nested Modules
Module Dependencies
Module Refactoring
Module State Addresses
Moved Blocks
Module Security
Module Design Patterns
AWS VPC Module
AWS EC2 Module
AWS S3 Module
Module Testing
Module Documentation
30 Hands-on Labs
Troubleshooting
Interview Questions
Enterprise Architecture
```

---

# 🧠 1. What Is a Terraform Module?

A Terraform module is a collection of Terraform configuration files grouped together for reuse.

Mental model:

```text
Terraform Configuration
        ↓
      Module
        ↓
Reusable Infrastructure
```

Instead of repeating:

```text
VPC code
Subnet code
Route code
Security Group code
```

you can create:

```text
VPC Module
```

and reuse it.

---

# 🏠 2. Real-World Analogy

Think about building houses.

Without modules:

```text
House 1
 ├── Foundation
 ├── Electrical
 ├── Plumbing

House 2
 ├── Foundation
 ├── Electrical
 ├── Plumbing

House 3
 ├── Foundation
 ├── Electrical
 ├── Plumbing
```

With modules:

```text
House Module
      ↓
Reusable Blueprint
      ↓
House 1
House 2
House 3
```

Terraform modules are reusable infrastructure blueprints.

---

# 🧩 3. Root Module

The directory where Terraform is executed is the root module.

Example:

```text
project/
├── main.tf
├── variables.tf
├── outputs.tf
└── providers.tf
```

Run:

```bash
terraform init
terraform plan
terraform apply
```

The current configuration is the root module.

---

# 🧱 4. Child Module

A child module is called by another module.

Example:

```hcl
module "network" {
  source = "./modules/network"
}
```

Architecture:

```text
Root Module
     │
     ├── Network Module
     │
     ├── EC2 Module
     │
     └── S3 Module
```

---

# 📁 5. Standard Module Structure

Recommended:

```text
modules/
└── network/
    ├── main.tf
    ├── variables.tf
    ├── outputs.tf
    ├── locals.tf
    ├── versions.tf
    └── README.md
```

A larger module may contain:

```text
modules/network/
├── main.tf
├── variables.tf
├── outputs.tf
├── locals.tf
├── versions.tf
├── data.tf
├── validation.tf
├── README.md
└── examples/
```

Do not create files just for the sake of having files. Organize around clarity.

---

# 🧠 6. `main.tf`

Typically contains the primary resources.

Example:

```hcl
resource "aws_vpc" "this" {
  cidr_block = var.vpc_cidr

  tags = {
    Name = var.name
  }
}
```

---

# 🎛️ 7. `variables.tf`

Defines module inputs.

```hcl
variable "name" {
  description = "Name of the VPC"
  type        = string
}

variable "vpc_cidr" {
  description = "VPC CIDR"
  type        = string
}
```

---

# 📤 8. `outputs.tf`

Defines values exposed to the caller.

```hcl
output "vpc_id" {
  description = "VPC ID"
  value       = aws_vpc.this.id
}
```

Caller:

```hcl
module.network.vpc_id
```

---

# 🧮 9. `locals.tf`

Use locals for reusable calculations.

```hcl
locals {
  common_tags = {
    Project   = var.project
    ManagedBy = "Terraform"
  }
}
```

Resource:

```hcl
tags = local.common_tags
```

---

# 🔢 10. `versions.tf`

A module can declare Terraform/provider requirements.

Example:

```hcl
terraform {
  required_version = ">= 1.5.0"

  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 6.0"
    }
  }
}
```

Choose constraints based on the versions your project actually supports.

---

# 📦 11. Calling a Local Module

Root module:

```hcl
module "network" {
  source = "./modules/network"

  name     = "vishwatech-dev"
  vpc_cidr = "10.0.0.0/16"
}
```

Architecture:

```text
root/
│
├── main.tf
│
└── modules/
    └── network/
        ├── main.tf
        ├── variables.tf
        └── outputs.tf
```

---

# 🔗 12. Module Inputs

The caller passes values:

```hcl
module "network" {
  source = "./modules/network"

  name     = var.name
  vpc_cidr = var.vpc_cidr
}
```

Inside module:

```hcl
var.name
var.vpc_cidr
```

Think:

```text
Root Module
     │
     │ inputs
     ▼
Child Module
```

---

# 📤 13. Module Outputs

Child module:

```hcl
output "vpc_id" {
  value = aws_vpc.this.id
}
```

Root module:

```hcl
output "network_vpc_id" {
  value = module.network.vpc_id
}
```

Architecture:

```text
Child Resource
     ↓
Module Output
     ↓
Root Module
     ↓
Root Output
```

---

# 🔄 14. Module Composition

A powerful pattern:

```text
Root
 │
 ├── Network
 │    ├── VPC
 │    ├── Subnets
 │    └── Routes
 │
 ├── Security
 │    └── Security Groups
 │
 └── Application
      ├── EC2
      └── Load Balancer
```

Each module has a focused responsibility.

---

# 🧱 15. Module Boundaries

Good module:

```text
network
```

owns:

```text
VPC
Subnets
Routes
Gateways
```

Another:

```text
application
```

owns:

```text
Compute
Load Balancer
Application configuration
```

Avoid creating modules so tiny that the architecture becomes difficult to understand.

---

# 🧠 16. Module Design Principle

A good module should have:

```text
Clear purpose
Stable interface
Useful inputs
Useful outputs
Minimal hidden behavior
Good documentation
Versioning
Tests/examples
```

Think:

```text
Module = Product
```

It has:

```text
API → variables
Output → outputs
Implementation → resources
Documentation → README
Version → Git/Registry
```

---

# 📥 17. Module Source Types

Terraform supports module sources such as:

```text
Local paths
Git repositories
GitHub
Terraform Registry
Other supported VCS/archive sources
```

Examples:

```hcl
source = "./modules/network"
```

Git:

```hcl
source = "git::https://github.com/example/network.git?ref=v1.0.0"
```

Registry:

```hcl
source  = "terraform-aws-modules/vpc/aws"
version = "~> 6.0"
```

Always verify the module's current documentation and supported version before use.

---

# 🌐 18. Terraform Registry Modules

Registry modules provide reusable community/vendor modules.

Typical syntax:

```hcl
module "vpc" {
  source  = "terraform-aws-modules/vpc/aws"
  version = "~> 6.0"

  # inputs
}
```

Advantages:

```text
Reusable
Community reviewed
Documented
Versioned
Faster development
```

But:

```text
Do not blindly trust third-party modules.
```

Review:

```text
Source
Version
Resources
Permissions
Security
Dependencies
Release history
```

---

# 🔒 19. Pin Module Versions

Avoid:

```hcl
source = "terraform-aws-modules/vpc/aws"
```

for production when versioning is available.

Prefer:

```hcl
version = "~> 6.0"
```

or another deliberate constraint.

For Git:

```hcl
?ref=v1.2.0
```

Predictability matters.

---

# 🏷️ 20. Semantic Versioning

Common pattern:

```text
MAJOR.MINOR.PATCH
```

Example:

```text
v2.4.1
```

Meaning conceptually:

```text
2 → major
4 → minor
1 → patch
```

A major version may introduce breaking interface changes.

---

# 🧩 21. Module Interface

Think:

```text
                 MODULE
        ┌─────────────────────┐
Inputs →│                     │→ Outputs
        │    Resources        │
        │    Data Sources     │
        │    Locals            │
        └─────────────────────┘
```

Inputs:

```text
Variables
```

Outputs:

```text
Outputs
```

Implementation:

```text
Resources
Data Sources
Locals
```

---

# 🔐 22. Variable Validation

Example:

```hcl
variable "environment" {
  type = string

  validation {
    condition = contains(
      ["dev", "stage", "prod"],
      var.environment
    )

    error_message = "Environment must be dev, stage, or prod."
  }
}
```

This creates a strong module contract.

---

# 🧠 23. Optional Inputs

Terraform supports optional object attributes and defaults in supported type expressions.

Example:

```hcl
variable "settings" {
  type = object({
    instance_type = optional(string, "t3.micro")
    monitoring    = optional(bool, true)
  })
}
```

Use optional attributes to keep interfaces manageable.

Avoid creating hundreds of optional switches.

---

# 🏷️ 24. Module Tags

A reusable module can standardize tags:

```hcl
variable "tags" {
  type    = map(string)
  default = {}
}

locals {
  tags = merge(
    var.tags,
    {
      ManagedBy = "Terraform"
    }
  )
}
```

Be clear about which tags the caller controls and which are module-enforced.

---

# 🌐 25. AWS VPC Module — Basic

Variables:

```hcl
variable "name" {
  type = string
}

variable "cidr" {
  type = string
}
```

Resource:

```hcl
resource "aws_vpc" "this" {
  cidr_block = var.cidr

  tags = {
    Name = var.name
  }
}
```

Output:

```hcl
output "vpc_id" {
  value = aws_vpc.this.id
}
```

Caller:

```hcl
module "vpc" {
  source = "./modules/vpc"

  name = "vishwatech-dev"
  cidr = "10.0.0.0/16"
}
```

---

# 🌐 26. AWS VPC Module — Production Pattern

A mature VPC module may expose:

```text
VPC CIDR
Availability Zones
Public subnet CIDRs
Private subnet CIDRs
Database subnet CIDRs
NAT gateway strategy
DNS settings
Tags
Flow logs
Endpoints
```

Architecture:

```text
VPC Module
│
├── VPC
├── Internet Gateway
├── Public Subnets
├── Private Subnets
├── Route Tables
├── NAT
├── Network ACLs
└── Outputs
```

---

# 🖥️ 27. AWS EC2 Module

Inputs:

```hcl
variable "ami_id" {
  type = string
}

variable "instance_type" {
  type = string
}

variable "subnet_id" {
  type = string
}
```

Resource:

```hcl
resource "aws_instance" "this" {
  ami           = var.ami_id
  instance_type = var.instance_type
  subnet_id     = var.subnet_id
}
```

Output:

```hcl
output "instance_id" {
  value = aws_instance.this.id
}
```

---

# 🪣 28. AWS S3 Module

A simple module can expose:

```text
Bucket name
Versioning
Encryption
Tags
Lifecycle configuration
Access controls
```

Example:

```hcl
variable "bucket_name" {
  type = string
}

resource "aws_s3_bucket" "this" {
  bucket = var.bucket_name
}

output "bucket_id" {
  value = aws_s3_bucket.this.id
}
```

For production, follow current AWS S3 security recommendations and the provider's resource model.

---

# 🔢 29. `count` with Modules

Example:

```hcl
module "app" {
  source = "./modules/app"

  count = 2

  environment = "dev"
}
```

Addresses:

```text
module.app[0]
module.app[1]
```

Use when numeric instances are appropriate.

---

# 🔁 30. `for_each` with Modules

Example:

```hcl
module "app" {
  source = "./modules/app"

  for_each = {
    web = "t3.small"
    api = "t3.medium"
  }

  instance_type = each.value
  name          = each.key
}
```

Addresses:

```text
module.app["web"]
module.app["api"]
```

Excellent for identity-based module instances.

---

# 🔌 31. Passing Providers to Modules

A module can receive provider configurations from its caller.

Root:

```hcl
provider "aws" {
  region = "us-east-1"
}

provider "aws" {
  alias  = "dr"
  region = "eu-north-1"
}
```

Module call:

```hcl
module "dr" {
  source = "./modules/network"

  providers = {
    aws = aws.dr
  }
}
```

Provider requirements should be declared appropriately inside the module.

---

# 🌍 32. Multi-Region Modules

Architecture:

```text
Root
 │
 ├── Network → us-east-1
 │
 └── Network → eu-north-1
```

Each module instance can receive the appropriate provider configuration.

Useful for:

```text
DR
Global applications
Regional infrastructure
Multi-region data services
```

---

# 🔗 33. Module Dependencies

Modules can have implicit dependencies through values.

Example:

```hcl
module "network" {
  source = "./modules/network"
}
```

Then:

```hcl
module "app" {
  source    = "./modules/app"
  subnet_id = module.network.private_subnet_id
}
```

Terraform understands:

```text
Network
   ↓
Application
```

Prefer value references over unnecessary `depends_on`.

---

# ⚠️ 34. Module `depends_on`

Terraform supports dependencies on module blocks.

Example:

```hcl
module "app" {
  source = "./modules/app"

  depends_on = [
    module.network
  ]
}
```

Use only when a real dependency exists that is not represented by input/output references.

---

# 🧠 35. Nested Modules

A module can call another module:

```text
Root
 │
 └── Platform Module
       │
       ├── Network Module
       ├── Security Module
       └── Compute Module
```

Be careful with deep nesting.

A useful rule:

```text
Prefer understandable composition
over clever abstraction.
```

---

# 🧱 36. Module Composition Pattern

Enterprise:

```text
platform/
│
├── network
├── security
├── identity
├── observability
└── application
```

Root environment:

```text
environments/prod/
       │
       └── main.tf
              │
              ├── network
              ├── security
              └── application
```

---

# 🏗️ 37. Module Repository Pattern

A dedicated module repository:

```text
terraform-aws-vpc/
├── main.tf
├── variables.tf
├── outputs.tf
├── versions.tf
├── README.md
├── CHANGELOG.md
├── examples/
│   ├── basic/
│   └── complete/
└── tests/
```

This is suitable for modules that are shared across many projects.

---

# 📚 38. Module Documentation

A good README should contain:

```text
Description
Architecture
Requirements
Providers
Inputs
Outputs
Usage
Examples
Versioning
Security
Upgrade Guide
Changelog
```

Example:

```markdown
## Usage

module "network" {
  source = "..."

  name = "prod"
}
```

---

# 🧪 39. Module Examples

Create examples:

```text
examples/
├── basic/
│   ├── main.tf
│   └── README.md
│
└── complete/
    ├── main.tf
    └── README.md
```

Examples are valuable for:

```text
Documentation
Validation
Learning
Regression testing
```

---

# 🔬 40. Testing Modules

A module should be tested before production use.

Testing levels:

```text
terraform fmt
terraform validate
terraform plan
Integration tests
Policy checks
Security scanning
```

Terraform also has testing capabilities in modern versions.

Use the official testing documentation for the exact syntax supported by your Terraform version.

---

# 🔍 41. Module Security Review

Before using a third-party module:

```text
☑ Source verified
☑ Version pinned
☑ Git/Registry origin trusted
☑ Resources reviewed
☑ IAM reviewed
☑ Data sources reviewed
☑ External commands reviewed
☑ Provider requirements reviewed
☑ Outputs reviewed
☑ Sensitive values reviewed
```

Never assume:

```text
Popular module = automatically safe
```

---

# 🔐 42. Sensitive Module Outputs

If a module returns a secret:

```hcl
output "password" {
  value     = aws_db_instance.this.password
  sensitive = true
}
```

Important:

```text
sensitive = true
```

helps prevent accidental display in normal CLI output.

It does **not** mean the value is removed from Terraform state.

Protect the state backend accordingly.

---

# 🧠 43. Module Version Upgrade

Suppose:

```text
v1.2.0
```

becomes:

```text
v2.0.0
```

Before upgrading:

```text
Read changelog
Review breaking changes
Review provider requirements
Test in dev
Run plan
Review replacements
Test staging
Then production
```

Never blindly upgrade a production module.

---

# 🔄 44. Module Refactoring

Suppose resource:

```text
aws_vpc.main
```

moves into:

```text
module.network.aws_vpc.main
```

If the infrastructure object remains the same, use a `moved` block where appropriate:

```hcl
moved {
  from = aws_vpc.main
  to   = module.network.aws_vpc.main
}
```

Then:

```bash
terraform plan
```

should show a state address move rather than unnecessary replacement when the migration is correct.

---

# 🧩 45. Module State Addresses

Root:

```text
module.network
```

Resource:

```text
module.network.aws_vpc.this
```

With `for_each`:

```text
module.network["prod"].aws_vpc.this
```

With nested module:

```text
module.platform.module.network.aws_vpc.this
```

Understanding addresses is critical for refactoring.

---

# ⚡ 46. Module Anti-Patterns

Avoid:

```text
❌ One module containing the entire company
❌ 100+ unrelated variables
❌ Hidden resource creation
❌ Excessive nested modules
❌ Unpinned external modules
❌ Hardcoded credentials
❌ Provider configuration hidden unexpectedly
❌ Outputs for everything
❌ Every tiny resource becoming a module
❌ Breaking interface changes without versioning
```

---

# 🏆 47. Good vs Bad Module

### Bad

```text
MegaModule
 ├── VPC
 ├── EKS
 ├── RDS
 ├── IAM
 ├── S3
 ├── CloudFront
 ├── Lambda
 └── Everything
```

### Better

```text
Network Module
Security Module
Data Module
Compute Module
Application Module
```

Then compose them at the environment/platform layer.

---

# 🏢 48. Enterprise Module Architecture

```text
                    GitHub
                      │
                      ▼
              Module Repositories
                      │
        ┌─────────────┼─────────────┐
        ▼             ▼             ▼
   Network Module  Security      Compute
        │             │             │
        └─────────────┼─────────────┘
                      ▼
                Platform Layer
                      │
             ┌────────┴────────┐
             ▼                 ▼
           DEV               PROD
             │                 │
             ▼                 ▼
         AWS Account       AWS Account
```

---

# 🧠 49. Module Contract

Think like an API:

```text
             MODULE API
                 │
      ┌──────────┴──────────┐
      ▼                     ▼
   Inputs                 Outputs
      │                     │
      ▼                     ▼
 variables              outputs
      │                     │
      └──────────┬──────────┘
                 ▼
            Implementation
```

If you change:

```text
variable name
variable type
required input
output name
output meaning
```

you may be changing the module API.

Treat it like software.

---

# 🧪 50. Hands-On Labs

## Lab 01 — Create First Module

Build a simple AWS VPC module.

## Lab 02 — Add Variables

Parameterize:

```text
name
cidr
```

## Lab 03 — Add Outputs

Expose:

```text
vpc_id
```

## Lab 04 — Local Module

Call:

```text
./modules/vpc
```

## Lab 05 — Add Tags

Create reusable tag inputs.

## Lab 06 — Validation

Validate environment values.

## Lab 07 — Optional Object

Create configurable module settings.

## Lab 08 — VPC Subnets

Add public/private subnet support.

## Lab 09 — EC2 Module

Build reusable EC2 module.

## Lab 10 — S3 Module

Build reusable S3 module.

## Lab 11 — Module Composition

Combine:

```text
VPC
Security
EC2
```

## Lab 12 — `for_each` Module

Create application modules for:

```text
web
api
worker
```

## Lab 13 — `count` Module

Create multiple test instances.

## Lab 14 — Provider Alias

Deploy module to another region.

## Lab 15 — Multi-Region Modules

Create:

```text
primary
DR
```

## Lab 16 — Git Module

Consume a Git-hosted module.

## Lab 17 — Versioned Git Module

Pin a release/tag.

## Lab 18 — Registry Module

Consume a Registry module.

## Lab 19 — Registry Version Pinning

Test a version constraint.

## Lab 20 — Module Documentation

Build a professional README.

## Lab 21 — Examples

Create:

```text
basic
complete
```

examples.

## Lab 22 — Module Validation

Run:

```bash
terraform fmt
terraform validate
```

## Lab 23 — Module Testing

Build Terraform tests supported by your Terraform version.

## Lab 24 — Module Refactoring

Move resources into a module using `moved`.

## Lab 25 — Module Upgrade

Upgrade a module version safely.

## Lab 26 — Security Review

Review a third-party module.

## Lab 27 — Sensitive Outputs

Practice sensitive output handling.

## Lab 28 — Nested Modules

Build controlled composition.

## Lab 29 — Enterprise Platform Module

Create:

```text
network
security
compute
```

composition.

## Lab 30 — Enterprise Challenge

Build:

```text
Root
 │
 ├── Network Module
 ├── Security Module
 ├── Compute Module
 └── Data Module
       │
       ▼
   AWS Infrastructure
```

with:

```text
Versioning
Remote State
Provider Aliases
CI/CD
Security
Testing
Documentation
```

---

# 🏆 51. Production Module Checklist

```text
☑ Clear module purpose
☑ Stable interface
☑ Typed variables
☑ Variable validation
☑ Useful outputs
☑ Minimal hidden behavior
☑ Provider requirements
☑ Versioning
☑ Documentation
☑ Examples
☑ Tests
☑ Security review
☑ Upgrade strategy
☑ Changelog
☑ No credentials
☑ No accidental destructive defaults
```

---

# 🚨 52. Troubleshooting

## Module Not Found

Check:

```text
source
path
Git URL
Registry namespace
network access
```

---

## Version Constraint Error

Check:

```text
Terraform version
Provider version
Module version
Dependency constraints
```

---

## Provider Configuration Error

Check:

```text
required_providers
provider aliases
providers map
root provider configuration
```

---

## Unexpected Resource Replacement

Check:

```text
module version
resource address
moved blocks
input changes
provider changes
state
```

Run:

```bash
terraform plan
```

and inspect the exact resource address.

---

## Module Dependency Cycle

Architecture:

```text
Module A
   ↓
Module B
   ↓
Module A
```

Break the cycle by redesigning module boundaries or passing only necessary values.

---

# 🎓 53. Interview Questions

### Beginner

1. What is a Terraform module?
2. What is the root module?
3. What is a child module?
4. What are module inputs?
5. What are module outputs?
6. What is `source`?
7. What is a local module?
8. What is a Registry module?
9. Why use modules?
10. What is module versioning?

### Intermediate

11. How do you pass variables to modules?
12. How do you consume module outputs?
13. How do `count` and `for_each` work with modules?
14. How do provider aliases work with modules?
15. How do modules depend on each other?
16. How do you structure an AWS VPC module?
17. How do you validate module inputs?
18. How do you test modules?
19. How do you document modules?
20. How do you secure third-party modules?

### Advanced

21. Design a reusable enterprise VPC module.
22. How would you version a shared module?
23. How would you migrate resources into a module?
24. How do `moved` blocks help module refactoring?
25. How would you design multi-region modules?
26. How would you manage module breaking changes?
27. How would you design module interfaces?
28. When should a resource NOT become a module?
29. How would you review a third-party module for security?
30. Design an enterprise Terraform module ecosystem.

---

# ⚡ 54. Cheat Sheet

Local module:

```hcl
module "network" {
  source = "./modules/network"

  name = "dev"
}
```

Git:

```hcl
module "network" {
  source = "git::https://github.com/example/network.git?ref=v1.0.0"
}
```

Registry:

```hcl
module "vpc" {
  source  = "terraform-aws-modules/vpc/aws"
  version = "~> 6.0"
}
```

Input:

```hcl
name = var.name
```

Output:

```hcl
module.network.vpc_id
```

Count:

```hcl
count = 2
```

For each:

```hcl
for_each = var.environments
```

Provider:

```hcl
providers = {
  aws = aws.dr
}
```

Refactoring:

```hcl
moved {
  from = aws_vpc.main
  to   = module.network.aws_vpc.main
}
```

Validation:

```bash
terraform fmt
terraform validate
terraform plan
```

---

# 🏆 55. Mastery Checklist

```text
MODULE FUNDAMENTALS
☑ Root module
☑ Child module
☑ Module structure
☑ Inputs
☑ Outputs
☑ Locals

SOURCES
☑ Local
☑ Git
☑ Registry
☑ Version pinning

DESIGN
☑ Module boundaries
☑ Composition
☑ Interface design
☑ Validation
☑ Optional attributes
☑ Tags

ADVANCED
☑ count
☑ for_each
☑ Provider aliases
☑ Nested modules
☑ Dependencies
☑ moved blocks

OPERATIONS
☑ Version upgrades
☑ Documentation
☑ Examples
☑ Testing
☑ Security review

ENTERPRISE
☑ Module repositories
☑ Version lifecycle
☑ Multi-region
☑ Multi-account
☑ CI/CD
☑ Remote state
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
09 State & State Management     🟢
        ↓
10 Remote State & Locking       🟢
        ↓
11 Terraform Modules            🟢 ← YOU ARE HERE
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

# 🧩 BUILD ONCE • REUSE EVERYWHERE

### VishwaTech Labs

**Terraform • AWS • Azure • GCP • Docker • GitHub Actions • Kubernetes • Ansible • DevSecOps**

### By Vishwanath Gowda H

⭐ Infrastructure as Code • Reusable Modules • Enterprise Automation

</div>
