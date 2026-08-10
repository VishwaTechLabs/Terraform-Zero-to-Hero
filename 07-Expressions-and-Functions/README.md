<div align="center">

# 🧠 Terraform Expressions & Functions — Complete Masterclass

### ⚡ Think • Transform • Calculate • Iterate • Generate Dynamic Infrastructure | VishwaTech Labs

[![Terraform](https://img.shields.io/badge/Terraform-844FBA?logo=terraform&logoColor=white)](https://developer.hashicorp.com/terraform/docs)
[![HCL](https://img.shields.io/badge/HCL-Expressions-blue)](https://developer.hashicorp.com/terraform/language/expressions)
[![Functions](https://img.shields.io/badge/Functions-50%2B-success)](#-function-families)
[![AWS](https://img.shields.io/badge/AWS-Cloud-FF9900?logo=amazonaws&logoColor=white)](https://aws.amazon.com/)
[![Labs](https://img.shields.io/badge/Labs-30+-purple)](#-hands-on-labs)

**Master Terraform's expression language and built-in functions so your configurations become dynamic, reusable, readable, and production-ready.**

[📘 Expressions](https://developer.hashicorp.com/terraform/language/expressions) •
[🧮 Functions](https://developer.hashicorp.com/terraform/language/functions) •
[🔢 Operators](https://developer.hashicorp.com/terraform/language/expressions/operators)

</div>

---

# 🎯 What You Will Learn

This module covers:

```text
Expressions
Operators
Arithmetic
Comparison
Logical Operators
Conditional Expressions
String Templates
String Functions
Collection Functions
Map Functions
Set Functions
Numeric Functions
Encoding Functions
Filesystem Functions
Date/Time Functions
Type Conversion
lookup
try
can
merge
flatten
concat
toset
tomap
tolist
coalesce
coalescelist
contains
length
keys
values
element
distinct
sort
join
split
replace
lower
upper
trim
format
jsonencode
jsondecode
cidrsubnet
cidrhost
for Expressions
Nested for Expressions
Filtering
Dynamic Values
Expression Precedence
terraform console
Real AWS Examples
Hands-on Labs
Troubleshooting
Interview Questions
```

---

# 🧠 1. What Is an Expression?

An expression produces a value.

Example:

```hcl
var.environment
```

Expression:

```hcl
var.environment == "prod"
```

Result:

```text
true
```

Another:

```hcl
var.instance_count + 1
```

Result:

```text
number
```

Mental model:

```text
Input
  ↓
Expression
  ↓
Value
```

---

# 🧩 2. Expression Categories

Terraform expressions include:

```text
Literal Values
References
Operators
Conditional Expressions
For Expressions
Function Calls
Indexing
Attribute Access
Splat Expressions
```

---

# 🔢 3. Literal Values

String:

```hcl
"Terraform"
```

Number:

```hcl
100
```

Boolean:

```hcl
true
```

List:

```hcl
["dev", "stage", "prod"]
```

Map:

```hcl
{
  environment = "dev"
}
```

---

# 🔗 4. References

Variable:

```hcl
var.environment
```

Local:

```hcl
local.common_tags
```

Resource:

```hcl
aws_vpc.main.id
```

Data source:

```hcl
data.aws_caller_identity.current.account_id
```

Module:

```hcl
module.vpc.vpc_id
```

---

# ➕ 5. Arithmetic Operators

Terraform supports arithmetic operators such as:

```text
+
-
*
/
%
```

Examples:

```hcl
10 + 5
10 - 5
10 * 5
10 / 5
10 % 3
```

---

# 🆚 6. Comparison Operators

```text
==
!=
>
>=
<
<=
```

Examples:

```hcl
var.environment == "prod"
```

```hcl
var.instance_count >= 2
```

---

# 🧠 7. Logical Operators

```text
&&
||
!
```

Example:

```hcl
var.environment == "prod" && var.enable_monitoring
```

Another:

```hcl
var.environment == "prod" || var.environment == "staging"
```

---

# ❓ 8. Conditional Expressions

Terraform supports:

```hcl
condition ? true_value : false_value
```

Example:

```hcl
var.environment == "prod" ? "large" : "small"
```

Result:

```text
prod
  ↓
large

dev
  ↓
small
```

---

# 🏗️ 9. Conditional Resource Creation

Example:

```hcl
count = var.enable_monitoring ? 1 : 0
```

Architecture:

```text
enable_monitoring
       │
   ┌───┴───┐
 true     false
   │        │
 count=1  count=0
```

---

# 📝 10. String Templates

Example:

```hcl
"vpc-${var.environment}"
```

Result:

```text
vpc-dev
```

Multiple expressions:

```hcl
"${var.project}-${var.environment}-${var.region}"
```

Prefer clear interpolation and direct expressions where possible.

---

# 🔤 11. String Functions

Useful functions:

```text
lower
upper
trim
trimspace
replace
substr
join
split
format
formatlist
startswith
endswith
strcontains
```

---

# 🔡 12. `lower`

```hcl
lower("VISHWATECH")
```

Result:

```text
vishwatech
```

Useful for:

```text
Naming
Tags
Environment normalization
```

---

# 🔠 13. `upper`

```hcl
upper("terraform")
```

Result:

```text
TERRAFORM
```

---

# 🧹 14. `trimspace`

```hcl
trimspace("   terraform   ")
```

Result:

```text
terraform
```

Useful for cleaning user input.

---

# 🔄 15. `replace`

```hcl
replace("vishwa-tech-labs", "-", "_")
```

Result:

```text
vishwa_tech_labs
```

---

# ✂️ 16. `substr`

```hcl
substr("Terraform", 0, 4)
```

Result:

```text
Terr
```

Always verify indexes carefully.

---

# 🔗 17. `join`

```hcl
join("-", ["vishwa", "tech", "labs"])
```

Result:

```text
vishwa-tech-labs
```

---

# ✂️ 18. `split`

```hcl
split("-", "vishwa-tech-labs")
```

Result:

```text
["vishwa", "tech", "labs"]
```

---

# 🎨 19. `format`

```hcl
format("%s-%s", var.project, var.environment)
```

Result:

```text
project-dev
```

Useful for consistent names.

---

# 📋 20. `formatlist`

Example:

```hcl
formatlist("subnet-%s", ["public", "private"])
```

Concept:

```text
[
  "subnet-public",
  "subnet-private"
]
```

---

# 📏 21. `length`

```hcl
length(["dev", "stage", "prod"])
```

Result:

```text
3
```

Can also be used with strings and other supported collections.

---

# 🔎 22. `contains`

```hcl
contains(
  ["dev", "stage", "prod"],
  var.environment
)
```

Result:

```text
true / false
```

Excellent for variable validation.

---

# 🗺️ 23. `lookup`

Example:

```hcl
lookup(
  var.instance_types,
  var.environment,
  "t3.micro"
)
```

Meaning:

```text
Find key
   ↓
If found → value
   ↓
Otherwise → default
```

---

# 🧠 24. `try`

`try` evaluates expressions and returns the first one that succeeds.

Example:

```hcl
try(var.server.name, "default-server")
```

Useful when dealing with optional/variable structures.

Use it deliberately.

Avoid using `try` everywhere to hide real configuration errors.

---

# 🛡️ 25. `can`

`can` tests whether an expression can be evaluated successfully.

Example:

```hcl
can(var.server.name)
```

Result:

```text
true / false
```

Often useful in validation logic.

---

# 🔀 26. `merge`

Merge maps:

```hcl
merge(
  {
    Environment = "dev"
  },
  {
    ManagedBy = "Terraform"
  }
)
```

Result:

```text
{
  Environment = "dev"
  ManagedBy   = "Terraform"
}
```

Very useful for standardized tags.

---

# 🧩 27. `flatten`

Example:

```hcl
flatten([
  ["subnet-a", "subnet-b"],
  ["subnet-c"]
])
```

Result:

```text
[
  "subnet-a",
  "subnet-b",
  "subnet-c"
]
```

Useful for nested collections.

---

# 🔗 28. `concat`

```hcl
concat(
  ["dev"],
  ["stage", "prod"]
)
```

Result:

```text
["dev", "stage", "prod"]
```

---

# 🧮 29. `distinct`

```hcl
distinct([
  "dev",
  "dev",
  "prod"
])
```

Result:

```text
["dev", "prod"]
```

Useful for removing duplicates.

---

# 🔤 30. `sort`

```hcl
sort([
  "prod",
  "dev",
  "stage"
])
```

Result:

```text
["dev", "prod", "stage"]
```

Useful when deterministic ordering matters.

---

# 🔢 31. `toset`

```hcl
toset([
  "dev",
  "dev",
  "prod"
])
```

Result:

```text
{"dev", "prod"}
```

Useful with:

```hcl
for_each
```

when unique keys are desired.

---

# 🗺️ 32. `tomap`

Converts a compatible value into a map.

Example:

```hcl
tomap({
  dev  = "t3.micro"
  prod = "t3.medium"
})
```

Useful when explicit map typing/conversion is required.

---

# 📋 33. `tolist`

Converts a compatible value to a list.

Example:

```hcl
tolist(toset(["dev", "prod"]))
```

Remember:

```text
set
  ↓
unordered / unique

list
  ↓
ordered
```

---

# 🧮 34. `coalesce`

Returns the first non-null value.

Example:

```hcl
coalesce(
  var.custom_name,
  "default-name"
)
```

Useful for optional values.

---

# 📚 35. `coalescelist`

Returns the first non-empty list.

Example:

```hcl
coalescelist(
  var.custom_subnets,
  var.default_subnets
)
```

Useful for fallback collections.

---

# 🔐 36. `jsonencode`

Convert Terraform values to JSON:

```hcl
jsonencode({
  Environment = var.environment
  ManagedBy   = "Terraform"
})
```

Useful for:

```text
IAM policies
User data
Application configuration
API payloads
```

---

# 📥 37. `jsondecode`

Convert JSON into Terraform values.

Example:

```hcl
jsondecode(file("config.json"))
```

Use carefully with files containing secrets.

---

# 🌐 38. CIDR Functions

Important for networking:

```text
cidrsubnet
cidrhost
cidrnetmask
```

---

# 🧮 39. `cidrsubnet`

Example:

```hcl
cidrsubnet("10.0.0.0/16", 8, 1)
```

Concept:

```text
10.0.0.0/16
      ↓
Create smaller subnet
      ↓
10.0.1.0/24
```

This is extremely useful for dynamic VPC designs.

---

# 🏠 40. `cidrhost`

Example:

```hcl
cidrhost("10.0.1.0/24", 10)
```

Returns an address within the CIDR range.

Useful for:

```text
Network calculations
Host addressing
Automation
```

---

# 🔁 41. For Expressions

For expressions transform collections.

Example:

```hcl
[for name in var.names : upper(name)]
```

Input:

```text
["dev", "stage", "prod"]
```

Output:

```text
["DEV", "STAGE", "PROD"]
```

---

# 🗺️ 42. For Expression with Map

```hcl
{
  for environment in var.environments :
  environment => upper(environment)
}
```

Result:

```text
{
  dev  = "DEV"
  stage = "STAGE"
  prod = "PROD"
}
```

---

# 🔍 43. Filtering with For Expressions

Example:

```hcl
[
  for env in var.environments :
  env
  if env != "dev"
]
```

Input:

```text
dev
stage
prod
```

Output:

```text
stage
prod
```

---

# 🧩 44. For Expression + Transformation

```hcl
[
  for name in var.names :
  "server-${lower(name)}"
]
```

Result:

```text
server-web
server-api
server-db
```

---

# 🏗️ 45. For Expression + Map

Example:

```hcl
{
  for instance in var.instances :
  instance.name => instance.type
}
```

This pattern is excellent for:

```text
for_each
```

---

# 🔁 46. Nested For Expressions

Example:

```hcl
flatten([
  for environment in var.environments : [
    for region in var.regions :
    "${environment}-${region}"
  ]
])
```

Result concept:

```text
dev-us-east-1
dev-eu-north-1
prod-us-east-1
prod-eu-north-1
```

This is useful for matrix-style infrastructure generation.

---

# 🧠 47. Splat Expressions

Example:

```hcl
aws_instance.web[*].id
```

This collects attributes from multiple resource instances.

Useful with resources created using:

```text
count
```

and compatible collection-shaped values.

---

# 🔢 48. Indexing

List:

```hcl
var.availability_zones[0]
```

Map:

```hcl
var.instance_types["prod"]
```

Resource:

```hcl
aws_instance.web[0].id
```

---

# 🧭 49. Expression Precedence

Terraform evaluates expressions according to operator precedence.

Use parentheses when readability matters:

```hcl
(var.count + 1) * 2
```

instead of relying on readers to infer precedence.

Best practice:

```text
Readable
Explicit
Simple
```

---

# 🧮 50. `terraform console`

The best way to experiment with expressions:

```bash
terraform console
```

Try:

```text
> 10 + 5
15
```

```text
> lower("TERRAFORM")
"terraform"
```

```text
> length(["dev", "prod"])
2
```

```text
> cidrsubnet("10.0.0.0/16", 8, 1)
"10.0.1.0/24"
```

---

# 🏗️ 51. Real AWS Naming Example

Variables:

```hcl
variable "project" {
  type    = string
  default = "vishwatech"
}

variable "environment" {
  type    = string
  default = "dev"
}
```

Local:

```hcl
locals {
  name_prefix = lower(
    "${var.project}-${var.environment}"
  )
}
```

Use:

```hcl
tags = {
  Name = local.name_prefix
}
```

Result:

```text
vishwatech-dev
```

---

# 🏷️ 52. Dynamic Tags with `merge`

```hcl
locals {
  common_tags = {
    Project     = "VishwaTech"
    ManagedBy   = "Terraform"
    Environment = var.environment
  }

  extra_tags = {
    Owner = var.owner
  }

  tags = merge(
    local.common_tags,
    local.extra_tags
  )
}
```

Use:

```hcl
tags = local.tags
```

---

# 🌐 53. Dynamic Subnet Calculation

Variables:

```hcl
variable "vpc_cidr" {
  type    = string
  default = "10.0.0.0/16"
}
```

Create subnet CIDRs:

```hcl
locals {
  public_subnets = [
    for index in range(3) :
    cidrsubnet(var.vpc_cidr, 8, index)
  ]
}
```

Concept:

```text
10.0.0.0/16
     ↓
10.0.0.0/24
10.0.1.0/24
10.0.2.0/24
```

---

# 🧱 54. Dynamic Resources

Example:

```hcl
variable "buckets" {
  type = set(string)

  default = [
    "logs",
    "data",
    "backup"
  ]
}
```

Resource:

```hcl
resource "aws_s3_bucket" "this" {
  for_each = var.buckets

  bucket = "${var.project}-${each.key}"
}
```

This combines:

```text
Variables
+
for_each
+
Expressions
+
String interpolation
```

---

# 🧠 55. Dynamic IAM Policy JSON

Example:

```hcl
locals {
  policy = jsonencode({
    Version = "2012-10-17"

    Statement = [{
      Effect   = "Allow"
      Action   = ["s3:GetObject"]
      Resource = "arn:aws:s3:::${var.bucket_name}/*"
    }]
  })
}
```

Useful for dynamically generated JSON documents.

---

# 🔐 56. Function Security

Be careful with:

```text
file()
filebase64()
jsondecode()
templatefile()
```

when handling:

```text
Secrets
Private keys
Credentials
Tokens
Sensitive configuration
```

Remember:

```text
Terraform expression
      ↓
May enter
      ↓
Plan / State / Logs / Resource API
```

---

# 🧩 57. `templatefile`

Example:

```hcl
user_data = templatefile(
  "${path.module}/user-data.sh.tftpl",
  {
    environment = var.environment
  }
)
```

Template:

```text
#!/bin/bash

echo "Environment: ${environment}"
```

Useful for:

```text
EC2 user data
Configuration files
Startup scripts
Application templates
```

---

# 📁 58. Filesystem Functions

Common functions:

```text
file
filebase64
fileset
templatefile
```

Example:

```hcl
file("${path.module}/config.txt")
```

These operate on files available to Terraform.

---

# 📅 59. Date/Time Functions

Terraform has functions such as:

```text
timestamp
formatdate
timeadd
```

Example:

```hcl
timestamp()
```

Important:

Using time-dependent expressions carelessly can cause plans to change repeatedly.

Use timestamps only when the behavior is intentional.

---

# 🔢 60. Numeric Functions

Examples:

```text
abs
ceil
floor
max
min
log
pow
signum
```

Example:

```hcl
max(2, 5, 3)
```

Result:

```text
5
```

---

# 🧠 61. Function Categories

```text
STRING
├── lower
├── upper
├── trim
├── replace
├── substr
├── join
├── split
└── format

COLLECTION
├── length
├── concat
├── flatten
├── distinct
├── sort
├── contains
├── merge
├── keys
└── values

TYPE
├── tolist
├── toset
├── tomap
├── tonumber
├── tostring
└── tobool

LOGIC
├── try
├── can
├── coalesce
└── coalescelist

ENCODING
├── jsonencode
├── jsondecode
├── base64encode
└── base64decode

NETWORK
├── cidrsubnet
├── cidrhost
└── cidrnetmask

FILES
├── file
├── filebase64
├── fileset
└── templatefile

NUMERIC
├── abs
├── ceil
├── floor
├── max
├── min
└── pow
```

---

# 🧪 62. Hands-On Labs

## Lab 01 — Arithmetic

Practice:

```hcl
10 + 5
10 * 2
```

## Lab 02 — Comparison

Build:

```hcl
var.environment == "prod"
```

## Lab 03 — Logical Operators

Combine:

```text
environment
monitoring
```

## Lab 04 — Conditional

Create a production/non-production value.

## Lab 05 — String Functions

Practice:

```text
lower
upper
trimspace
replace
```

## Lab 06 — Join/Split

Convert:

```text
list ↔ string
```

## Lab 07 — Lookup

Map environment to instance type.

## Lab 08 — Try

Safely access an optional attribute.

## Lab 09 — Can

Validate expression availability.

## Lab 10 — Merge

Create standardized tags.

## Lab 11 — Flatten

Flatten nested subnet lists.

## Lab 12 — Concat

Combine multiple lists.

## Lab 13 — Distinct

Remove duplicate values.

## Lab 14 — Sort

Create deterministic ordering.

## Lab 15 — Type Conversion

Practice:

```text
tolist
toset
tomap
```

## Lab 16 — Coalesce

Build fallback values.

## Lab 17 — JSON Encode

Create an IAM-style policy document.

## Lab 18 — JSON Decode

Read a controlled JSON configuration.

## Lab 19 — CIDR Subnet

Generate three VPC subnets.

## Lab 20 — CIDR Host

Calculate host addresses.

## Lab 21 — For Expression

Transform a list.

## Lab 22 — For + Filter

Filter production environments.

## Lab 23 — For + Map

Create a map from a list.

## Lab 24 — Nested For

Generate environment × region combinations.

## Lab 25 — Splat

Collect instance IDs.

## Lab 26 — Template File

Generate EC2 user data.

## Lab 27 — Dynamic Tags

Use `merge`.

## Lab 28 — Dynamic S3

Use:

```text
for_each
expressions
functions
```

## Lab 29 — Dynamic VPC

Generate subnet CIDRs using `cidrsubnet`.

## Lab 30 — Enterprise Challenge

Build:

```text
Variables
   ↓
Expressions
   ↓
Functions
   ↓
Locals
   ↓
for_each
   ↓
AWS Resources
   ↓
Outputs
```

---

# 🏢 63. Enterprise Pattern

```text
User Input
    │
    ▼
Variables
    │
    ▼
Validation
    │
    ▼
Expressions
    │
    ▼
Functions
    │
    ▼
Locals
    │
    ▼
Resources
    │
    ▼
Cloud Infrastructure
```

This is where Terraform starts becoming a real programming language for infrastructure configuration.

---

# 🔐 64. Best Practices

```text
☑ Keep expressions readable
☑ Use locals for repeated calculations
☑ Use functions intentionally
☑ Validate user input
☑ Prefer clear names
☑ Avoid deeply nested expressions
☑ Avoid try() as an error-hiding mechanism
☑ Avoid time-dependent values unless intentional
☑ Use terraform console for experiments
☑ Keep security-sensitive data out of source control
☑ Test CIDR calculations
☑ Prefer deterministic collection handling
```

---

# 🚨 65. Common Errors

## Invalid Function Argument

Check:

```text
Function documentation
Argument type
Argument count
```

---

## Invalid Index

Example:

```hcl
var.list[10]
```

when the list has fewer elements.

Use:

```text
length()
```

and validation where appropriate.

---

## Type Mismatch

Example:

```text
string
```

provided where:

```text
number
```

is expected.

Use explicit conversion only when semantically correct.

---

## `for_each` Key Problems

Remember:

```text
for_each
    ↓
Needs stable, appropriate keys
```

Avoid accidentally deriving unstable keys from changing values.

---

## CIDR Errors

Check:

```text
Parent CIDR
New bits
Subnet number
Overlap
Address range
```

---

# 🎓 66. Interview Questions

### Beginner

1. What is a Terraform expression?
2. What is an operator?
3. What is a conditional expression?
4. What is string interpolation?
5. What is `length`?
6. What is `lookup`?
7. What is `merge`?
8. What is `flatten`?
9. What is `toset`?
10. What is `join`?

### Intermediate

11. What is `try`?
12. What is `can`?
13. What is `coalesce`?
14. What is `jsonencode`?
15. What is `cidrsubnet`?
16. What is a for expression?
17. How do you filter a for expression?
18. What is a splat expression?
19. How do you dynamically generate tags?
20. How do you calculate subnet CIDRs?

### Advanced

21. How would you design dynamic multi-region Terraform?
22. How would you use `for_each` with maps generated by expressions?
23. How would you avoid unstable resource keys?
24. When should `try` not be used?
25. How would you safely generate IAM JSON?
26. How would you build a dynamic VPC subnet architecture?
27. How would you test complex expressions?
28. How can expressions accidentally expose secrets?
29. How would you design readable Terraform expressions?
30. Design an enterprise dynamic infrastructure configuration.

---

# ⚡ 67. Cheat Sheet

```hcl
# Conditional
condition ? true_value : false_value

# Reference
var.name
local.name
resource.type.name.attribute
data.type.name.attribute
module.name.output

# String
lower("ABC")
upper("abc")
trimspace(" text ")
replace("a-b", "-", "_")
join("-", ["a", "b"])
split("-", "a-b")

# Collections
length(var.items)
contains(var.items, "prod")
concat(list1, list2)
flatten(list)
distinct(list)
sort(list)
merge(map1, map2)

# Types
tolist(value)
toset(value)
tomap(value)

# Safe/fallback
try(expression, fallback)
can(expression)
coalesce(value1, value2)
coalescelist(list1, list2)

# Encoding
jsonencode(value)
jsondecode(value)

# Networking
cidrsubnet("10.0.0.0/16", 8, 1)
cidrhost("10.0.1.0/24", 10)

# For
[for x in var.items : upper(x)]

# Filter
[for x in var.items : x if x != "dev"]

# Map
{for x in var.items : x => upper(x)}

# Console
terraform console
```

---

# 🏆 68. Mastery Checklist

```text
EXPRESSIONS
☑ Literals
☑ References
☑ Operators
☑ Conditionals
☑ String templates
☑ Indexing
☑ Splat

OPERATORS
☑ Arithmetic
☑ Comparison
☑ Logical

STRINGS
☑ lower
☑ upper
☑ trimspace
☑ replace
☑ substr
☑ join
☑ split
☑ format

COLLECTIONS
☑ length
☑ contains
☑ lookup
☑ merge
☑ flatten
☑ concat
☑ distinct
☑ sort
☑ keys
☑ values

LOGIC
☑ try
☑ can
☑ coalesce
☑ coalescelist

TYPE CONVERSION
☑ tolist
☑ toset
☑ tomap

ENCODING
☑ jsonencode
☑ jsondecode

NETWORKING
☑ cidrsubnet
☑ cidrhost
☑ cidrnetmask

ITERATION
☑ for
☑ filtering
☑ map generation
☑ nested for
☑ dynamic resource generation

ADVANCED
☑ templatefile
☑ terraform console
☑ dynamic tags
☑ dynamic CIDRs
☑ expression security
```

---

# 🗺️ 69. Terraform Roadmap

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
07 Expressions & Functions      🟢 ← YOU ARE HERE
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

# 🧠 EXPRESSIONS → LOGIC → FUNCTIONS → DYNAMIC INFRASTRUCTURE

### VishwaTech Labs

**Terraform • AWS • Azure • GCP • Docker • GitHub Actions • Kubernetes • Ansible • DevSecOps**

### By Vishwanath Gowda H

⭐ Think in Expressions. Build Dynamic Infrastructure.

</div>
