🚀 AWS S3 Static Website Hosting via Terraform:

Welcome to your automated Infrastructure as Code (IaC) repository for provisioning and hosting a static website on Amazon Web Services (AWS) using HashiCorp Terraform.
This project eliminates manual infrastructure management, spinning up a fully functioning, public-facing website entirely from code.

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------

🌟 Project Overview:


This repository houses the native Terraform configurations required to automatically provision, configure, and secure a cost-effective static website.


💡 What Makes It Unique?

Zero-File Footprint: You don't need an external source or src/ directory. Terraform dynamically injects and provisions index.html and error.html straight into the cloud infrastructure.

100% Pure IaC: No GitHub Actions, third-party tooling, or complex CI/CD dependencies are required to get your site live.

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------

🏗️ Architecture Design:

The infrastructure deployed by this Terraform workflow implements a robust, secure, and modern AWS S3 layout:

🪣 Amazon S3 Bucket: Serves as the high-availability storage layer for web assets.

🌐 S3 Static Website Configuration: Activates native HTTP endpoints to serve content directly to browsers.

🛡️ S3 Public Access Block Overrides: Safely overrides account-level blocks to permit controlled public traffic to the website assets.

📜 S3 Bucket IAM Policy: Grants explicit, granular s3:GetObject read-only permissions to anonymous internet traffic.

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------

🛠️ Prerequisites & Workspace Setup:

To run this project seamlessly within Visual Studio Code, ensure your local development environment is configured with the following tools:

🧰 Required Software:

🗃️ Visual Studio Code: The primary IDE used for writing and executing the code.

🏗️ Terraform CLI: Core binary (>= 1.0) installed on your local machine and added to your system's PATH.

💻 AWS CLI: Installed locally to manage authentication.

🔌 Recommended VS Code Extensions:

To make editing a breeze, install these extensions from the VS Code Marketplace:

🧩 HashiCorp Terraform: Enables syntax highlighting, autocompletion, and real-time formatting (terraform fmt) as you type.

⚡ AWS Toolkit: Provides integrated AWS resource management right from your sidebar.

-----------------------------------------------------------------------------------------------------------------------------------------------------

📂 Terraform Structure:

```text
.
├── main.tf           # Core logic (S3 bucket, hosting config, policy, inline HTML objects)
├── outputs.tf        # Output variables (Prints out the live S3 Website URL)
├── providers.tf      # Defines the AWS provider version constraints
├── terraform.tfvars  # Environment-specific variables (Your specific bucket name/region)
└── variables.tf      # Input variable definitions (Names, regions, and resource tags)
```

## 🔧 Step-by-Step Configuration & Deep-Dive

This section breaks down each configuration file created in Visual Studio Code, explaining **what** the code does and **why** it is architected this way.

----

### 1️⃣ `providers.tf` (The Cloud Connector)
This file establishes the connection between Terraform and Amazon Web Services (AWS).

```hcl
terraform {
  required_version = ">= 1.0"
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
}

provider "aws" {
  region = var.aws_region
}
```
- _What it does: It tells Terraform to download and use the official HashiCorp AWS provider plugin and sets the deployment region._

- _Why we use it: Pinning the provider version (~> 5.0) prevents breaking changes. If AWS updates its provider tomorrow, your code remains stable because it locks down a compatible version._

----

2️⃣ `**Variables.tf**` (The Input Framework):

This file defines the input parameters used to customize your infrastructure without touching the core code.

```hcl

#------------Region-------------------
variable "aws_region" {
  description = "The AWS region to deploy resources into"
  type        = string
  default     = "ap-southeast-1" # Defaults to Singapore; change as needed
}

#------------Bucket-------------------
variable "bucket_name" {
  description = "The globally unique name for the S3 bucket hosting the website"
  type        = string
}

#------------Tags-------------------
variable "tags" {
  description = "A map of tags to assign to the resources"
  type        = map(string)
  default = {
    Environment = "Dev"
    Project     = "Static-Website"
    ManagedBy   = "Terraform"
  }
}
```
- _What it does: It sets up templates for data like your region, bucket naming schema, and operational tracking tags._

- _Why we use it: Hardcoding values inside your logic is a bad practice. Utilizing variables makes your infrastructure modular, repeatable, and easily shareable across different environments (like Dev, QA, or Production)._

----

3️⃣ `**Terraform.tfvars**` (The Parameter Values)"

This is where you supply the concrete, real-world values for the inputs defined in variables.tf.

```hcl

aws_region  = "ap-southeast-1"
bucket_name = "myfirst-s3bucket-static-website-hosting-2026" (#Completely lowercase, hyphens only, no spaces!)

```

- _What it does: It injects your actual bucket name and chosen region into the configuration at runtime._

- _Why we use it: It keeps environment definitions separate from code. Note: S3 bucket names must be entirely lowercase, contain no spaces, and be globally unique across all AWS accounts._

---

4️⃣ `**Main.tf**` (The Core Infrastructure Logic):

This is the heart of the project. It explicitly builds your AWS resources and writes your website code directly onto the cloud block.

```hcl


# ------------------1. Provision the S3 Bucket-------------------
resource "aws_s3_bucket" "website" {
  bucket        = var.bucket_name
  force_destroy = true # Allows a clean 'terraform destroy' even with files inside

  tags = var.tags
}

# ------------------2. Open Public Access (Required for public-facing web hosting)-------------------
resource "aws_s3_bucket_public_access_block" "website_public_access" {
  bucket = aws_s3_bucket.website.id

  block_public_acls       = false
  block_public_policy     = false
  ignore_public_acls      = false
  restrict_public_buckets = false
}

# ------------------3. Enable Static Website Hosting Properties-------------------
resource "aws_s3_bucket_website_configuration" "website_config" {
  bucket = aws_s3_bucket.website.id

  index_document {
    suffix = "index.html"
  }

  error_document {
    key = "error.html"
  }
}

# ------------------4. Attach the Public Read Policy (Read-Only access for anonymous traffic)-------------------
resource "aws_s3_bucket_policy" "allow_public_access" {
  depends_on = [aws_s3_bucket_public_access_block.website_public_access]
  bucket     = aws_s3_bucket.website.id

  policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Sid       = "PublicReadGetObject"
        Effect    = "Allow"
        Principal = "*"
        Action    = "s3:GetObject"
        Resource  = "${aws_s3_bucket.website.arn}/*"
      }
    ]
  })
}

# ------------------5. Render index.html inline via Terraform-------------------
resource "aws_s3_object" "index" {
  bucket       = aws_s3_bucket.website.id
  key          = "index.html"
  content_type = "text/html"
  content      = <<EOF
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Welcome To My Site</title>
    <style>
        body { font-family: 'Segoe UI', Arial, sans-serif; text-align: center; margin-top: 120px; background-color: #f8f9fa; color: #212529; }
        .card { max-width: 500px; margin: 0 auto; background: white; padding: 40px; border-radius: 12px; box-shadow: 0 4px 6px rgba(0,0,0,0.05); }
        h1 { color: #0078d4; margin-bottom: 10px; }
        p { color: #6c757d; font-size: 1.1em; }
    </style>
</head>
<body>
    <div class="card">
        <h1>🚀 Hello, World!</h1>
        <p>This static website was fully provisioned and uploaded entirely via <strong>Terraform</strong> inside VS Code.</p>
    </div>
</body>
</html>
EOF
}

# ------------------6. Render error.html inline via Terraform-------------------
resource "aws_s3_object" "error" {
  bucket       = aws_s3_bucket.website.id
  key          = "error.html"
  content_type = "text/html"
  content      = <<EOF
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>404 Not Found</title>
    <style>
        body { font-family: 'Segoe UI', Arial, sans-serif; text-align: center; margin-top: 120px; background-color: #fff5f5; color: #212529; }
        .card { max-width: 500px; margin: 0 auto; background: white; padding: 40px; border-radius: 12px; box-shadow: 0 4px 6px rgba(0,0,0,0.05); border-top: 4px solid #e53e3e; }
        h1 { color: #e53e3e; }
        p { color: #4a5568; margin-bottom: 20px; }
        a { color: #3182ce; text-decoration: none; font-weight: bold; }
    </style>
</head>
<body>
    <div class="card">
        <h1>🔍 404 - Page Not Found</h1>
        <p>Oops! The page you are trying to visit does not exist.</p>
        <a href="index.html">← Return Home</a>
    </div>
</body>
</html>
EOF
}

```

- _What it does: It provisions the S3 bucket, flips the AWS safety blocks to "off" to allow public hosting traffic, defines index.html as the landing page, grants explicit read permissions to internet traffic, and renders the raw HTML objects._

- _Why we use it:_

   - _force_destroy = true ensures that when you run a teardown, Terraform cleans out the files and successfully deletes the bucket without manual intervention._

   - _depends_on forces the public access blocks to be altered before applying the bucket policy, avoiding access denied loops during compilation._

   - _Utilizing inline content strings for aws_s3_object drops your HTML text straight into the cloud natively, removing any dependencies on Git or tracking local asset folder trees._
 

5️⃣ `**outputs.tf**` (The Post-Deployment Print):

This file extracts computed data directly from AWS after a successful execution.

```hcl
output "website_url" {
  description = "The S3 bucket static website hosting endpoint URL"
  value       = "http://${aws_s3_bucket_website_configuration.website_config.website_endpoint}"
}

```

- _What it does: It intercepts the dynamic website endpoint link generated by AWS and displays it clearly in your VS Code terminal window._

- _Why we use it: S3 website URLs are long and randomly structured based on region and bucket name. This saves you from having to log into the AWS Management Console to find your live web URL._
