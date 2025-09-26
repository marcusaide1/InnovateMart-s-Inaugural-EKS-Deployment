# InnovateMart-s-Inaugural-EKS-Deployment

# Project Bedrock - Cloud Infrastructure & Application Platform

![Architecture Diagram](./docs/architecture-diagram.png)

## Overview
Project Bedrock is a cloud-native application platform built on AWS, featuring automated infrastructure provisioning, Kubernetes orchestration, and CI/CD pipelines.

## Features
- **Infrastructure as Code**: Terraform-managed AWS resources
- **Kubernetes Cluster**: EKS with auto-scaling node groups
- **Managed Database**: PostgreSQL RDS with high availability
- **CI/CD Pipeline**: GitHub Actions for automated deployments
- **Secure Networking**: VPC with public/private subnets

## Quick Start
```bash
# 1. Clone repository
git clone https://github.com/your-username/project-bedrock.git
cd project-bedrock

# 2. Set up infrastructure
cd infrastructure
terraform init
terraform apply

# 3. Deploy application
cd ../kubernetes
kubectl apply -f namespace.yaml
kubectl apply -f app/



Architecture
Frontend: NGINX/Application Load Balancer

Backend: Kubernetes Deployment on EKS

Database: PostgreSQL RDS in private subnets

Networking: Multi-AZ VPC with NAT Gateway



Requirements
Terraform >= 1.0

kubectl >= 1.25

AWS CLI >= 2.0

GitHub Account (for CI/CD)


Access Instructions
Application URL: http://project-bedrock-dev-alb-123456.eu-west-1.elb.amazonaws.com

Read-only Access:

Kubernetes: Use provided kubeconfig

Database: Connection string in Secrets Manager

AWS: IAM user with read-only permissions




## 2. `docs/architecture-guide.md`

```markdown
# Architecture Guide

## System Overview
Project Bedrock implements a three-tier architecture on AWS with complete infrastructure automation.

## Components

### 1. Networking (VPC)
- **CIDR**: 10.0.0.0/16
- **Public Subnets**: 10.0.1.0/24, 10.0.2.0/24, 10.0.3.0/24
- **Private Subnets**: 10.0.101.0/24, 10.0.102.0/24, 10.0.103.0/24
- **Database Subnets**: 10.0.201.0/24, 10.0.202.0/24, 10.0.203.0/24

### 2. Compute (EKS Cluster)
- **Kubernetes Version**: 1.28
- **Node Group**: t3.medium/t3.large instances
- **Auto-scaling**: 2-5 nodes
- **Network Plugin**: AWS VPC CNI

### 3. Database (RDS PostgreSQL)
- **Engine**: PostgreSQL 15.7
- **Instance**: db.t3.micro
- **Storage**: 20GB GP2
- **Backup**: 7-day retention

### 4. Security
- **IAM Roles**: Least privilege access
- **Security Groups**: Application-specific rules
- **Secrets Management**: AWS Secrets Manager
- **Encryption**: AES-256 at rest, TLS in transit

## Data Flow
1. User → Application Load Balancer → EKS Ingress → Kubernetes Service → Pods → RDS Database

## High Availability
- Multi-AZ deployment (3 availability zones)
- Auto-scaling node groups
- Database backups and snapshots
- Stateful resource redundancy

docs/deployment-guide.md
# Deployment Guide

## Prerequisites
- AWS Account with appropriate permissions
- Terraform, kubectl, AWS CLI installed
- GitHub repository fork

## Phase 1: Infrastructure Setup

### 1.1 Configure AWS Credentials
```bash
aws configure
# AWS Access Key ID: [YOUR_ACCESS_KEY]
# AWS Secret Access Key: [YOUR_SECRET_KEY]
# Default region: eu-west-1



Deploy Infrastructure
cd infrastructure/
terraform init
terraform plan
terraform apply  # Approve when prompted


Save Outputs
terraform output > ../outputs.txt

Phase 2: Kubernetes Configuration
2.1 Configure kubectl
aws eks update-kubeconfig --region eu-west-1 --name project-bedrock-dev-cluster
kubectl get nodes  # Verify connection

Deploy Application
cd ../kubernetes/
kubectl apply -f namespace.yaml
kubectl apply -f app/

Verify Deployment
kubectl get all -n project-bedrock
kubectl get ingress -n project-bedrock

Phase 3: CI/CD Setup
3.1 Configure GitHub Secrets
Add these secrets to your GitHub repository:

AWS_ACCESS_KEY_ID

AWS_SECRET_ACCESS_KEY

KUBECONFIG_BASE64 (base64-encoded kubeconfig)

3.2 Push to Trigger Deployment
bash
git add .
git commit -m "Initial deployment"
git push origin main
Access Instructions
Application Access
URL: Check Load Balancer DNS name:

bash
kubectl get ingress -n project-bedrock
Database Access
Connection String:

text
postgresql://bedrockadmin:[PASSWORD]@[RDS_ENDPOINT]:5432/projectbedrock
Get Credentials:

bash
# Database endpoint
cd infrastructure/
terraform output rds_instance_endpoint

# Password (stored in Secrets Manager)
aws secretsmanager get-secret-value --secret-id project-bedrock-dev-db-password
Read-only Access
Kubernetes Read-only Access
bash
# Download kubeconfig with read-only permissions
aws eks update-kubeconfig --region eu-west-1 --name project-bedrock-dev-cluster --role-arn arn:aws:iam::[ACCOUNT_ID]:role/ReadOnlyRole

# Test access
kubectl get pods -n project-bedrock --as read-only-user
Database Read-only User
sql
-- Connect to database and create read-only user
CREATE USER read_only_user WITH PASSWORD 'secure_password';
GRANT CONNECT ON DATABASE projectbedrock TO read_only_user;
GRANT USAGE ON SCHEMA public TO read_only_user;
GRANT SELECT ON ALL TABLES IN SCHEMA public TO read_only_user;
Troubleshooting
Common Issues
Terraform State Lock: Check DynamoDB table

EKS Node Connection: Verify node group status

Database Connection: Check security groups

Ingress Not Working: Verify ALB configuration

Logs and Monitoring
bash
# Application logs
kubectl logs -n project-bedrock -l app=bedrock-app

# Cluster events
kubectl get events -n project-bedrock

# Resource utilization
kubectl top pods -n project-bedrock
Cleanup
bash
cd infrastructure/
terraform destroy  # Remove all resources
text

## 4. IAM Policies in `policies/` directory

**A. `policies/eks-node-iam-policy.json`**
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "ec2:DescribeInstances",
        "ec2:DescribeRegions",
        "ecr:GetAuthorizationToken",
        "ecr:BatchCheckLayerAvailability",
        "ecr:GetDownloadUrlForLayer",
        "ecr:GetRepositoryPolicy",
        "ecr:DescribeRepositories",
        "ecr:ListImages",
        "ecr:BatchGetImage"
      ],
      "Resource": "*"
    }
  ]
}
B. policies/rds-access-policy.json

json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "rds:DescribeDBInstances",
        "rds:DescribeDBClusters",
        "rds:DownloadDBLogFilePortion",
        "rds:ListTagsForResource"
      ],
      "Resource": "*"
    }
  ]
}
C. policies/s3-state-policy.json

json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:GetObject",
        "s3:ListBucket",
        "s3:GetBucketLocation"
      ],
      "Resource": [
        "arn:aws:s3:::project-bedrock-dev-tfstate-*",
        "arn:aws:s3:::project-bedrock-dev-tfstate-*/*"
      ]
    }
  ]
}
5. Create the directory structure and files
bash
# Create directories
mkdir -p docs policies

# Create README
cat > README.md << 'EOF'
[PASTE README CONTENT HERE]
EOF

# Create architecture guide
cat > docs/architecture-guide.md << 'EOF'
[PASTE ARCHITECTURE GUIDE CONTENT HERE]
EOF

# Create deployment guide
cat > docs/deployment-guide.md << 'EOF'
[PASTE DEPLOYMENT GUIDE CONTENT HERE]
EOF

# Create IAM policies
cat > policies/eks-node-iam-policy.json << 'EOF'
[PASTE IAM POLICY CONTENT HERE]
EOF

# Create architecture diagram placeholder
touch docs/architecture-diagram.png
6. Final Steps
Create GitHub repository:

bash
git init
git add .
git commit -m "Initial commit: Project Bedrock infrastructure and documentation"
git branch -M main
git remote add origin https://github.com/your-username/project-bedrock.git
git push -u origin main
Create architecture diagram (you can use tools like draw.io, Lucidchart, or even ASCII):

text
+----------------+      +----------------+      +---------------+
|   Internet     |----->|   ALB (80/443) |----->|   EKS Cluster |
+----------------+      +----------------+      +---------------+
                                                       |
                                                       v
                                            +-------------------+
                                            |   RDS PostgreSQL  |
                                            +-------------------+
Update with actual values from your Terraform outputs:

ALB DNS name

RDS endpoint

EKS cluster name

This documentation provides a complete professional package for your cloudlaunch assignment with clear instructions, architecture overview, and access guidelines.


