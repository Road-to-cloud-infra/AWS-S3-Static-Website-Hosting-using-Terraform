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

⚡ AWS Toolkit: (Optional) Provides integrated AWS resource management right from your sidebar.

-----------------------------------------------------------------------------------------------------------------------------------------------------

📂 Terraform Structure:

```text
.
├── main.tf           # Core logic (S3 bucket, hosting config, policy, inline HTML objects)
├── outputs.tf        # Output variables (Prints out the live S3 Website URL)
├── providers.tf      # Defines the AWS provider version constraints
├── terraform.tfvars  # Environment-specific variables (Your specific bucket name/region)
└── variables.tf      # Input variable definitions (Names, regions, and resource tags)
