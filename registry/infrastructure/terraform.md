# Terraform & Infrastructure as Code (IaC) Guidelines

These rules dictate how Terraform code must be structured. The absolute priority is declarative safety, state protection, and the Principle of Least Privilege for cloud resources.

## 1. State Management
* **Remote State Only:** Never use local state (`terraform.tfstate` committed to Git) for production configurations. Always configure a remote backend (e.g., AWS S3 + DynamoDB for locking, or Terraform Cloud) to prevent state corruption during concurrent executions.

## 2. Security Defaults
* **Closed by Default:** When creating Security Groups, Firewalls, or IAM Policies, strictly apply the Principle of Least Privilege. Never use `0.0.0.0/0` for inbound traffic on internal services (databases, caches, internal APIs).
* **Encryption at Rest:** Every storage resource (S3 buckets, RDS databases, EBS volumes) MUST have encryption at rest explicitly enabled.

## 3. Modularity & Variables
* **No Hardcoded Values:** Do not hardcode environment-specific values (like instance sizes, domain names, or counts) inside the resource blocks. Pass them as variables (`var.instance_type`).
* **Use Modules:** Group related resources (e.g., a VPC + Subnets + Route Tables) into reusable Terraform Modules rather than placing hundreds of lines in a single `main.tf`.