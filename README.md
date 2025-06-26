# Required Remainder Infrastructure

This repository contains the AWS CloudFormation infrastructure for deploying the complete Required Remainder application stack. It provides a production-ready, scalable, and secure infrastructure using modern AWS services.

## 🏗️ Architecture Overview

### Backend Infrastructure
- **VPC** with public and private subnets across 2 AZs
- **ECS Fargate Cluster** for running Spring Boot containers
- **ECR Repository** for storing Docker images
- **Internal Application Load Balancer** for enhanced security
- **IAM Roles** with least-privilege permissions
- **Security Groups** with proper network isolation

### Frontend Infrastructure
- **S3 Bucket** for static file hosting
- **CloudFront Distribution** for global CDN and HTTPS
- **IAM Roles** for S3 and CloudFront access

### Shared Infrastructure
- **VPC** with proper subnet configuration
- **Internet Gateway** and **NAT Gateway** for connectivity
- **Security Groups** with appropriate rules

## 🚀 Deployment Options

### GitHub Actions

The infrastructure can be deployed automatically using GitHub Actions workflows:

1. **Navigate to Actions tab** in your GitHub repository
2. **Select "Deploy Infra" workflow**
3. **Click "Run workflow"** to trigger deployment
4. **Monitor the deployment** in the Actions tab

The workflow will:
- Validate CloudFormation templates
- Deploy frontend infrastructure (S3 + CloudFront)
- Deploy backend infrastructure (VPC, ECS, ECR, ALB)
- Output stack information and next steps

### Manual Deployment

#### Prerequisites
- AWS CLI configured with appropriate permissions
- AWS credentials with CloudFormation, ECS, ECR, S3, CloudFront permissions

#### Deploy Frontend Infrastructure
```bash
aws cloudformation deploy \
  --template-file cloudformation/frontend.yaml \
  --stack-name required-remainder-frontend \
  --capabilities CAPABILITY_NAMED_IAM \
  --parameter-overrides Environment=dev
```

#### Deploy Backend Infrastructure
```bash
aws cloudformation deploy \
  --template-file cloudformation/backend.yaml \
  --stack-name required-remainder-backend \
  --capabilities CAPABILITY_NAMED_IAM \
  --parameter-overrides \
    Environment=dev \
    ContainerImage=public.ecr.aws/amazonlinux/amazonlinux:2 \
    ECRRepositoryName=required-remainder-backend \
    DeployService=false
```

## 🔐 Required GitHub Secrets

Configure these secrets in your GitHub repository for automated deployment:

| Secret | Description | Required | How to Obtain |
|--------|-------------|----------|---------------|
| `AWS_ACCESS_KEY_ID` | AWS access key | ✅ | AWS IAM > Users > Security credentials |
| `AWS_SECRET_ACCESS_KEY` | AWS secret key | ✅ | AWS IAM > Users > Security credentials |

### IAM Permissions Required

The AWS user/role needs the following AWS managed policies:

| Policy | Type | Access Level |
|--------|------|--------------|
| `AmazonEC2ContainerRegistryFullAccess` | AWS managed | Full access to ECR |
| `AmazonEC2FullAccess` | AWS managed | Full access to EC2 (VPC, security groups, load balancers) |
| `AmazonECS_FullAccess` | AWS managed | Full access to ECS |
| `AmazonS3FullAccess` | AWS managed | Full access to S3 |
| `AWSCloudFormationFullAccess` | AWS managed | Full access to CloudFormation |
| `CloudFrontFullAccess` | AWS managed | Full access to CloudFront |
| `CloudWatchLogsFullAccess` | AWS managed | Full access to CloudWatch Logs |
| `IAMFullAccess` | AWS managed | Full access to IAM (for creating service roles) |

## 📋 CloudFormation Templates

### Backend Template (`cloudformation/backend.yaml`)

**Resources Created:**
- VPC with public and private subnets
- Internet Gateway and NAT Gateway
- ECS Cluster and Task Definition
- ECR Repository
- Application Load Balancer (internal)
- Target Group and Listener
- Security Groups
- IAM Roles and Policies

**Parameters:**
- `Environment`: Environment name (dev, staging, prod)
- `ContainerImage`: Docker image URI
- `ECRRepositoryName`: ECR repository name
- `DeployService`: Whether to create ECS service (true/false)

### Frontend Template (`cloudformation/frontend.yaml`)

**Resources Created:**
- S3 Bucket for static hosting
- CloudFront Distribution
- IAM User for deployment
- Bucket Policy

**Parameters:**
- `Environment`: Environment name (dev, staging, prod)

## 🔍 Stack Outputs

### Backend Stack Outputs
- `ECRRepoUri`: ECR repository URI
- `LoadBalancerDNS`: Internal ALB DNS name
- `VPCId`: VPC ID
- `PrivateSubnetIds`: Private subnet IDs
- `SecurityGroupId`: Security group ID

### Frontend Stack Outputs
- `S3BucketName`: S3 bucket name
- `CloudFrontDistributionId`: CloudFront distribution ID
- `CloudFrontDomainName`: CloudFront domain name


### Update Infrastructure
```bash
# Update backend
aws cloudformation deploy \
  --template-file cloudformation/backend.yaml \
  --stack-name required-remainder-backend \
  --capabilities CAPABILITY_NAMED_IAM

# Update frontend
aws cloudformation deploy \
  --template-file cloudformation/frontend.yaml \
  --stack-name required-remainder-frontend \
  --capabilities CAPABILITY_NAMED_IAM
```

### Delete Infrastructure
```bash
# Delete backend stack
aws cloudformation delete-stack --stack-name required-remainder-backend

# Delete frontend stack
aws cloudformation delete-stack --stack-name required-remainder-frontend
```