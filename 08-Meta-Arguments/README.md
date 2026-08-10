<div align="center">

# ⚙️ Terraform Meta-Arguments — Complete Masterclass

### 🚀 Control Creation • Iteration • Dependencies • Lifecycle • Providers | VishwaTech Labs

[![Terraform](https://img.shields.io/badge/Terraform-844FBA?logo=terraform&logoColor=white)](https://developer.hashicorp.com/terraform/docs)
[![Lifecycle](https://img.shields.io/badge/Lifecycle-Management-blue)](#-lifecycle-meta-argument)
[![count](https://img.shields.io/badge/count-Multiple%20Instances-green)](#-count)
[![for_each](https://img.shields.io/badge/for__each-Identity--Based-purple)](#-for_each)
[![Security](https://img.shields.io/badge/Security-Production%20Safe-red)](#-security-and-safety)
[![Labs](https://img.shields.io/badge/Labs-30+-success)](#-hands-on-labs)

**Master Terraform meta-arguments and learn how to control *how* Terraform creates, replaces, iterates, depends on, and manages resources.**

[📘 Meta-Arguments](https://developer.hashicorp.com/terraform/language/meta-arguments) •
[🔄 Lifecycle](https://developer.hashicorp.com/terraform/language/meta-arguments/lifecycle) •
[🔢 count](https://developer.hashicorp.com/terraform/language/meta-arguments/count) •
[🔁 for_each](https://developer.hashicorp.com/terraform/language/meta-arguments/for_each) •
[🔗 depends_on](https://developer.hashicorp.com/terraform/language/meta-arguments/depends_on)

</div>

---

# 🎯 What You Will Learn

This module covers:

```text
What Are Meta-Arguments?
count
count.index
for_each
each.key
each.value
count vs for_each
depends_on
provider
lifecycle
create_before_destroy
prevent_destroy
ignore_changes
replace_triggered_by
Resource Addressing
State Addresses
Instance Identity
Resource Replacement
Dependency Control
Multi-Region Providers
Multi-Account Patterns
Production Safety
Hands-on Labs
Troubleshooting
Interview Questions
```

Core idea:

```text
Normal Resource Arguments
        ↓
Describe WHAT the resource needs

Meta-Arguments
        ↓
Control HOW Terraform manages it
```

---

# 🧠 1. What Is a Meta-Argument?

A meta-argument is a Terraform language feature that changes how Terraform handles a resource or module.

Examples:

```hcl
count
for_each
depends_on
provider
lifecycle
```

These are not ordinary provider-specific resource arguments.

They are part of Terraform's configuration language.

---

# 🧩 2. Resource Arguments vs Meta-Arguments

Example:

```hcl
resource "aws_instance" "web" {
  ami           = var.ami_id
  instance_type = "t3.micro"

  count = 2
}
```

Here:

```text
ami
instance_type
    ↓
Resource arguments

count
    ↓
Meta-argument
```

---

# 🔢 3. `count`

`count` creates multiple resource instances based on a number.

Example:

```hcl
resource "aws_instance" "web" {
  count = 3

  ami           = var.ami_id
  instance_type = "t3.micro"

  tags = {
    Name = "web-${count.index}"
  }
}
```

Terraform creates:

```text
web-0
web-1
web-2
```

Addresses:

```text
aws_instance.web[0]
aws_instance.web[1]
aws_instance.web[2]
```

---

# 🔢 4. `count.index`

Inside a resource using `count`:

```hcl
count.index
```

returns the zero-based instance index.

Example:

```hcl
tags = {
  Name = "server-${count.index}"
}
```

Result:

```text
server-0
server-1
server-2
```

---

# ⚠️ 5. `count` and List Reordering

Suppose:

```text
[
  web,
  api,
  db
]
```

is mapped to:

```text
0 → web
1 → api
2 → db
```

If list order changes:

```text
[
  api,
  web,
  db
]
```

the indexes can point to different logical objects.

This can cause unnecessary resource changes.

For identity-based collections, consider `for_each`.

---

# 🔁 6. `for_each`

`for_each` creates resource instances using keys from a map or set.

Example:

```hcl
resource "aws_s3_bucket" "environment" {
  for_each = toset([
    "dev",
    "staging",
    "prod"
  ])

  bucket = "replace-with-unique-prefix-${each.key}"
}
```

Addresses:

```text
aws_s3_bucket.environment["dev"]
aws_s3_bucket.environment["staging"]
aws_s3_bucket.environment["prod"]
```

---

# 🔑 7. `each.key`

For a `for_each` resource:

```hcl
each.key
```

represents the current key.

Example:

```hcl
tags = {
  Environment = each.key
}
```

---

# 💎 8. `each.value`

For a map:

```hcl
variable "instances" {
  type = map(string)

  default = {
    web = "t3.micro"
    api = "t3.small"
    db  = "t3.medium"
  }
}
```

Resource:

```hcl
resource "aws_instance" "server" {
  for_each = var.instances

  instance_type = each.value
  ami           = var.ami_id

  tags = {
    Name = each.key
  }
}
```

Here:

```text
each.key
    ↓
web / api / db

each.value
    ↓
t3.micro / t3.small / t3.medium
```

---

# 🆚 9. `count` vs `for_each`

| Requirement | Recommended |
|---|---|
| Create N nearly identical instances | `count` |
| Instances have stable names/keys | `for_each` |
| Collection has unique identities | `for_each` |
| Simple numeric scaling | `count` |
| Different configuration per instance | `for_each` |
| Map-based infrastructure | `for_each` |

Mental model:

```text
count
  ↓
How many?

for_each
  ↓
Which named instances?
```

---

# 🧱 10. `depends_on`

`depends_on` creates an explicit dependency.

Example:

```hcl
resource "aws_instance" "app" {
  ami           = var.ami_id
  instance_type = "t3.micro"

  depends_on = [
    aws_iam_role_policy.app
  ]
}
```

Terraform will ensure the dependency is respected.

---

# 🔗 11. Implicit vs Explicit Dependencies

### Implicit

```hcl
subnet_id = aws_subnet.public.id
```

Terraform automatically understands:

```text
EC2
 ↓
Subnet
```

### Explicit

```hcl
depends_on = [
  aws_iam_role_policy.app
]
```

Use explicit dependencies when the relationship is real but not represented through a direct reference.

---

# ⚠️ 12. Do Not Overuse `depends_on`

Avoid:

```hcl
depends_on = [
  aws_vpc.main
]
```

when you already have:

```hcl
vpc_id = aws_vpc.main.id
```

Unnecessary dependencies can:

```text
Reduce parallelism
Make graphs harder to understand
Create longer plans/applies
Hide weak configuration design
```

---

# 🌍 13. `provider`

The `provider` meta-argument selects a provider configuration for a resource.

Example:

```hcl
provider "aws" {
  region = "us-east-1"
}

provider "aws" {
  alias  = "europe"
  region = "eu-north-1"
}
```

Resource:

```hcl
resource "aws_s3_bucket" "europe" {
  provider = aws.europe

  bucket = "replace-with-unique-name"
}
```

---

# 🗺️ 14. Provider Alias Architecture

```text
Terraform
   │
   ├── aws
   │    └── us-east-1
   │
   └── aws.europe
        └── eu-north-1
```

Useful for:

```text
Multi-region
Multi-account
Disaster recovery
Global infrastructure
```

---

# 🏢 15. Multi-Account Provider Pattern

Concept:

```text
Root Configuration
       │
       ├── AWS Dev Role
       │
       ├── AWS Stage Role
       │
       └── AWS Prod Role
```

Use carefully controlled provider configurations and IAM roles.

Do not place long-lived access keys in provider blocks.

---

# 🔄 16. `lifecycle`

The `lifecycle` meta-argument changes how Terraform handles resource changes.

Example:

```hcl
resource "aws_instance" "web" {
  ami           = var.ami_id
  instance_type = "t3.micro"

  lifecycle {
    create_before_destroy = true
  }
}
```

Important lifecycle settings include:

```text
create_before_destroy
prevent_destroy
ignore_changes
replace_triggered_by
precondition
postcondition
```

---

# 🔄 17. `create_before_destroy`

Default replacement can conceptually look like:

```text
Old Resource
     ↓
Destroy
     ↓
Create New
```

With:

```hcl
lifecycle {
  create_before_destroy = true
}
```

Terraform attempts:

```text
Create New
     ↓
Destroy Old
```

This can reduce downtime.

---

# ⚠️ 18. `create_before_destroy` Is Not Always Possible

Some resources have:

```text
Unique naming constraints
Dependency restrictions
Provider/API limitations
Quotas
Capacity constraints
```

Therefore:

```text
create_before_destroy
```

is a strategy, not a guarantee that every replacement can happen without interruption.

---

# 🛑 19. `prevent_destroy`

Example:

```hcl
resource "aws_s3_bucket" "critical" {
  bucket = var.bucket_name

  lifecycle {
    prevent_destroy = true
  }
}
```

Terraform will reject plans that attempt to destroy the resource while this setting applies.

Useful for:

```text
Production databases
Critical buckets
Important infrastructure
```

---

# ⚠️ 20. `prevent_destroy` Is Not a Backup

Important:

```text
prevent_destroy
      ≠
Backup
```

It protects Terraform-managed destruction paths.

It does not protect against:

```text
Manual deletion
Account compromise
Provider-side events
Data corruption
Other tools
```

Use proper backup and recovery mechanisms.

---

# 👀 21. `ignore_changes`

Example:

```hcl
resource "aws_instance" "web" {
  ami           = var.ami_id
  instance_type = "t3.micro"

  lifecycle {
    ignore_changes = [
      tags
    ]
  }
}
```

Terraform will ignore changes to the selected attribute(s) when planning updates caused by configuration/state comparison.

Use this carefully.

---

# ⚠️ 22. `ignore_changes` Can Hide Drift

If another system changes:

```text
tags
```

and Terraform ignores tags:

```text
External change
      ↓
Terraform sees
      ↓
Ignored
```

This may be intentional.

But excessive `ignore_changes` can hide real configuration drift.

---

# 🎯 23. Attribute-Specific Ignore

You can ignore specific attributes:

```hcl
lifecycle {
  ignore_changes = [
    tags["Owner"]
  ]
}
```

This is useful when an external automation system owns only a specific field.

Always verify the current Terraform/provider behavior for nested attribute syntax.

---

# 🔁 24. `replace_triggered_by`

This lifecycle setting allows a resource replacement to be triggered when referenced managed objects or attributes change.

Example concept:

```hcl
resource "aws_instance" "web" {
  ami           = var.ami_id
  instance_type = "t3.micro"

  lifecycle {
    replace_triggered_by = [
      aws_security_group.web
    ]
  }
}
```

Use this when a meaningful dependency requires replacement rather than merely an update.

---

# 🧠 25. `replace_triggered_by` Mental Model

```text
Resource A changes
      ↓
Replacement trigger
      ↓
Resource B
      ↓
Replace
```

This is different from:

```text
depends_on
```

because:

```text
depends_on
    ↓
Ordering/dependency

replace_triggered_by
    ↓
Replacement behavior
```

---

# 🧪 26. Preconditions

Terraform supports resource/module conditions that can validate assumptions before actions proceed.

Example:

```hcl
resource "aws_instance" "web" {
  ami           = var.ami_id
  instance_type = var.instance_type

  lifecycle {
    precondition {
      condition     = var.environment != "prod" || var.instance_type != "t3.micro"
      error_message = "Production must not use t3.micro."
    }
  }
}
```

This can enforce configuration assumptions.

---

# 🧪 27. Postconditions

Postconditions validate an expected result after an operation.

Concept:

```text
Terraform creates/reads resource
          ↓
Postcondition
          ↓
Expected state?
          ↓
YES → Continue
NO  → Report failure
```

Use postconditions when an important resource outcome must satisfy a known requirement.

---

# 🧩 28. Lifecycle Block Structure

Example:

```hcl
lifecycle {
  create_before_destroy = true

  prevent_destroy = false

  ignore_changes = [
    tags
  ]

  replace_triggered_by = [
    aws_security_group.web
  ]

  precondition {
    condition     = true
    error_message = "Condition failed."
  }

  postcondition {
    condition     = true
    error_message = "Postcondition failed."
  }
}
```

Do not blindly combine every lifecycle feature.

Choose only what the architecture requires.

---

# 🔢 29. Meta-Arguments with `count`

Example:

```hcl
resource "aws_instance" "web" {
  count = var.instance_count

  ami           = var.ami_id
  instance_type = var.instance_type

  tags = {
    Name = "web-${count.index}"
  }
}
```

Address:

```text
aws_instance.web[0]
```

---

# 🔁 30. Meta-Arguments with `for_each`

```hcl
resource "aws_instance" "web" {
  for_each = var.instances

  ami           = var.ami_id
  instance_type = each.value

  tags = {
    Name = each.key
  }
}
```

Address:

```text
aws_instance.web["api"]
```

---

# 🧠 31. Meta-Arguments with Modules

Many meta-arguments can also be used with module blocks.

Example:

```hcl
module "network" {
  source = "./modules/network"

  for_each = var.environments

  environment = each.key
}
```

This allows reusable modules to be instantiated multiple times.

---

# 🏗️ 32. Module `count`

```hcl
module "app" {
  source = "./modules/app"

  count = 2

  environment = var.environment
}
```

Module addresses become indexed.

---

# 🆚 33. Module `count` vs `for_each`

Same principle:

```text
count
  ↓
Numeric instances

for_each
  ↓
Named/identity-based instances
```

For environment-based modules:

```hcl
for_each = {
  dev  = {}
  prod = {}
}
```

is often easier to reason about than numeric indexes.

---

# 🧭 34. Resource Addressing

Without iteration:

```text
aws_instance.web
```

With count:

```text
aws_instance.web[0]
```

With for_each:

```text
aws_instance.web["web"]
```

Module:

```text
module.network
```

Module with count:

```text
module.network[0]
```

Module with for_each:

```text
module.network["prod"]
```

---

# 🔄 35. Refactoring `count` to `for_each`

Suppose old state:

```text
aws_instance.web[0]
aws_instance.web[1]
```

You may want:

```text
aws_instance.web["web"]
aws_instance.web["api"]
```

Do not simply change the configuration and blindly apply.

Plan the state/address migration carefully using:

```text
moved blocks
```

or controlled state operations.

---

# 🧠 36. `moved` Blocks

Example concept:

```hcl
moved {
  from = aws_instance.web[0]
  to   = aws_instance.web["web"]
}
```

This tells Terraform:

```text
Old address
     ↓
New address
```

without treating the change as a real infrastructure replacement.

Use this for safe refactoring when the underlying object is intended to remain the same.

---

# 🔥 37. Real Example — Production EC2

```hcl
resource "aws_instance" "app" {
  ami           = var.ami_id
  instance_type = var.instance_type

  lifecycle {
    create_before_destroy = true

    prevent_destroy = true

    ignore_changes = [
      tags["Owner"]
    }
  }

  tags = {
    Name        = "production-app"
    Environment = "prod"
    ManagedBy   = "Terraform"
  }
}
```

Use such settings only after understanding their operational consequences.

---

# 🪣 38. Real Example — Critical S3

```hcl
resource "aws_s3_bucket" "critical" {
  bucket = var.bucket_name

  lifecycle {
    prevent_destroy = true
  }

  tags = {
    Environment = "prod"
    DataClass   = "critical"
  }
}
```

Still implement:

```text
Versioning
Backup/recovery strategy
Access control
Encryption
Monitoring
```

as appropriate.

---

# 🌐 39. Real Example — Multi-Region

```hcl
provider "aws" {
  region = "us-east-1"
}

provider "aws" {
  alias  = "dr"
  region = "eu-north-1"
}

resource "aws_s3_bucket" "primary" {
  provider = aws

  bucket = "replace-with-unique-name"
}

resource "aws_s3_bucket" "dr" {
  provider = aws.dr

  bucket = "replace-with-unique-name"
}
```

---

# 🔗 40. Real Example — Explicit Dependency

Suppose:

```text
Policy
  ↓
Application
```

and there is no direct attribute reference.

Then:

```hcl
resource "aws_instance" "app" {
  ami           = var.ami_id
  instance_type = "t3.micro"

  depends_on = [
    aws_iam_role_policy.app
  ]
}
```

Use this only if the dependency represents a real operational requirement.

---

# 🧠 41. Meta-Argument Decision Tree

```text
Need multiple resources?
        │
        ├── Simple numeric count?
        │       ↓
        │     count
        │
        └── Named identities?
                ↓
              for_each

Need ordering?
        │
        ↓
     depends_on

Need another provider?
        │
        ↓
      provider

Need replacement behavior?
        │
        ↓
     lifecycle
```

---

# 🔐 42. Security and Safety

Meta-arguments can prevent mistakes, but they do not replace security architecture.

Use:

```text
Least privilege
Protected state
Backups
Approvals
Separate accounts
Separate environments
CI/CD controls
Policy as code
Monitoring
```

---

# 🚨 43. Common Mistakes

## Mistake 1 — Using `count` for Identity

Bad design:

```text
0 = web
1 = api
2 = database
```

when identities matter.

Consider:

```text
for_each
```

---

## Mistake 2 — Overusing `depends_on`

This creates unnecessary dependencies.

---

## Mistake 3 — `ignore_changes` Everywhere

This can hide drift.

---

## Mistake 4 — Assuming `prevent_destroy` Is Backup

It isn't.

---

## Mistake 5 — Blind `create_before_destroy`

Some resources cannot safely support it.

---

## Mistake 6 — Using `-target` as Normal Workflow

Targeting is not a replacement for good dependency modeling.

---

## Mistake 7 — Changing Addresses Without Migration

Changing:

```text
count
```

to:

```text
for_each
```

can cause Terraform to propose replacements if state addresses are not migrated.

---

# 🧪 44. Hands-On Labs

## Lab 01 — Basic `count`

Create 3 test instances/resources.

## Lab 02 — `count.index`

Generate unique names.

## Lab 03 — Count Scaling

Change:

```text
3 → 5
```

and inspect the plan.

## Lab 04 — Reduce Count

Change:

```text
5 → 2
```

and observe which instances Terraform plans to remove.

## Lab 05 — `for_each` Set

Create resources for:

```text
dev
stage
prod
```

## Lab 06 — `for_each` Map

Assign different instance types.

## Lab 07 — `each.key`

Use keys in resource names.

## Lab 08 — `each.value`

Use values in configuration.

## Lab 09 — Count vs for_each

Build both implementations and compare addresses.

## Lab 10 — Explicit Dependency

Create a real dependency that Terraform cannot infer.

## Lab 11 — Remove Unnecessary Dependency

Replace `depends_on` with a direct resource reference.

## Lab 12 — Provider Alias

Deploy resources to two regions.

## Lab 13 — Provider Alias + Module

Pass multiple provider configurations to a module.

## Lab 14 — create_before_destroy

Test a safe replacement scenario.

## Lab 15 — prevent_destroy

Attempt a controlled destroy and observe the protection.

## Lab 16 — ignore_changes

Ignore a controlled tag change.

## Lab 17 — Remove ignore_changes

Observe the difference.

## Lab 18 — replace_triggered_by

Create a controlled replacement dependency.

## Lab 19 — Precondition

Prevent invalid production instance types.

## Lab 20 — Postcondition

Validate a resource result.

## Lab 21 — Module count

Create multiple module instances.

## Lab 22 — Module for_each

Create modules for environments.

## Lab 23 — Resource Addresses

Inspect:

```bash
terraform state list
```

## Lab 24 — Moved Block

Rename a resource address without replacement.

## Lab 25 — Count → for_each Migration

Perform a controlled state/address migration.

## Lab 26 — Multi-Region

Create primary + DR resources.

## Lab 27 — Production Protection

Combine carefully selected lifecycle settings.

## Lab 28 — Dependency Graph

Inspect:

```bash
terraform graph
```

## Lab 29 — Targeting Recovery Exercise

Use `-target` only in a controlled recovery scenario and document why.

## Lab 30 — Enterprise Challenge

Build:

```text
Multi-Environment
       ↓
for_each
       ↓
Provider Aliases
       ↓
Lifecycle Rules
       ↓
Dependencies
       ↓
Protected Production
```

---

# 🏢 45. Enterprise Architecture

```text
                         Terraform
                             │
          ┌──────────────────┼──────────────────┐
          ▼                  ▼                  ▼
       Resources           Modules           Providers
          │                  │                  │
     ┌────┴────┐        ┌────┴────┐       ┌────┴────┐
     ▼         ▼        ▼         ▼       ▼         ▼
   count    for_each   count    for_each  Dev      Prod
     │         │
     └────┬────┘
          ▼
      Lifecycle
          │
   ┌──────┼────────┐
   ▼      ▼        ▼
 create  prevent  ignore
 before  destroy  changes
 destroy
```

---

# 🔐 46. Production Safety Checklist

Before applying lifecycle changes:

```text
☑ Read terraform plan
☑ Understand replacement behavior
☑ Check downtime
☑ Check dependencies
☑ Check quotas
☑ Check naming constraints
☑ Check backups
☑ Check state
☑ Check provider behavior
☑ Test in lower environment
☑ Protect production credentials
☑ Require approval where appropriate
```

---

# 🎓 47. Interview Questions

### Beginner

1. What is a Terraform meta-argument?
2. What is `count`?
3. What is `count.index`?
4. What is `for_each`?
5. What is `each.key`?
6. What is `each.value`?
7. What is `depends_on`?
8. What is the `provider` meta-argument?
9. What is `lifecycle`?
10. What is `prevent_destroy`?

### Intermediate

11. `count` vs `for_each`?
12. Why can `count` cause index problems?
13. When should you use `for_each`?
14. What is `create_before_destroy`?
15. What is `ignore_changes`?
16. What is `replace_triggered_by`?
17. What is a provider alias?
18. How do explicit and implicit dependencies differ?
19. How do lifecycle rules affect replacement?
20. What is a resource address?

### Advanced

21. How would you migrate `count` to `for_each` safely?
22. How do `moved` blocks help refactoring?
23. Why can excessive `ignore_changes` be dangerous?
24. When is `depends_on` appropriate?
25. How would you design multi-account provider aliases?
26. How would you protect production infrastructure from accidental deletion?
27. How would you reduce downtime during replacement?
28. What limitations can affect `create_before_destroy`?
29. How would you use preconditions in enterprise Terraform?
30. Design a production Terraform meta-argument strategy.

---

# ⚡ 48. Cheat Sheet

```hcl
# Count
count = 3

# Count index
count.index

# for_each
for_each = var.resources

# Key/value
each.key
each.value

# Explicit dependency
depends_on = [
  aws_iam_role_policy.app
]

# Provider selection
provider = aws.dr

# Lifecycle
lifecycle {
  create_before_destroy = true

  prevent_destroy = true

  ignore_changes = [
    tags
  ]

  replace_triggered_by = [
    aws_security_group.web
  ]
}

# Preconditions
lifecycle {
  precondition {
    condition     = var.environment != "prod"
    error_message = "Invalid production configuration."
  }
}

# Postconditions
lifecycle {
  postcondition {
    condition     = self.id != ""
    error_message = "Resource ID was not returned."
  }
}

# Moved address
moved {
  from = aws_instance.web[0]
  to   = aws_instance.web["web"]
}
```

---

# 🏆 49. Mastery Checklist

```text
COUNT
☑ count
☑ count.index
☑ Scaling
☑ Addressing
☑ Index stability

FOR_EACH
☑ Map
☑ Set
☑ each.key
☑ each.value
☑ Stable identity

DEPENDENCIES
☑ Implicit
☑ Explicit
☑ depends_on
☑ Dependency graph

PROVIDERS
☑ provider meta-argument
☑ Aliases
☑ Multi-region
☑ Multi-account

LIFECYCLE
☑ create_before_destroy
☑ prevent_destroy
☑ ignore_changes
☑ replace_triggered_by
☑ precondition
☑ postcondition

REFACTORING
☑ Resource addresses
☑ count → for_each
☑ moved blocks
☑ State awareness

SAFETY
☑ Plan review
☑ Replacement review
☑ Backup
☑ Drift awareness
☑ Production protection
```

---

# 🗺️ 50. Terraform Roadmap

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
08 Meta-Arguments               🟢 ← YOU ARE HERE
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

# ⚙️ CONTROL HOW TERRAFORM BUILDS

### VishwaTech Labs

**Terraform • AWS • Azure • GCP • Docker • GitHub Actions • Kubernetes • Ansible • DevSecOps**

### By Vishwanath Gowda H

⭐ Iterate Smart • Depend Correctly • Protect Production

</div>
