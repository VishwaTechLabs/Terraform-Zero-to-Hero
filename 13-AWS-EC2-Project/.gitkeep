<div align="center">

# 🖥️ Terraform AWS EC2 — Complete Real-World Project

### 🚀 Compute • IAM • Security Groups • EBS • User Data • Metadata • Monitoring | VishwaTech Labs

[![Terraform](https://img.shields.io/badge/Terraform-844FBA?logo=terraform&logoColor=white)](https://developer.hashicorp.com/terraform/docs)
[![AWS EC2](https://img.shields.io/badge/AWS-EC2-FF9900?logo=amazonaws&logoColor=white)](https://aws.amazon.com/ec2/)
[![IAM](https://img.shields.io/badge/AWS-IAM-red?logo=amazonaws&logoColor=white)](https://aws.amazon.com/iam/)
[![Security](https://img.shields.io/badge/Security-Hardened-green)](#-security-best-practices)
[![Labs](https://img.shields.io/badge/Labs-30+-purple)](#-hands-on-labs)

**Build production-style AWS EC2 infrastructure using Terraform — from AMI selection and IAM roles to EBS, user data, monitoring, metadata security, networking and reusable modules.**

[📘 AWS EC2](https://docs.aws.amazon.com/ec2/) •
[📘 Terraform AWS Provider](https://registry.terraform.io/providers/hashicorp/aws/latest/docs) •
[📘 Terraform](https://developer.hashicorp.com/terraform/docs)

</div>

---

# 🎯 Project Goal

Build a secure EC2 deployment using Terraform:

```text
                    AWS
                     │
                     ▼
                  VPC
                     │
          ┌──────────┴──────────┐
          ▼                     ▼
     Public Subnet         Private Subnet
          │                     │
          ▼                     ▼
      EC2 Web               EC2 App
          │                     │
          └──────────┬──────────┘
                     ▼
                Security Group
                     │
                IAM Instance Role
                     │
              CloudWatch / SSM
```

The project teaches the difference between:

```text
Compute
Network
Identity
Storage
Security
Observability
```

---

# 🧠 1. What Is Amazon EC2?

Amazon EC2 provides resizable compute capacity in AWS.

Think:

```text
Physical Server
      ↓
Virtual Machine
      ↓
EC2 Instance
```

An EC2 instance can run:

```text
Linux
Windows
Applications
Web servers
Containers
Agents
Automation workloads
```

---

# 🧩 2. EC2 Components

A production EC2 deployment commonly involves:

```text
AMI
Instance Type
Subnet
Security Group
IAM Instance Profile
Key Pair / SSM
EBS Volumes
Network Interface
User Data
Metadata Options
Monitoring
Tags
```

Architecture:

```text
EC2
│
├── AMI
├── CPU / Memory
├── ENI
├── EBS
├── IAM Role
├── Security Group
├── User Data
└── Metadata Service
```

---

# 💿 3. AMI

AMI = Amazon Machine Image.

It provides the base image used to launch an instance.

Examples:

```text
Amazon Linux
Ubuntu
Windows Server
Custom Golden Image
```

Terraform:

```hcl
resource "aws_instance" "web" {
  ami           = var.ami_id
  instance_type = var.instance_type
}
```

Avoid hardcoding AMI IDs across environments without a deliberate image-management strategy.

---

# 🔍 4. AMI Selection

AMI IDs are region-specific.

Therefore:

```text
us-east-1 AMI
      ≠
eu-north-1 AMI
```

Possible approaches:

```text
Explicit AMI variable
AWS data source
SSM public parameters
Golden image pipeline
```

Example data source pattern:

```hcl
data "aws_ami" "amazon_linux" {
  most_recent = true

  owners = ["amazon"]

  filter {
    name   = "name"
    values = ["al2023-ami-*-x86_64"]
  }

  filter {
    name   = "state"
    values = ["available"]
  }
}
```

Always verify the image family and architecture you intend to deploy.

---

# ⚙️ 5. Instance Type

Instance type controls resources such as:

```text
vCPU
Memory
Network performance
Storage characteristics
Architecture
```

Examples:

```text
t3.micro
t3.small
t3.medium
m7i.large
c7i.large
r7i.large
```

Choose based on:

```text
Workload
CPU
Memory
Network
Cost
Architecture
```

Do not choose instance size purely by habit.

---

# 🔐 6. IAM Role for EC2

Avoid putting AWS access keys inside the EC2 machine.

Bad:

```text
EC2
 └── access_key
 └── secret_key
```

Better:

```text
EC2
  ↓
IAM Instance Role
  ↓
AWS APIs
```

Terraform:

```hcl
resource "aws_iam_role" "ec2" {
  name = "${var.name}-ec2-role"

  assume_role_policy = jsonencode({
    Version = "2012-10-17"
    Statement = [{
      Effect = "Allow"
      Principal = {
        Service = "ec2.amazonaws.com"
      }
      Action = "sts:AssumeRole"
    }]
  })
}
```

---

# 🪪 7. Instance Profile

EC2 receives an IAM role through an instance profile.

```hcl
resource "aws_iam_instance_profile" "ec2" {
  name = "${var.name}-profile"
  role = aws_iam_role.ec2.name
}
```

Attach:

```hcl
resource "aws_instance" "web" {
  iam_instance_profile = aws_iam_instance_profile.ec2.name

  # ...
}
```

Mental model:

```text
IAM Role
   ↓
Instance Profile
   ↓
EC2
```

---

# 🔑 8. SSH Key Pair

Traditional Linux access may use an EC2 key pair:

```hcl
resource "aws_key_pair" "this" {
  key_name   = var.key_name
  public_key = file(var.public_key_path)
}
```

Then:

```hcl
resource "aws_instance" "web" {
  key_name = aws_key_pair.this.key_name
}
```

Never commit:

```text
Private key
.pem
.ppk
SSH credentials
```

to Git.

---

# 🛡️ 9. Prefer SSM for Administration

For many AWS environments, AWS Systems Manager Session Manager can reduce the need for inbound SSH.

Architecture:

```text
Engineer
   ↓
AWS Console / CLI
   ↓
SSM
   ↓
EC2
```

Benefits:

```text
No public SSH requirement
Centralized access
IAM-based control
Audit integration
```

The instance needs the appropriate SSM agent and IAM permissions/network access.

---

# 🌐 10. Network Placement

Public EC2:

```text
Internet
   ↓
IGW
   ↓
Public Subnet
   ↓
EC2
```

Private EC2:

```text
Internet
   ↓
NAT
   ↓
Private Subnet
   ↓
EC2
```

For production application servers, private placement is often preferred unless direct public exposure is actually required.

---

# 🛡️ 11. Security Group

Example:

```hcl
resource "aws_security_group" "web" {
  name        = "${var.name}-web-sg"
  description = "Web server security group"
  vpc_id      = var.vpc_id

  ingress {
    description = "HTTPS"
    from_port   = 443
    to_port     = 443
    protocol    = "tcp"
    cidr_blocks = var.allowed_https_cidrs
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}
```

Production rule:

```text
Only open required ports
Only trusted sources
```

---

# 🚫 12. Avoid Open SSH

Avoid:

```text
TCP 22
0.0.0.0/0
```

Prefer:

```text
SSM
VPN
Bastion
Trusted corporate CIDR
```

according to your organization's architecture.

---

# 💾 13. EBS

EBS = Elastic Block Store.

EC2 uses EBS volumes for persistent block storage.

Common volume types include:

```text
gp3
io2
```

Choose based on:

```text
Capacity
IOPS
Throughput
Latency
Durability
Cost
```

---

# 💽 14. Root EBS Volume

Example:

```hcl
resource "aws_instance" "web" {
  ami           = var.ami_id
  instance_type = var.instance_type

  root_block_device {
    volume_size = 20
    volume_type = "gp3"
    encrypted   = true
  }
}
```

Encryption should generally be enabled for production workloads where required.

---

# 🔐 15. Additional EBS Volume

```hcl
resource "aws_ebs_volume" "data" {
  availability_zone = var.availability_zone
  size              = 50
  type              = "gp3"
  encrypted         = true

  tags = {
    Name = "${var.name}-data"
  }
}
```

Attach:

```hcl
resource "aws_volume_attachment" "data" {
  device_name = "/dev/sdf"
  volume_id   = aws_ebs_volume.data.id
  instance_id = aws_instance.web.id
}
```

---

# ⚠️ 16. EBS and Availability Zones

EBS volumes are tied to an Availability Zone.

Therefore:

```text
EC2 AZ-A
   ↓
EBS AZ-A
```

A volume in:

```text
AZ-B
```

cannot simply be attached directly to an instance in:

```text
AZ-A
```

without an appropriate copy/snapshot/migration strategy.

---

# 📜 17. User Data

User data can bootstrap an EC2 instance.

Example:

```hcl
resource "aws_instance" "web" {
  ami           = var.ami_id
  instance_type = var.instance_type

  user_data = <<-EOF
              #!/bin/bash
              dnf update -y
              dnf install -y nginx
              systemctl enable nginx
              systemctl start nginx
              EOF
}
```

Use user data for:

```text
Initial bootstrap
Packages
Configuration
Agents
Startup tasks
```

For complex immutable infrastructure, consider baking software into AMIs instead of turning user data into a giant configuration script.

---

# 🧠 18. `user_data` vs `user_data_replace_on_change`

For workloads where a change in user data should force replacement, Terraform/AWS provider behavior can be controlled with the appropriate argument supported by the resource/provider version.

Understand:

```text
Bootstrap change
       ↓
Restart?
Replace?
Reconfigure?
```

Do not assume every user-data change automatically rebuilds the instance.

---

# 🔐 19. EC2 Instance Metadata

EC2 Instance Metadata Service (IMDS) provides instance information.

For hardened environments, prefer IMDSv2.

Example:

```hcl
metadata_options {
  http_endpoint               = "enabled"
  http_tokens                 = "required"
  http_put_response_hop_limit = 1
}
```

Important:

```text
IMDSv2
+
Least privilege IAM
```

is a strong baseline for EC2 security.

---

# 🛡️ 20. Why IMDSv2 Matters

Concept:

```text
Application
    ↓
Metadata Service
    ↓
Temporary AWS Credentials
```

Requiring tokens reduces exposure to certain credential-access paths.

Security principle:

```text
Protect metadata
Protect credentials
Protect IAM role
```

---

# 📊 21. Monitoring

Terraform can enable detailed monitoring:

```hcl
resource "aws_instance" "web" {
  monitoring = true
}
```

Also consider:

```text
CloudWatch Agent
CloudWatch alarms
SSM
CloudTrail
VPC Flow Logs
Application logs
```

Monitoring design should match workload requirements and cost constraints.

---

# 🏷️ 22. Tags

Standardize tags:

```hcl
locals {
  common_tags = {
    Project     = var.project_name
    Environment = var.environment
    ManagedBy   = "Terraform"
    Owner       = var.owner
  }
}
```

Apply:

```hcl
tags = local.common_tags
```

Useful tags:

```text
Project
Environment
Owner
CostCenter
Application
ManagedBy
Backup
DataClassification
```

---

# 📁 23. Project Folder Structure

```text
13-Terraform-AWS-EC2-Project/
│
├── README.md
│
├── terraform/
│   ├── versions.tf
│   ├── provider.tf
│   ├── variables.tf
│   ├── locals.tf
│   ├── data.tf
│   ├── iam.tf
│   ├── security_groups.tf
│   ├── ec2.tf
│   ├── ebs.tf
│   ├── user_data.sh
│   ├── outputs.tf
│   ├── terraform.tfvars.example
│   └── .gitignore
│
├── modules/
│   └── ec2/
│       ├── main.tf
│       ├── variables.tf
│       ├── outputs.tf
│       └── versions.tf
│
└── examples/
    ├── public-web/
    └── private-app/
```

---

# ⚙️ 24. Provider

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

---

# 📋 25. Variables

```hcl
variable "aws_region" {
  type    = string
  default = "eu-north-1"
}

variable "project_name" {
  type    = string
  default = "vishwatech"
}

variable "environment" {
  type = string

  validation {
    condition = contains(
      ["dev", "stage", "prod"],
      var.environment
    )

    error_message = "Use dev, stage, or prod."
  }
}

variable "ami_id" {
  description = "AMI ID for the EC2 instance"
  type        = string
}

variable "instance_type" {
  description = "EC2 instance type"
  type        = string
  default     = "t3.micro"
}

variable "subnet_id" {
  description = "Subnet for EC2"
  type        = string
}

variable "vpc_id" {
  description = "VPC ID"
  type        = string
}
```

---

# 🧮 26. Locals

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

# 🖥️ 27. EC2 Resource

```hcl
resource "aws_instance" "this" {
  ami                         = var.ami_id
  instance_type               = var.instance_type
  subnet_id                   = var.subnet_id
  vpc_security_group_ids     = [aws_security_group.web.id]
  iam_instance_profile       = aws_iam_instance_profile.ec2.name
  monitoring                  = true
  user_data                   = file("${path.module}/user_data.sh")

  metadata_options {
    http_endpoint               = "enabled"
    http_tokens                 = "required"
    http_put_response_hop_limit = 1
  }

  root_block_device {
    volume_type = "gp3"
    volume_size = 20
    encrypted   = true
  }

  tags = merge(
    local.common_tags,
    {
      Name = "${local.name_prefix}-ec2"
    }
  )
}
```

---

# ⚠️ 28. Public IP Decision

Do not automatically enable:

```hcl
associate_public_ip_address = true
```

Ask:

```text
Does this server actually need public internet reachability?
```

Better architecture for many applications:

```text
Internet
   ↓
ALB
   ↓
Private EC2
```

rather than:

```text
Internet
   ↓
Public EC2
```

---

# 🌐 29. Web Architecture

Production-style:

```text
                 Internet
                    │
                    ▼
              Application
              Load Balancer
                    │
          ┌─────────┴─────────┐
          ▼                   ▼
      EC2 AZ-A             EC2 AZ-B
      Private              Private
          │                   │
          └─────────┬─────────┘
                    ▼
                 Database
```

This is generally more scalable and secure than exposing individual application instances.

---

# 🪪 30. IAM Policy

Do not attach broad permissions unnecessarily.

Bad:

```text
AdministratorAccess
```

Better:

```text
Only permissions required
for the workload
```

Example use cases:

```text
S3 read-only
CloudWatch metrics
SSM
Secrets retrieval
```

Use AWS-managed or custom policies appropriate to the application.

---

# 🔐 31. SSM IAM Example

A common starting point for Systems Manager-managed EC2 instances is the AWS-managed policy:

```text
AmazonSSMManagedInstanceCore
```

Example:

```hcl
resource "aws_iam_role_policy_attachment" "ssm" {
  role       = aws_iam_role.ec2.name
  policy_arn = "arn:aws:iam::aws:policy/AmazonSSMManagedInstanceCore"
}
```

Review permissions and organizational policy before production use.

---

# 🧰 32. User Data Script

`user_data.sh`:

```bash
#!/bin/bash

set -e

dnf update -y
dnf install -y nginx

systemctl enable nginx
systemctl start nginx

cat > /usr/share/nginx/html/index.html <<'HTML'
<!DOCTYPE html>
<html>
<head>
  <title>VishwaTech Labs</title>
</head>
<body>
  <h1>🚀 Terraform EC2 Lab</h1>
  <p>Deployed with Terraform.</p>
</body>
</html>
HTML
```

The package manager differs between Linux distributions. This example assumes an Amazon Linux family image using `dnf`.

---

# 🧪 33. Validate Bootstrap

After launch:

```text
EC2
 ↓
User Data
 ↓
Nginx
 ↓
Web Page
```

Check:

```bash
systemctl status nginx
```

Logs:

```bash
sudo cat /var/log/cloud-init-output.log
```

---

# 📤 34. Outputs

```hcl
output "instance_id" {
  description = "EC2 instance ID"
  value       = aws_instance.this.id
}

output "private_ip" {
  description = "Private IP"
  value       = aws_instance.this.private_ip
}

output "public_ip" {
  description = "Public IP when assigned"
  value       = aws_instance.this.public_ip
}

output "instance_arn" {
  description = "EC2 ARN"
  value       = aws_instance.this.arn
}
```

---

# 🧩 35. EC2 Module

Root:

```hcl
module "web" {
  source = "./modules/ec2"

  name          = "web"
  ami_id        = var.ami_id
  instance_type = "t3.micro"
  subnet_id     = var.subnet_id
  vpc_id        = var.vpc_id
}
```

Module:

```text
modules/ec2/
├── main.tf
├── variables.tf
├── outputs.tf
└── versions.tf
```

---

# 🔢 36. Multiple EC2 Instances

Use `for_each` for identity-based instances:

```hcl
variable "instances" {
  type = map(object({
    instance_type = string
    subnet_id     = string
  }))
}
```

Example:

```hcl
instances = {
  web = {
    instance_type = "t3.small"
    subnet_id     = "subnet-aaa"
  }

  api = {
    instance_type = "t3.medium"
    subnet_id     = "subnet-bbb"
  }
}
```

Then:

```hcl
resource "aws_instance" "this" {
  for_each = var.instances

  ami           = var.ami_id
  instance_type = each.value.instance_type
  subnet_id     = each.value.subnet_id
}
```

---

# 🔄 37. Auto Scaling

For production workloads requiring horizontal scaling, avoid treating individual EC2 resources as the scaling mechanism.

Preferred architecture:

```text
Launch Template
       ↓
Auto Scaling Group
       ↓
EC2 Instances
       ↓
Load Balancer
```

Terraform can manage the entire stack.

---

# 🏗️ 38. Launch Template

Concept:

```hcl
resource "aws_launch_template" "app" {
  name_prefix   = "${var.name}-"
  image_id      = var.ami_id
  instance_type = var.instance_type

  metadata_options {
    http_tokens = "required"
  }

  tag_specifications {
    resource_type = "instance"

    tags = {
      Name = var.name
    }
  }
}
```

Then connect the launch template to an Auto Scaling Group.

---

# 📈 39. Auto Scaling Architecture

```text
                  ALB
                   │
          ┌────────┴────────┐
          ▼                 ▼
       EC2 AZ-A          EC2 AZ-B
          ▲                 ▲
          └──── Auto Scaling┘
                    │
             Launch Template
```

Benefits:

```text
High availability
Horizontal scaling
Self-healing
Capacity management
```

---

# 📊 40. CloudWatch Alarm Concept

Example metrics:

```text
CPUUtilization
StatusCheckFailed
NetworkIn
NetworkOut
```

A production scaling architecture may use CloudWatch alarms with an Auto Scaling Group.

---

# 🧠 41. EC2 vs Container

Use EC2 when you need:

```text
OS-level control
Custom agents
Legacy workloads
Specialized system configuration
```

Use containers when you need:

```text
Portable application packaging
Fast deployment
Immutable application artifacts
Orchestration
```

Later in this course:

```text
Docker
   ↓
Kubernetes
```

will build on these concepts.

---

# 🔐 42. Security Best Practices

```text
☑ Prefer private subnets
☑ Use SSM where appropriate
☑ Require IMDSv2
☑ Encrypt EBS
☑ Least-privilege IAM
☑ Restrict Security Groups
☑ Patch operating systems
☑ Use hardened/golden AMIs
☑ Enable monitoring
☑ Avoid long-lived credentials
☑ Tag resources
☑ Protect Terraform state
```

---

# 💰 43. Cost Optimization

Consider:

```text
Instance right-sizing
Savings Plans
Reserved Instances where appropriate
Spot for interruptible workloads
Auto Scaling
EBS sizing
Monitoring costs
Data transfer
```

For labs:

```bash
terraform destroy
```

when the resources are no longer required.

---

# 🔍 44. Troubleshooting

## EC2 Won't Launch

Check:

```text
AMI
Instance type
Subnet
Capacity
IAM
Security Group
Quota
```

---

## User Data Failed

Check:

```bash
sudo cat /var/log/cloud-init-output.log
```

Also inspect:

```bash
cloud-init status
```

---

## Cannot Connect

Check:

```text
Instance state
Route table
Security Group
NACL
Public/private placement
SSM status
Network connectivity
```

---

## SSM Not Working

Check:

```text
SSM Agent
IAM role
AmazonSSMManagedInstanceCore
Network access
Region
Instance registration
```

---

## EC2 Has No Internet

Private instance:

```text
Private route
NAT Gateway
NAT route
IGW
```

Public instance:

```text
Public route
IGW
Public IP
```

---

# 🧪 45. Hands-On Labs

## Lab 01 — Launch EC2

Create a basic instance.

## Lab 02 — AMI Data Source

Find a current AMI dynamically.

## Lab 03 — Variables

Parameterize instance type.

## Lab 04 — Security Group

Allow controlled HTTP/HTTPS.

## Lab 05 — SSH Security

Restrict SSH to a trusted CIDR.

## Lab 06 — SSM

Connect without inbound SSH.

## Lab 07 — IAM Role

Attach an EC2 instance role.

## Lab 08 — EBS Encryption

Create encrypted storage.

## Lab 09 — Additional EBS

Attach a data volume.

## Lab 10 — User Data

Install Nginx.

## Lab 11 — IMDSv2

Require tokens.

## Lab 12 — Tags

Implement common tags.

## Lab 13 — Private EC2

Deploy into a private subnet.

## Lab 14 — NAT Connectivity

Test controlled outbound access.

## Lab 15 — Public EC2

Deploy a disposable web server.

## Lab 16 — CloudWatch

Enable detailed monitoring.

## Lab 17 — SSM

Manage instance through Systems Manager.

## Lab 18 — Multiple Instances

Use `for_each`.

## Lab 19 — EC2 Module

Build reusable module.

## Lab 20 — Launch Template

Build immutable compute configuration.

## Lab 21 — Auto Scaling

Create an Auto Scaling Group.

## Lab 22 — ALB Integration

Put instances behind an Application Load Balancer.

## Lab 23 — Multi-AZ

Deploy across two AZs.

## Lab 24 — Golden AMI

Build a standardized image workflow concept.

## Lab 25 — IAM Least Privilege

Reduce instance role permissions.

## Lab 26 — Security Hardening

Implement IMDSv2 and private placement.

## Lab 27 — Monitoring

Create operational alarms.

## Lab 28 — Remote State

Store state remotely.

## Lab 29 — GitHub Actions

Deploy EC2 through CI/CD.

## Lab 30 — Enterprise Challenge

Build:

```text
ALB
 │
 ├── EC2 AZ-A
 └── EC2 AZ-B
       │
       ├── IAM Role
       ├── SSM
       ├── EBS
       ├── IMDSv2
       └── CloudWatch
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

# 🏆 46. Production Checklist

```text
COMPUTE
☑ Correct AMI
☑ Right-sized instance
☑ Multi-AZ where required
☑ Launch template for scalable workloads

SECURITY
☑ IMDSv2
☑ Least privilege IAM
☑ Encrypted EBS
☑ Restricted Security Groups
☑ Private subnet where appropriate
☑ SSM/VPN instead of open SSH where appropriate

OPERATIONS
☑ Monitoring
☑ Logs
☑ Patching
☑ Tags
☑ Backup strategy
☑ Cost monitoring

TERRAFORM
☑ Variables
☑ Outputs
☑ Modules
☑ Remote state
☑ Locking
☑ CI/CD
```

---

# 🎓 47. Interview Questions

### Beginner

1. What is EC2?
2. What is an AMI?
3. What is an instance type?
4. What is EBS?
5. What is an ENI?
6. What is a Security Group?
7. What is an IAM role?
8. What is an instance profile?
9. What is user data?
10. What is IMDS?

### Intermediate

11. Public vs private EC2?
12. Why use SSM instead of SSH?
13. How does EC2 access S3 securely?
14. How do you encrypt EBS?
15. How does user data work?
16. What is IMDSv2?
17. How do you deploy multiple EC2 instances?
18. How do you create reusable EC2 modules?
19. How do you troubleshoot user data?
20. How do you monitor EC2?

### Advanced

21. Design a highly available EC2 architecture.
22. Why use Launch Templates?
23. EC2 vs Auto Scaling Group?
24. How would you secure EC2 in a private subnet?
25. How would you eliminate long-lived AWS keys?
26. How would you build a golden AMI pipeline?
27. How would you design EC2 across multiple AZs?
28. How would you integrate ALB + ASG + Terraform?
29. How would you minimize EC2 cost?
30. Design an enterprise Terraform EC2 platform.

---

# ⚡ 48. Cheat Sheet

```bash
terraform init
terraform fmt
terraform validate
terraform plan
terraform apply
terraform destroy
```

AWS identity:

```bash
aws sts get-caller-identity
```

SSM:

```text
EC2
 ↓
IAM Role
 ↓
SSM
 ↓
Engineer
```

Secure metadata:

```hcl
metadata_options {
  http_tokens = "required"
}
```

Encrypted EBS:

```hcl
root_block_device {
  volume_type = "gp3"
  encrypted   = true
}
```

Module:

```hcl
module "ec2" {
  source = "./modules/ec2"
}
```

---

# 🗺️ 49. Terraform Roadmap

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
13 AWS EC2 Project              🟢 ← YOU ARE HERE
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

# 🖥️ BUILD COMPUTE THE RIGHT WAY

### VishwaTech Labs

**Terraform • AWS • Azure • GCP • Docker • GitHub Actions • Kubernetes • Ansible • DevSecOps**

### By Vishwanath Gowda H

⭐ Infrastructure as Code • Cloud Compute • Secure Automation

</div>
