# AWS-S3-Static-Website-Hosting-using-Terraform
This repository contains the Infrastructure as Code (IaC) configuration to automatically provision and configure a highly available, secure, and cost-effective static website hosting architecture on Amazon Web Services (AWS) using HashiCorp Terraform.

🚀 Project Overview
This project automates the deployment of a static website (HTML) using AWS S3. It configures the necessary bucket policies, public access blocks, and website hosting configurations to serve your web assets directly to the internet.

🏗️ Architecture
The infrastructure deployed by this Terraform configuration includes:

Amazon S3 Bucket: Stores the static website files (index.html, error.html).
S3 Bucket Website Configuration: Enables the static website hosting feature on the bucket.
S3 Bucket Public Access Block: Explicitly manages public access settings to allow public read access for website delivery.
S3 Bucket Policy: Grants s3:GetObject permissions to anonymous users so the website is publicly readable.

🛠️ Prerequisites
Before you begin, ensure you have the following installed and configured:
Terraform: Version ~> 1.0 or higher installed locally.
AWS CLI: Configured with administrative or appropriate IAM permissions to manage S3 buckets.
AWS Account: An active AWS account.

📂 Terraform Structure

├── main.tf               # Main Terraform configuration (S3 resources & policies)
├── variables.tf          # Input variables (Bucket name, AWS region, tags)
├── outputs.tf            # Output values (Website endpoint URL)
├── providers.tf          # AWS Provider and Terraform version constraints
├── terraform.tfvars      # (Ignored) Environment-specific variable values
└── src/                  # Website source code directory
    ├── index.html        # Main landing page
    └── error.html        # Custom 404 error page
