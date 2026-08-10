<div align="center">

# 🌐 Terraform AWS VPC — Complete Real-World Project

### 🏗️ Build a Production-Style AWS Network from Scratch | VishwaTech Labs

[![Terraform](https://img.shields.io/badge/Terraform-844FBA?logo=terraform&logoColor=white)](https://developer.hashicorp.com/terraform/docs)
[![AWS VPC](https://img.shields.io/badge/AWS-VPC-FF9900?logo=amazonaws&logoColor=white)](https://aws.amazon.com/vpc/)
[![IaC](https://img.shields.io/badge/IaC-Terraform-blue)](#-project-goals)
[![Networking](https://img.shields.io/badge/Networking-VPC-green)](#-architecture)
[![Labs](https://img.shields.io/badge/Labs-30+-purple)](#-hands-on-labs)

**A complete hands-on Terraform + AWS networking project covering VPC, subnets, routing, Internet Gateway, NAT Gateway, NACLs, Security Groups, outputs, modules, state, validation and production practices.**

[📘 AWS VPC](https://docs.aws.amazon.com/vpc/) •
[📘 Terraform AWS Provider](https://registry.terraform.io/providers/hashicorp/aws/latest/docs) •
[📘 Terraform](https://developer.hashicorp.com/terraform/docs)

</div>

---

# 🎯 Project Goal

Build this architecture using Terraform:

```text
                         INTERNET
                            │
                            ▼
                 ┌─────────────────────┐
                 │ Internet Gateway    │
                 │       (IGW)         │
                 └──────────┬──────────┘
                            │
                    ┌───────▼───────┐
                    │     VPC       │
                    │ 10.0.0.0/16   │
                    └───────┬───────┘
                            │
             ┌──────────────┴──────────────┐
             │                             │
             ▼                             ▼
      PUBLIC SUBNETS                 PRIVATE SUBNETS
      10.0.1.0/24                    10.0.11.0/24
      10.0.2.0/24                    10.0.12.0/24
             │                             │
             ▼                             ▼
      Load Balancer / NAT             App / Database
             │                             │
             └──────────────┬──────────────┘
                            │
                         AWS Cloud
```

---

# 🧠 1. What Is a VPC?

Amazon VPC provides a logically isolated virtual network in AWS.

Think of it as:

```text
AWS Account
     │
     ▼
   VPC
     │
 ┌───┴───────────────┐
 ▼                   ▼
Public              Private
Subnets             Subnets
```

A VPC gives you control over:

```text
IP addressing
Subnets
Routing
Gateways
Network security
Connectivity
```

---

# 🏠 2. Simple Analogy

Imagine:

```text
2400 sq.ft Office
       ↓
      VPC
```

Rooms:

```text
Reception       → Public Subnet
Engineering     → Private Subnet
Database Room   → Private DB Subnet
Security Room   → Network Controls
Main Gate       → Internet Gateway
```

Terraform becomes the architect that creates the entire office automatically.

---

# 📐 3. CIDR Design

Primary VPC:

```text
10.0.0.0/16
```

Conceptually:

```text
10.0.0.0 ─────────────── 10.0.255.255
```

This provides:

```text
65,536 IPv4 addresses
```

AWS reserves addresses in each subnet, so usable addresses are fewer than the mathematical CIDR total.

---

# 🧩 4. Subnet Plan

Example:

| Purpose | CIDR | Type |
|---|---|---|
| Public AZ-A | `10.0.1.0/24` | Public |
| Public AZ-B | `10.0.2.0/24` | Public |
| Private AZ-A | `10.0.11.0/24` | Private |
| Private AZ-B | `10.0.12.0/24` | Private |
| DB AZ-A | `10.0.21.0/24` | Private |
| DB AZ-B | `10.0.22.0/24` | Private |

Architecture:

```text
VPC 10.0.0.0/16
│
├── Public
│   ├── 10.0.1.0/24
│   └── 10.0.2.0/24
│
├── Private
│   ├── 10.0.11.0/24
│   └── 10.0.12.0/24
│
└── Database
    ├── 10.0.21.0/24
    └── 10.0.22.0/24
```

---

# 🌍 5. Availability Zones

Production networks commonly span multiple Availability Zones.

Example:

```text
Region: eu-north-1

AZ-A
 ├── Public Subnet
 ├── Private Subnet
 └── DB Subnet

AZ-B
 ├── Public Subnet
 ├── Private Subnet
 └── DB Subnet
```

Benefits:

```text
High availability
Failure isolation
Load distribution
```

---

# 🚪 6. Internet Gateway

Internet Gateway connects a VPC to the internet.

Architecture:

```text
Internet
   │
   ▼
Internet Gateway
   │
   ▼
VPC
```

Terraform:

```hcl
resource "aws_internet_gateway" "this" {
  vpc_id = aws_vpc.this.id

  tags = {
    Name = "${var.name}-igw"
  }
}
```

---

# 🛣️ 7. Route Tables

Public route:

```text
0.0.0.0/0
      ↓
Internet Gateway
```

Terraform:

```hcl
resource "aws_route_table" "public" {
  vpc_id = aws_vpc.this.id

  route {
    cidr_block = "0.0.0.0/0"
    gateway_id = aws_internet_gateway.this.id
  }
}
```

---

# 🏙️ 8. Public Subnet

A subnet is considered public when its routing provides a path toward an Internet Gateway and its resources have appropriate public addressing.

Example:

```hcl
resource "aws_subnet" "public" {
  vpc_id            = aws_vpc.this.id
  cidr_block        = "10.0.1.0/24"
  availability_zone = var.az_a

  tags = {
    Name = "${var.name}-public-a"
  }
}
```

---

# 🔒 9. Private Subnet

Private subnet:

```text
No direct route to Internet Gateway
```

For outbound internet access, a common architecture is:

```text
Private EC2
    │
    ▼
Private Route Table
    │
    ▼
NAT Gateway
    │
    ▼
Internet Gateway
    │
    ▼
Internet
```

---

# 🚦 10. NAT Gateway

NAT Gateway allows private subnet resources to initiate outbound connections without accepting unsolicited inbound connections through that NAT path.

Architecture:

```text
Private Subnet
      │
      ▼
NAT Gateway
      │
      ▼
Public Subnet
      │
      ▼
Internet Gateway
      │
      ▼
Internet
```

NAT Gateway requires appropriate public-subnet routing and an Elastic IP in the common architecture.

---

# 💰 11. NAT Gateway Cost

NAT Gateways incur AWS charges.

For learning:

```text
Use only when needed
Destroy after labs
Monitor costs
```

Production:

```text
Design NAT placement deliberately
Consider AZ resilience
Estimate data-processing costs
```

Do not create unnecessary NAT Gateways.

---

# 🧱 12. Network ACL

NACL = Network Access Control List.

It operates at subnet level.

Concept:

```text
Subnet
  │
  ▼
NACL
  │
  ▼
Resources
```

NACLs are stateless.

That means:

```text
Inbound rule
+
Outbound rule
```

must be considered independently.

---

# 🛡️ 13. Security Group

Security Group operates at the resource/ENI level and is stateful.

Example:

```text
Internet
   │
   ▼
Security Group
   │
   ▼
EC2
```

Security Groups commonly control:

```text
Inbound
Outbound
```

and return traffic is handled by the stateful behavior.

---

# 🆚 14. NACL vs Security Group

| Feature | NACL | Security Group |
|---|---|---|
| Scope | Subnet | ENI/resource |
| Stateful | ❌ | ✅ |
| Rule type | Allow + Deny | Allow |
| Return traffic | Explicit rules | Stateful |
| Typical use | Subnet boundary | Workload access |

Use both where appropriate.

---

# 📁 15. Project Folder Structure

```text
12-Terraform-AWS-VPC-Project/
│
├── README.md
│
├── terraform/
│   ├── versions.tf
│   ├── provider.tf
│   ├── variables.tf
│   ├── locals.tf
│   ├── vpc.tf
│   ├── subnets.tf
│   ├── route_tables.tf
│   ├── nat.tf
│   ├── nacl.tf
│   ├── security_groups.tf
│   ├── outputs.tf
│   ├── terraform.tfvars.example
│   └── .gitignore
│
├── modules/
│   └── vpc/
│       ├── main.tf
│       ├── variables.tf
│       ├── outputs.tf
│       └── versions.tf
│
└── examples/
    └── basic/
        └── README.md
```

---

# ⚙️ 16. Provider Configuration

`provider.tf`:

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

provider "aws" {
  region = var.aws_region
}
```

Use a provider version compatible with your project.

---

# 📋 17. Variables

`variables.tf`:

```hcl
variable "aws_region" {
  description = "AWS region"
  type        = string
  default     = "eu-north-1"
}

variable "project_name" {
  description = "Project name"
  type        = string
  default     = "vishwatech"
}

variable "environment" {
  description = "Environment"
  type        = string

  validation {
    condition = contains(
      ["dev", "stage", "prod"],
      var.environment
    )

    error_message = "Use dev, stage, or prod."
  }
}

variable "vpc_cidr" {
  description = "VPC CIDR"
  type        = string
  default     = "10.0.0.0/16"
}
```

---

# 🏷️ 18. Locals

`locals.tf`:

```hcl
locals {
  name_prefix = "${var.project_name}-${var.environment}"

  common_tags = {
    Project     = var.project_name
    Environment = var.environment
    ManagedBy   = "Terraform"
  }
}
```

---

# 🌐 19. VPC Resource

`vpc.tf`:

```hcl
resource "aws_vpc" "this" {
  cidr_block           = var.vpc_cidr
  enable_dns_support   = true
  enable_dns_hostnames = true

  tags = merge(
    local.common_tags,
    {
      Name = "${local.name_prefix}-vpc"
    }
  )
}
```

---

# 🚪 20. Internet Gateway

```hcl
resource "aws_internet_gateway" "this" {
  vpc_id = aws_vpc.this.id

  tags = merge(
    local.common_tags,
    {
      Name = "${local.name_prefix}-igw"
    }
  )
}
```

---

# 🏘️ 21. Public Subnets

```hcl
variable "availability_zones" {
  type = list(string)

  default = [
    "eu-north-1a",
    "eu-north-1b"
  ]
}
```

```hcl
resource "aws_subnet" "public" {
  for_each = {
    a = "10.0.1.0/24"
    b = "10.0.2.0/24"
  }

  vpc_id            = aws_vpc.this.id
  cidr_block        = each.value
  availability_zone = var.availability_zones[each.key == "a" ? 0 : 1]

  map_public_ip_on_launch = true

  tags = merge(
    local.common_tags,
    {
      Name = "${local.name_prefix}-public-${each.key}"
      Tier = "public"
    }
  )
}
```

For a larger design, use explicit subnet objects/maps instead of embedding positional AZ logic.

---

# 🔒 22. Private Subnets

```hcl
resource "aws_subnet" "private" {
  for_each = {
    a = "10.0.11.0/24"
    b = "10.0.12.0/24"
  }

  vpc_id            = aws_vpc.this.id
  cidr_block        = each.value
  availability_zone = var.availability_zones[each.key == "a" ? 0 : 1]

  tags = merge(
    local.common_tags,
    {
      Name = "${local.name_prefix}-private-${each.key}"
      Tier = "private"
    }
  )
}
```

---

# 🛣️ 23. Public Route Table

```hcl
resource "aws_route_table" "public" {
  vpc_id = aws_vpc.this.id

  route {
    cidr_block = "0.0.0.0/0"
    gateway_id = aws_internet_gateway.this.id
  }

  tags = merge(
    local.common_tags,
    {
      Name = "${local.name_prefix}-public-rt"
    }
  )
}
```

---

# 🔗 24. Public Route Associations

```hcl
resource "aws_route_table_association" "public" {
  for_each = aws_subnet.public

  subnet_id      = each.value.id
  route_table_id = aws_route_table.public.id
}
```

---

# 💳 25. Elastic IP for NAT

```hcl
resource "aws_eip" "nat" {
  domain = "vpc"

  tags = merge(
    local.common_tags,
    {
      Name = "${local.name_prefix}-nat-eip"
    }
  )
}
```

---

# 🚦 26. NAT Gateway

```hcl
resource "aws_nat_gateway" "this" {
  allocation_id = aws_eip.nat.id
  subnet_id     = aws_subnet.public["a"].id

  depends_on = [
    aws_internet_gateway.this
  ]

  tags = merge(
    local.common_tags,
    {
      Name = "${local.name_prefix}-nat"
    }
  )
}
```

For high availability, production architectures often use NAT Gateway per AZ or another deliberate egress strategy.

---

# 🔐 27. Private Route Table

```hcl
resource "aws_route_table" "private" {
  vpc_id = aws_vpc.this.id

  route {
    cidr_block     = "0.0.0.0/0"
    nat_gateway_id = aws_nat_gateway.this.id
  }

  tags = merge(
    local.common_tags,
    {
      Name = "${local.name_prefix}-private-rt"
    }
  )
}
```

---

# 🔗 28. Private Associations

```hcl
resource "aws_route_table_association" "private" {
  for_each = aws_subnet.private

  subnet_id      = each.value.id
  route_table_id = aws_route_table.private.id
}
```

---

# 🛡️ 29. Security Group

Example application Security Group:

```hcl
resource "aws_security_group" "app" {
  name        = "${local.name_prefix}-app-sg"
  description = "Application security group"
  vpc_id      = aws_vpc.this.id

  ingress {
    description = "HTTPS"
    from_port   = 443
    to_port     = 443
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }

  tags = local.common_tags
}
```

Production rules should be restricted to actual application requirements.

---

# 🧱 30. NACL Example

A basic custom NACL:

```hcl
resource "aws_network_acl" "private" {
  vpc_id = aws_vpc.this.id

  tags = merge(
    local.common_tags,
    {
      Name = "${local.name_prefix}-private-nacl"
    }
  )
}
```

Remember:

```text
NACL = Stateless
Security Group = Stateful
```

Do not copy generic allow-all NACL rules into production without a security design.

---

# 📤 31. Outputs

`outputs.tf`:

```hcl
output "vpc_id" {
  description = "VPC ID"
  value       = aws_vpc.this.id
}

output "public_subnet_ids" {
  description = "Public subnet IDs"
  value       = values(aws_subnet.public)[*].id
}

output "private_subnet_ids" {
  description = "Private subnet IDs"
  value       = values(aws_subnet.private)[*].id
}

output "nat_gateway_id" {
  description = "NAT Gateway ID"
  value       = aws_nat_gateway.this.id
}
```

---

# 🧪 32. Example Variables File

Never commit real secrets.

`terraform.tfvars.example`:

```hcl
aws_region  = "eu-north-1"
project_name = "vishwatech"
environment  = "dev"

vpc_cidr = "10.0.0.0/16"

availability_zones = [
  "eu-north-1a",
  "eu-north-1b"
]
```

Copy:

```bash
cp terraform.tfvars.example terraform.tfvars
```

Then customize locally.

---

# 🚫 33. `.gitignore`

```gitignore
.terraform/
*.tfstate
*.tfstate.*
*.tfvars
*.tfvars.json
crash.log
crash.*.log
```

Keep the example file tracked:

```text
terraform.tfvars.example
```

---

# 🚀 34. Terraform Workflow

Initialize:

```bash
terraform init
```

Format:

```bash
terraform fmt -recursive
```

Validate:

```bash
terraform validate
```

Plan:

```bash
terraform plan
```

Apply:

```bash
terraform apply
```

Destroy:

```bash
terraform destroy
```

---

# 🔍 35. Verify AWS Infrastructure

Check:

```text
VPC
Subnets
Route Tables
Internet Gateway
NAT Gateway
Elastic IP
Security Groups
NACLs
```

CLI examples:

```bash
aws ec2 describe-vpcs
```

```bash
aws ec2 describe-subnets
```

```bash
aws ec2 describe-route-tables
```

Use the AWS CLI identity that corresponds to the intended account/role.

---

# 🧪 36. Connectivity Testing

Do not test by exposing everything to the internet.

Better:

```text
Public test workload
        ↓
Private test workload
        ↓
Controlled Security Groups
```

Test:

```text
Public → Internet
Private → Internet through NAT
Private → Public only when explicitly allowed
```

---

# 🧠 37. Terraform Dependency Graph

Run:

```bash
terraform graph
```

Concept:

```text
VPC
 │
 ├── IGW
 │    │
 │    └── Public Route
 │
 ├── Public Subnets
 │
 └── Private Subnets
      │
      └── NAT Route
```

This helps students understand Terraform's dependency engine.

---

# 🔄 38. Module Version of the Project

Later refactor:

```text
root/
│
├── main.tf
│
└── modules/
    └── vpc/
        ├── main.tf
        ├── variables.tf
        ├── outputs.tf
        └── versions.tf
```

Root:

```hcl
module "vpc" {
  source = "./modules/vpc"

  name       = "vishwatech-dev"
  vpc_cidr   = "10.0.0.0/16"
  environment = "dev"
}
```

---

# 🏢 39. Production Architecture

```text
                         Internet
                            │
                            ▼
                         IGW
                            │
                 ┌──────────▼──────────┐
                 │       VPC            │
                 │    10.0.0.0/16       │
                 └──────────┬───────────┘
                            │
              ┌─────────────┴─────────────┐
              │                           │
           AZ-A                         AZ-B
              │                           │
       ┌──────┴──────┐             ┌──────┴──────┐
       │             │             │             │
     Public       Private        Public        Private
       │             │             │             │
       │             ▼             │             ▼
       │            NAT            │            NAT
       │             │             │             │
       └─────────────┴─────────────┴─────────────┘
                            │
                         Services
```

For highly available production, evaluate AZ-specific NAT architecture and egress requirements.

---

# 💰 40. Cost Considerations

Main cost considerations:

```text
NAT Gateway
Elastic IP behavior
Data processing
Load Balancers
VPC endpoints
Cross-AZ traffic
```

For labs:

```bash
terraform destroy
```

after completing the exercise.

---

# 🔐 41. Security Best Practices

```text
☑ Use private subnets for internal workloads
☑ Minimize public IP addresses
☑ Restrict Security Group sources
☑ Avoid 0.0.0.0/0 unless required
☑ Use least privilege
☑ Use VPC endpoints where appropriate
☑ Protect state
☑ Encrypt sensitive services
☑ Enable logging/monitoring
☑ Separate environments
```

---

# 🚨 42. Common Mistakes

### Mistake 1 — Overlapping CIDRs

Bad:

```text
10.0.1.0/24
10.0.1.0/24
```

Fix:

```text
10.0.1.0/24
10.0.2.0/24
```

---

### Mistake 2 — Private Subnet Has IGW Route

Private subnet should not normally have:

```text
0.0.0.0/0 → IGW
```

Use NAT or another approved egress architecture when internet access is required.

---

### Mistake 3 — NAT in Private Subnet

NAT Gateway should be placed in a public subnet in the common design.

---

### Mistake 4 — Open SSH

Avoid:

```text
0.0.0.0/0
TCP 22
```

Prefer:

```text
Bastion/SSM/VPN/controlled source
```

according to your architecture.

---

# 🔍 43. Troubleshooting

## Private Instance Has No Internet

Check:

```text
Private route table
NAT Gateway
NAT subnet
Public route
Internet Gateway
Elastic IP
Security Group
NACL
```

---

## Public Instance Has No Internet

Check:

```text
Public route table
0.0.0.0/0 → IGW
Public IP
Security Group
NACL
```

---

## NAT Gateway Not Working

Check:

```text
NAT Gateway state
EIP
Public subnet
Public route
IGW
Private route
```

---

## Terraform Wants to Recreate Resources

Check:

```bash
terraform plan
```

Then inspect:

```text
CIDR changes
AZ changes
Resource addresses
Provider changes
State
Lifecycle
```

---

# 🧪 44. Hands-On Labs

## Lab 01 — Create VPC

Create:

```text
10.0.0.0/16
```

## Lab 02 — Internet Gateway

Attach IGW.

## Lab 03 — Public Subnet

Create:

```text
10.0.1.0/24
```

## Lab 04 — Public Route

Create:

```text
0.0.0.0/0 → IGW
```

## Lab 05 — Private Subnet

Create:

```text
10.0.11.0/24
```

## Lab 06 — NAT Gateway

Create NAT architecture.

## Lab 07 — Private Route

Route private subnet through NAT.

## Lab 08 — Multi-AZ

Create two AZs.

## Lab 09 — Security Group

Create application SG.

## Lab 10 — NACL

Create custom NACL.

## Lab 11 — Outputs

Expose all important IDs.

## Lab 12 — Variables

Parameterize CIDRs.

## Lab 13 — Locals

Create common tags.

## Lab 14 — `for_each`

Generate subnets dynamically.

## Lab 15 — CIDR Functions

Generate subnets using:

```hcl
cidrsubnet()
```

## Lab 16 — Add EC2

Deploy test EC2 into public subnet.

## Lab 17 — Private EC2

Deploy test EC2 into private subnet.

## Lab 18 — NAT Connectivity

Verify controlled outbound connectivity.

## Lab 19 — VPC Flow Logs

Add logging according to your requirements.

## Lab 20 — VPC Endpoints

Add an endpoint for a supported AWS service.

## Lab 21 — Module Refactor

Move VPC into a module.

## Lab 22 — Remote State

Move state to S3.

## Lab 23 — Multi-Environment

Build:

```text
dev
stage
prod
```

## Lab 24 — Multi-Region

Create a second-region network.

## Lab 25 — Provider Alias

Use aliased AWS provider.

## Lab 26 — State Refactor

Use `moved` blocks.

## Lab 27 — Security Hardening

Remove unnecessary public access.

## Lab 28 — Cost Review

Identify NAT/data-processing costs.

## Lab 29 — Destroy and Recreate

Practice clean lifecycle.

## Lab 30 — Enterprise Challenge

Build:

```text
VPC
├── 2 AZ
├── Public Subnets
├── Private Subnets
├── IGW
├── NAT
├── Route Tables
├── SG
├── NACL
├── Flow Logs
└── Outputs
```

using:

```text
Terraform
Modules
Remote State
GitHub Actions
Security Controls
```

---

# 🏆 45. Production Checklist

```text
NETWORK
☑ Non-overlapping CIDRs
☑ Multi-AZ design
☑ Public/private separation
☑ Route tables
☑ IGW
☑ NAT strategy

SECURITY
☑ Restricted SGs
☑ NACL strategy
☑ No unnecessary public IPs
☑ Controlled administrative access
☑ Flow logs where required

TERRAFORM
☑ Variables
☑ Outputs
☑ Modules
☑ Provider version
☑ Remote state
☑ Locking
☑ CI/CD

OPERATIONS
☑ Cost monitoring
☑ Backup/recovery
☑ Documentation
☑ Destroy lab resources
☑ Change review
```

---

# 🎓 46. Interview Questions

### Beginner

1. What is a VPC?
2. What is CIDR?
3. What is a subnet?
4. Public vs private subnet?
5. What is an Internet Gateway?
6. What is a route table?
7. What is a NAT Gateway?
8. What is a Security Group?
9. What is a NACL?
10. What is an Availability Zone?

### Intermediate

11. How does private subnet internet access work?
12. Why does NAT Gateway need a public subnet?
13. NACL vs Security Group?
14. How do route table associations work?
15. How do you design a multi-AZ VPC?
16. How do you calculate subnet CIDRs?
17. How would you use `for_each` for subnets?
18. How would you modularize a VPC?
19. How do you secure Terraform state?
20. How do you troubleshoot NAT connectivity?

### Advanced

21. Design a production VPC for a three-tier application.
22. How would you make NAT highly available?
23. When would you use VPC endpoints?
24. How would you design network isolation between workloads?
25. How would you manage multi-account VPC deployments?
26. How would you connect VPCs?
27. How would you design centralized egress?
28. How would you integrate VPC provisioning with GitHub Actions?
29. How would you minimize networking costs?
30. Design an enterprise AWS network using Terraform modules.

---

# ⚡ 47. Cheat Sheet

```text
VPC
10.0.0.0/16

PUBLIC
10.0.1.0/24
10.0.2.0/24

PRIVATE
10.0.11.0/24
10.0.12.0/24

DB
10.0.21.0/24
10.0.22.0/24

PUBLIC ROUTE
0.0.0.0/0 → IGW

PRIVATE ROUTE
0.0.0.0/0 → NAT

Terraform
terraform init
terraform fmt
terraform validate
terraform plan
terraform apply
terraform destroy

Diagnostics
terraform graph
terraform state list
terraform state show ADDRESS
```

---

# 🗺️ 48. Terraform Roadmap

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
12 AWS VPC Project              🟢 ← YOU ARE HERE
13 AWS EC2 Project
14 AWS S3 Project
15 IAM & Security
16 HCP Terraform
17 Terraform + GitHub Actions
18 Terraform Security & Policy
19 Multi-Cloud Terraform
20 Enterprise Capstone
```

---

<div align="center">

# 🌐 BUILD THE NETWORK BEFORE YOU BUILD THE APPLICATION

### VishwaTech Labs

**Terraform • AWS • Azure • GCP • Docker • GitHub Actions • Kubernetes • Ansible • DevSecOps**

### By Vishwanath Gowda H

⭐ Infrastructure as Code • Cloud Networking • Automation

</div>
