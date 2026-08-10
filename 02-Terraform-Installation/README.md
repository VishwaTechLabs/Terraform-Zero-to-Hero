<div align="center">

# ⚙️ Terraform Installation & Setup — Zero-to-Hero

### 🏗️ Windows • Linux • macOS • VS Code • AWS CLI • First Terraform Project | VishwaTech Labs

[![Terraform](https://img.shields.io/badge/Terraform-844FBA?logo=terraform&logoColor=white)](https://developer.hashicorp.com/terraform/install)
[![Windows](https://img.shields.io/badge/Windows-0078D4?logo=windows&logoColor=white)](https://developer.hashicorp.com/terraform/install)
[![Linux](https://img.shields.io/badge/Linux-FCC624?logo=linux&logoColor=black)](https://developer.hashicorp.com/terraform/install)
[![macOS](https://img.shields.io/badge/macOS-000000?logo=apple&logoColor=white)](https://developer.hashicorp.com/terraform/install)
[![AWS CLI](https://img.shields.io/badge/AWS%20CLI-232F3E?logo=amazonaws&logoColor=white)](https://docs.aws.amazon.com/cli/)

**Install Terraform correctly, configure your development environment, authenticate to AWS safely, and run your first Terraform project.**

[📘 Terraform Install](https://developer.hashicorp.com/terraform/install) •
[☁️ AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html) •
[🧩 VS Code](https://marketplace.visualstudio.com/items?itemName=HashiCorp.terraform)

</div>

---

# 🎯 What You Will Learn

By the end of this module you will be able to:

- Install Terraform on Windows
- Install Terraform on Linux
- Install Terraform on macOS
- Configure the system `PATH`
- Verify Terraform installation
- Install VS Code
- Install the official Terraform extension
- Install AWS CLI
- Configure AWS authentication safely
- Verify AWS identity
- Create your first Terraform project
- Install the AWS provider
- Run `terraform init`
- Run `terraform fmt`
- Run `terraform validate`
- Run `terraform plan`
- Run `terraform apply`
- Run `terraform destroy`
- Troubleshoot common installation problems

---

# 🧠 1. Terraform Setup Architecture

Your local environment will look like:

```text
                    YOUR COMPUTER
                         │
              ┌──────────┴──────────┐
              ▼                     ▼
          VS Code                Terminal
              │                     │
              └──────────┬──────────┘
                         ▼
                     Terraform
                         │
                         ▼
                   AWS Provider
                         │
                         ▼
                    AWS APIs
                         │
                         ▼
                    AWS Cloud
```

For local AWS authentication:

```text
Terraform
    │
    ▼
AWS SDK / Credential Chain
    │
    ▼
AWS CLI Profile / SSO / Environment / Role
    │
    ▼
AWS
```

---

# 📋 2. Prerequisites

Recommended knowledge:

```text
☑ Basic Linux/Windows commands
☑ Basic networking
☑ Basic cloud concepts
☑ Basic AWS knowledge
☑ Git basics
☑ Command-line basics
```

You do **not** need to be an expert programmer to start Terraform.

---

# 🪟 3. Windows Installation

## Option A — Official Terraform Download

Use the official HashiCorp installation instructions:

[Terraform Installation](https://developer.hashicorp.com/terraform/install)

Download the appropriate Windows package.

Terraform is distributed as a binary.

After extracting it, you should have:

```text
terraform.exe
```

---

# 📁 4. Terraform Installation Directory

A common approach is:

```text
C:\Terraform\
```

Place:

```text
terraform.exe
```

inside that directory.

Example:

```text
C:\
└── Terraform\
    └── terraform.exe
```

---

# 🛣️ 5. Configure Windows PATH

The `PATH` environment variable tells Windows where to find executable programs.

Add:

```text
C:\Terraform
```

to your user or system `PATH`.

Concept:

```text
Terminal
   │
   ▼
terraform
   │
   ▼
Windows PATH
   │
   ▼
C:\Terraform\terraform.exe
```

---

# 🔍 6. Verify Windows Installation

Open:

```text
PowerShell
```

or:

```text
Command Prompt
```

Run:

```powershell
terraform version
```

You should see Terraform version information.

Also:

```powershell
where.exe terraform
```

This shows which executable Windows is using.

---

# ⚠️ 7. `terraform` Not Recognized

If you see an error such as:

```text
terraform : The term 'terraform' is not recognized...
```

Check:

```text
1. Does terraform.exe exist?
2. Is the directory in PATH?
3. Did you open a new terminal?
4. Is there another Terraform installation?
```

Run:

```powershell
where.exe terraform
```

If nothing is returned, inspect your PATH.

---

# 🐧 8. Linux Installation

Use the official HashiCorp installation instructions for your Linux distribution:

[Terraform Install](https://developer.hashicorp.com/terraform/install)

For Ubuntu/Debian-based systems, the HashiCorp APT repository is one supported installation method.

After installation:

```bash
terraform version
```

---

# 🐧 9. Linux PATH

If Terraform is installed manually, ensure the executable is available through a directory in:

```text
$PATH
```

Check:

```bash
echo $PATH
```

Find Terraform:

```bash
which terraform
```

Verify:

```bash
terraform version
```

---

# 🍎 10. macOS Installation

Terraform can be installed through supported package-management methods such as Homebrew or through HashiCorp's official installation instructions.

Homebrew example:

```bash
brew tap hashicorp/tap
brew install hashicorp/tap/terraform
```

Verify:

```bash
terraform version
```

Official:

[Terraform Install](https://developer.hashicorp.com/terraform/install)

---

# 🔍 11. Check the Executable

Linux/macOS:

```bash
which terraform
```

Windows:

```powershell
where.exe terraform
```

Then:

```bash
terraform version
```

---

# 🧰 12. VS Code Setup

Recommended editor:

```text
Visual Studio Code
```

Download:

[Visual Studio Code](https://code.visualstudio.com/)

Install the official Terraform extension:

[HashiCorp Terraform Extension](https://marketplace.visualstudio.com/items?itemName=HashiCorp.terraform)

Benefits:

```text
Syntax highlighting
Formatting
Terraform language support
Validation assistance
Navigation
```

---

# 📂 13. Create Your First Project

Create:

```text
terraform-demo/
```

Inside:

```text
terraform-demo/
│
├── provider.tf
├── main.tf
└── outputs.tf
```

Open the folder in VS Code.

---

# 🧱 14. Create `provider.tf`

```hcl
terraform {
  required_providers {
    aws = {
      source = "hashicorp/aws"
    }
  }
}

provider "aws" {
  region = "us-east-1"
}
```

For production projects, define a deliberate provider version constraint and manage provider upgrades through review.

---

# 🪣 15. Create `main.tf`

```hcl
resource "aws_s3_bucket" "demo" {
  bucket = "replace-with-a-globally-unique-name"

  tags = {
    Name      = "Terraform-Demo"
    ManagedBy = "Terraform"
  }
}
```

Use a globally unique bucket name.

---

# 📤 16. Create `outputs.tf`

```hcl
output "bucket_name" {
  value = aws_s3_bucket.demo.bucket
}

output "bucket_arn" {
  value = aws_s3_bucket.demo.arn
}
```

---

# ☁️ 17. AWS CLI Installation

Install AWS CLI from the official AWS documentation:

[AWS CLI Installation](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)

Verify:

```bash
aws --version
```

Expected:

```text
aws-cli/2.x.x ...
```

The exact version will depend on the current AWS CLI release installed on your machine.

---

# 🔐 18. AWS Authentication Options

Terraform can obtain AWS credentials through supported credential mechanisms.

Common options:

```text
AWS IAM Identity Center / SSO
AWS CLI profile
Environment variables
EC2 IAM role
ECS task role
GitHub Actions OIDC
AssumeRole
Other AWS SDK credential-chain mechanisms
```

For local development, prefer short-lived or centrally managed authentication such as IAM Identity Center where available.

---

# 🧑‍💻 19. AWS CLI Profile

A traditional profile can be configured using:

```bash
aws configure --profile dev
```

Then verify:

```bash
aws sts get-caller-identity \
  --profile dev
```

You can use a profile with Terraform through the AWS provider or environment configuration.

Example:

```hcl
provider "aws" {
  region  = "us-east-1"
  profile = "dev"
}
```

Use profile-based authentication only when appropriate for your environment.

---

# 🔐 20. AWS IAM Identity Center / SSO

For organizations using IAM Identity Center:

```bash
aws configure sso
```

Then:

```bash
aws sso login --profile dev
```

Verify:

```bash
aws sts get-caller-identity --profile dev
```

This is generally preferable to keeping long-lived IAM user access keys on developer machines.

---

# 🌱 21. Environment Variables

AWS SDKs can also use environment variables.

Example:

```powershell
$env:AWS_PROFILE="dev"
```

Linux/macOS:

```bash
export AWS_PROFILE=dev
```

For temporary credential workflows, environment variables may also carry AWS access credentials.

Never commit credentials to Git.

---

# 🚨 22. What NOT To Do

Never put credentials directly into:

```hcl
provider "aws" {
  access_key = "AKIA..."
  secret_key = "..."
}
```

Do not commit:

```text
credentials
private keys
tokens
secrets
```

to GitHub.

---

# 🔍 23. Verify AWS Access

Run:

```bash
aws sts get-caller-identity
```

Example response structure:

```json
{
  "UserId": "...",
  "Account": "...",
  "Arn": "..."
}
```

This is one of the most useful commands for troubleshooting AWS authentication.

---

# 🧪 24. First Terraform Initialization

From your project directory:

```bash
terraform init
```

Terraform will:

```text
Read configuration
     ↓
Identify providers
     ↓
Download provider plugins
     ↓
Initialize working directory
```

You should see:

```text
Terraform has been successfully initialized!
```

---

# 📦 25. `.terraform` Directory

After:

```bash
terraform init
```

Terraform creates internal working files such as:

```text
.terraform/
```

Do not normally commit this directory to Git.

Add:

```gitignore
.terraform/
```

---

# 🔒 26. `.terraform.lock.hcl`

Terraform may create:

```text
.terraform.lock.hcl
```

Unlike `.terraform/`, this lock file is normally **committed to version control**.

It records selected provider versions/checksums and helps make provider installation more reproducible.

Recommended:

```text
.terraform/
        ❌ Do not commit

.terraform.lock.hcl
        ✅ Commit
```

---

# ✨ 27. Terraform Format

Run:

```bash
terraform fmt
```

Check formatting:

```bash
terraform fmt -check
```

Recommended:

```text
Developer
   ↓
terraform fmt
   ↓
Git
```

---

# ✅ 28. Terraform Validate

Run:

```bash
terraform validate
```

This checks the configuration for syntax and internal consistency.

Important:

```text
validate ≠ full cloud permission check
```

A configuration can validate successfully and still fail during `plan` or `apply`.

---

# 🔍 29. Terraform Plan

Run:

```bash
terraform plan
```

Terraform calculates the proposed changes.

Concept:

```text
Configuration
      +
Current State
      +
Provider Data
      ↓
Terraform Plan
      ↓
Proposed Changes
```

---

# 🚀 30. Terraform Apply

Run:

```bash
terraform apply
```

Terraform will normally ask for confirmation in interactive mode.

Type:

```text
yes
```

only after reviewing the plan.

---

# 📤 31. Check Outputs

After successful apply:

```bash
terraform output
```

Specific output:

```bash
terraform output bucket_name
```

---

# ☁️ 32. Verify in AWS

After:

```bash
terraform apply
```

verify your resource through AWS CLI or AWS Console.

For S3:

```bash
aws s3api head-bucket \
  --bucket YOUR_BUCKET_NAME
```

Use the correct account/profile/region context.

---

# 💥 33. Destroy the Lab

When the learning lab is complete:

```bash
terraform destroy
```

Review carefully before confirming.

This prevents unnecessary cloud costs.

---

# 🔄 34. Complete First-Lab Workflow

```text
Install Terraform
       ↓
Install AWS CLI
       ↓
Configure AWS Authentication
       ↓
Verify AWS Identity
       ↓
Create Terraform Files
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
Verify AWS Resource
       ↓
terraform destroy
```

---

# 🧪 35. Hands-On Labs

## Lab 01 — Terraform Version

```bash
terraform version
```

---

## Lab 02 — Find Terraform

Windows:

```powershell
where.exe terraform
```

Linux/macOS:

```bash
which terraform
```

---

## Lab 03 — VS Code

Install:

```text
VS Code
HashiCorp Terraform extension
```

---

## Lab 04 — AWS CLI

```bash
aws --version
```

---

## Lab 05 — AWS Identity

```bash
aws sts get-caller-identity
```

---

## Lab 06 — AWS Profile

Create:

```text
dev
```

and verify the identity.

---

## Lab 07 — Terraform Provider

Create:

```text
provider.tf
```

---

## Lab 08 — First S3 Resource

Create:

```text
main.tf
```

---

## Lab 09 — Terraform Init

```bash
terraform init
```

---

## Lab 10 — Terraform Format

```bash
terraform fmt
```

---

## Lab 11 — Terraform Validate

```bash
terraform validate
```

---

## Lab 12 — Terraform Plan

```bash
terraform plan
```

---

## Lab 13 — Terraform Apply

```bash
terraform apply
```

---

## Lab 14 — Terraform Output

```bash
terraform output
```

---

## Lab 15 — AWS Verification

Verify the created S3 bucket using AWS CLI.

---

## Lab 16 — Terraform Destroy

```bash
terraform destroy
```

---

## Lab 17 — PATH Troubleshooting

Intentionally diagnose a Terraform PATH issue.

---

## Lab 18 — Wrong AWS Profile

Use the wrong profile and troubleshoot the authentication failure.

---

## Lab 19 — Region Troubleshooting

Use a different region and understand regional resources.

---

## Lab 20 — First Git Repository

Create:

```text
.gitignore
README.md
.terraform.lock.hcl
```

and push the safe Terraform project to GitHub.

---

# 🧹 36. Recommended `.gitignore`

```gitignore
# Terraform working directory
.terraform/

# Terraform state
*.tfstate
*.tfstate.*

# Crash logs
crash.log
crash.*.log

# Variable files may contain secrets
*.tfvars
*.tfvars.json

# Override files
override.tf
override.tf.json
*_override.tf
*_override.tf.json

# OS / IDE
.vscode/
.idea/
.DS_Store
Thumbs.db
```

Remember:

```text
.terraform.lock.hcl
        ↓
Normally commit this
```

---

# 🔐 37. Installation Security Checklist

```text
☑ Download Terraform from HashiCorp
☑ Verify Terraform version
☑ Keep Terraform updated through a controlled process
☑ Install official VS Code extension
☑ Install AWS CLI from AWS
☑ Avoid hard-coded credentials
☑ Prefer IAM Identity Center for local organizational access
☑ Use least privilege
☑ Protect Terraform state
☑ Never commit secrets
☑ Commit provider lock file
```

---

# 🚨 38. Common Errors

## Error: Terraform Not Found

Check:

```bash
terraform version
```

Then:

```text
PATH
Installation directory
Terminal restart
Multiple installations
```

---

## Error: AWS Credentials Not Found

Run:

```bash
aws sts get-caller-identity
```

Check:

```text
Profile
SSO login
Environment variables
IAM role
Credential chain
```

---

## Error: AccessDenied

Possible causes:

```text
Wrong AWS account
Wrong profile
Insufficient IAM permissions
Expired SSO session
Wrong role
```

---

## Error: Provider Installation Failed

Check:

```text
Internet connectivity
Provider source
Version constraint
Registry availability
Proxy/firewall
```

Then retry:

```bash
terraform init
```

---

## Error: S3 Bucket Already Exists

S3 bucket names are globally unique.

Choose another name.

---

# 🧠 39. Terraform Installation vs Terraform Configuration

Important distinction:

```text
Installation
    ↓
Terraform binary
    ↓
terraform version
```

Configuration:

```text
provider.tf
main.tf
variables.tf
outputs.tf
```

Authentication:

```text
AWS CLI / SSO / Profile / Role / OIDC
```

Infrastructure:

```text
terraform plan
terraform apply
```

---

# 🏢 40. Enterprise Developer Workstation

Recommended:

```text
Developer Laptop
│
├── Git
├── VS Code
├── Terraform
├── AWS CLI
├── Docker
├── kubectl
└── Security Tools
```

Then:

```text
GitHub
   ↓
GitHub Actions
   ↓
Terraform
   ↓
AWS
```

---

# 🔗 41. Official Resources

- [Terraform Installation](https://developer.hashicorp.com/terraform/install)
- [Terraform Documentation](https://developer.hashicorp.com/terraform/docs)
- [Terraform Language](https://developer.hashicorp.com/terraform/language)
- [Terraform Registry](https://registry.terraform.io/)
- [AWS CLI Installation](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)
- [AWS CLI Command Reference](https://docs.aws.amazon.com/cli/latest/reference/)
- [VS Code](https://code.visualstudio.com/)
- [HashiCorp Terraform VS Code Extension](https://marketplace.visualstudio.com/items?itemName=HashiCorp.terraform)

---

# 🎓 42. Interview Questions

### Beginner

1. How do you install Terraform?
2. How do you verify Terraform installation?
3. What is PATH?
4. What is HCL?
5. How do you install AWS CLI?
6. How do you verify AWS credentials?
7. What is `terraform init`?
8. What is `terraform fmt`?
9. What is `terraform validate`?
10. What is `terraform plan`?

### Intermediate

11. Why is `.terraform.lock.hcl` committed?
12. Why is `.terraform/` ignored?
13. Why should Terraform state not be committed?
14. How does Terraform authenticate to AWS?
15. What is AWS IAM Identity Center?
16. What is an AWS CLI profile?
17. How do you troubleshoot `AccessDenied`?
18. How do you troubleshoot missing credentials?
19. What happens during `terraform init`?
20. Why should production credentials not be stored on developer machines?

### Advanced

21. How would you design secure Terraform authentication?
22. How would GitHub Actions authenticate to AWS?
23. Why is OIDC useful for Terraform CI/CD?
24. How would you protect Terraform state?
25. How would you standardize Terraform installation across an engineering team?

---

# ⚡ 43. Cheat Sheet

```bash
# Terraform
terraform version

terraform fmt

terraform fmt -check

terraform init

terraform validate

terraform plan

terraform apply

terraform output

terraform destroy

# AWS
aws --version

aws sts get-caller-identity

aws configure list

aws configure list-profiles

aws sso login --profile dev

aws sts get-caller-identity --profile dev
```

---

# 🏆 44. Setup Completion Checklist

```text
INSTALLATION
☑ Terraform installed
☑ PATH configured
☑ terraform version works
☑ AWS CLI installed
☑ aws --version works
☑ VS Code installed
☑ Terraform extension installed

AUTHENTICATION
☑ AWS identity verified
☑ Correct AWS account
☑ Correct profile/SSO session
☑ No hard-coded credentials

PROJECT
☑ provider.tf
☑ main.tf
☑ outputs.tf
☑ .gitignore

TERRAFORM
☑ terraform fmt
☑ terraform init
☑ terraform validate
☑ terraform plan
☑ terraform apply
☑ terraform output
☑ terraform destroy

GITHUB
☑ .terraform ignored
☑ State ignored
☑ Sensitive tfvars ignored
☑ .terraform.lock.hcl committed
```

---

# 🗺️ 45. Next Module

You have now completed:

```text
01 Terraform Fundamentals       🟢
02 Terraform Installation       🟢 ← YOU ARE HERE
```

Next:

```text
03-Terraform-CLI-and-Workflow/
```

We will go deep into:

```text
terraform init
terraform fmt
terraform validate
terraform plan
terraform apply
terraform destroy
terraform show
terraform output
terraform state
terraform refresh concepts
terraform graph
terraform console
terraform import concepts
Terraform workflow
Terraform lifecycle
CI/CD workflow
```

---

<div align="center">

# ⚙️ INSTALL • CONFIGURE • VERIFY • BUILD

### VishwaTech Labs

**Terraform • AWS • Azure • GCP • Docker • GitHub Actions • Kubernetes • Ansible • DevSecOps**

### By Vishwanath Gowda H

⭐ From `terraform version` to your first cloud resource!

</div>
