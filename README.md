# Multi-OS AWS EC2 Cluster Provisioning with Terraform & Ansible

## Overview
This project provides an automated Infrastructure as Code (IaC) and configuration management solution to provision and configure a multi-node, multi-OS cluster on AWS EC2. It uses Terraform to spin up the infrastructure (1 master and 3 worker nodes across different Linux distributions) and Ansible to automate the software configuration on the provisioned nodes.

## Architecture & Tech Stack
- **Terraform**: Provisions AWS EC2 instances (Ubuntu, Amazon Linux, Red Hat, Debian), security groups, and SSH key pairs.
- **Ansible**: Configures the provisioned worker nodes, updates package caches, and installs essential tools.
- **Dynamic Inventory**: Terraform automatically generates an Ansible inventory file (`ansible/inventories/hosts.ini`) upon successful infrastructure creation, populated with dynamic public IPs and correct SSH usernames.

## Project Structure
```text
.
├── ansible/
│   ├── ansible.cfg         # Ansible configuration
│   ├── inventories/        # Contains the dynamically generated hosts.ini
│   └── playbook/
│       ├── run.yml         # Main playbook to configure worker nodes
│       └── vars.yml        # Variables for Ansible playbooks
└── terraform/
    ├── main.tf             # Main Terraform entry point
    ├── dynamic.tf          # Generates the dynamic Ansible inventory
    ├── modules/ec2/        # Reusable module for provisioning EC2 instances
    └── ...                 # Other Terraform configuration files
```

## Prerequisites
- **AWS CLI** installed and configured with appropriate credentials (`aws configure`).
- **Terraform** installed (>= 1.0.0).
- **Ansible** installed (>= 2.9).
- An SSH key pair named `key-file` (or configured otherwise) placed in the `terraform/` directory.

## Usage

### 1. Provision Infrastructure
Navigate to the `terraform` directory, initialize the working directory, and apply the configuration to spin up the infrastructure:

```bash
cd terraform
terraform init
terraform apply
```
Type `yes` when prompted. This will create the EC2 instances and automatically generate the Ansible inventory file at `ansible/inventories/hosts.ini`.

### 2. Configure Nodes with Ansible
Navigate to the `ansible` directory and run the playbook to configure the worker nodes:

```bash
cd ../ansible
ansible-playbook playbook/run.yml -i inventories/hosts.ini
```
This will run the setup tasks on all hosts defined in the `workers` group.

### 3. Teardown
To destroy the provisioned infrastructure and avoid incurring AWS costs, run the following command in the `terraform` directory:

```bash
cd ../terraform
terraform destroy
```
Type `yes` when prompted to confirm the deletion.

## Features
- **Multi-OS Support**: Handles varying SSH users (`ubuntu`, `ec2-user`, `admin`) and package managers (e.g., `apt` for Debian/Ubuntu) dynamically.
- **Automated Handoff**: Seamlessly bridges the gap between Terraform provisioning and Ansible configuration via dynamic file generation.

## License
MIT License
