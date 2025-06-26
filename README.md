# Required Remainder Infrastructure

This repository contains the AWS CloudFormation infrastructure for deploying the complete Required Remainder application stack. It provides a production-ready, scalable, and secure infrastructure using modern AWS services.

## 🏗️ Architecture Overview

### Backend Infrastructure
- **VPC** with public subnets across 2 AZs
- **Elastic Beanstalk Environment** for running Spring Boot containers
- **ECR Repository** for storing Docker images
- **Application Load Balancer** (managed by Elastic Beanstalk)
- **IAM Roles** with least-privilege permissions
- **Security Groups** with proper network isolation
- **Auto Scaling Group** for scalability

### Frontend Infrastructure
- **S3 Bucket** for static file hosting
- **CloudFront Distribution** for global CDN and HTTPS
- **IAM Roles** for S3 and CloudFront access

### Shared Infrastructure
- **VPC** with proper subnet configuration
- **Internet Gateway** for connectivity
- **Security Groups** with appropriate rules

## 🚀 Current Status

### Deployed Resources
- ✅ **Backend**: Elastic Beanstalk environment with Spring Boot application
- ✅ **Frontend**: S3 bucket with CloudFront distribution
- ✅ **CI/CD**: GitHub Actions workflows for both frontend and backend
- ✅ **Monitoring**: CloudWatch logs and health checks

### Application URLs
- **Backend API**: `http://required-remainder-backend-env.eba-e2e4ckrc.us-east-1.elasticbeanstalk.com`
- **Frontend**: Available through CloudFront distribution

## 🚀 Deployment Options

### GitHub Actions (Recommended)

The infrastructure is deployed automatically via GitHub Actions workflows:

1. **Backend Deployment**: Triggered on push to main branch in `required_remainder_be`
2. **Frontend Deployment**: Triggered on push to main branch in `required_remainder_fe`

### Manual Deployment

#### Prerequisites
- AWS CLI configured with appropriate permissions
- AWS credentials with CloudFormation, Elastic Beanstalk, ECR, S3, CloudFront permissions

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
| `AWSElasticBeanstalkFullAccess` | AWS managed | Full access to Elastic Beanstalk |
| `AmazonS3FullAccess` | AWS managed | Full access to S3 |
| `AWSCloudFormationFullAccess` | AWS managed | Full access to CloudFormation |
| `CloudFrontFullAccess` | AWS managed | Full access to CloudFront |
| `CloudWatchLogsFullAccess` | AWS managed | Full access to CloudWatch Logs |
| `IAMFullAccess` | AWS managed | Full access to IAM (for creating service roles) |

## 📋 CloudFormation Templates

### Backend Template (`cloudformation/backend.yaml`)

**Resources Created:**
- VPC with public subnets
- Internet Gateway
- Elastic Beanstalk Application and Environment
- ECR Repository
- Application Load Balancer (managed by EB)
- Auto Scaling Group
- Security Groups
- IAM Roles and Instance Profile

**Parameters:**
- `Environment`: Environment name (dev, staging, prod)
- `ContainerImage`: Docker image URI (only needed when DeployService=true)
- `ECRRepositoryName`: ECR repository name
- `DeployService`: Whether to create EB environment (true/false)
- `InstanceType`: EC2 instance type (default: t3.small)
- `MinSize`: Minimum number of instances (default: 1)
- `MaxSize`: Maximum number of instances (default: 2)

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
- `ElasticBeanstalkApplicationName`: EB application name
- `ElasticBeanstalkEnvironmentURL`: EB environment URL
- `ElasticBeanstalkEnvironmentCNAME`: EB environment CNAME
- `VPCId`: VPC ID

### Frontend Stack Outputs
- `S3BucketName`: S3 bucket name
- `CloudFrontDistributionId`: CloudFront distribution ID
- `CloudFrontDomainName`: CloudFront domain name

## 🔄 Update Infrastructure
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

## 🗑️ Delete Infrastructure
```bash
# Delete backend stack
aws cloudformation delete-stack --stack-name required-remainder-backend

# Delete frontend stack
aws cloudformation delete-stack --stack-name required-remainder-frontend
```

## 📊 Monitoring and Logging

### CloudWatch Logs
- **Backend**: `/aws/elasticbeanstalk/required-remainder-backend-env/var/log/eb-docker/containers/eb-current-app/`
- **Frontend**: Access logs via CloudFront

### Health Checks
- **Backend**: Elastic Beanstalk health checks on `/actuator/health`
- **Frontend**: CloudFront distribution health

## 🔧 Maintenance

### Scaling
- Backend automatically scales based on CPU and memory usage
- Frontend scales globally via CloudFront

### Updates
- Backend updates via GitHub Actions (new Docker image)
- Frontend updates via GitHub Actions (new S3 content)

### Security
- Regular security updates via AWS managed services
- IAM roles with least privilege access
- Security groups with minimal required access

## 📄 License

This project is part of the Required Remainder application suite.