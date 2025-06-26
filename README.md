# Required Remainder Infra (Backend Infrastructure)

This repository contains the CloudFormation infrastructure for deploying the **backend** of the Required Remainder project using **AWS ECS Fargate**, **VPC**, **ECR**, and **an internal Application Load Balancer** for enhanced security.

---


## 🧱 Infrastructure Overview

- **VPC** with 2 private subnets
- **ECR** repository for backend container images
- **ECS Fargate Cluster** to run the Spring Boot service
- **Internal ALB** (not accessible from the public internet)
- **CloudFront + S3** static hosting for Angular frontend
- **IAM Roles** for ECS execution

---

## 🚀 How to Deploy

Make sure you have the AWS CLI configured and permissions to deploy CloudFormation stacks.


### 📌 1. Manually (from local)

```bash
aws cloudformation deploy \
  --template-file cloudformation/backend.yaml \
  --stack-name required-remainder-backend \
  --capabilities CAPABILITY_NAMED_IAM \
  --parameter-overrides \
    ContainerImage=<your-docker-image-uri>

    aws cloudformation deploy \
  --template-file cloudformation/frontend.yaml \
  --stack-name required-remainder-frontend \
  --capabilities CAPABILITY_NAMED_IAM
```


### 🤖 2. Automatically (GitHub Actions)
The file .github/workflows/deploy.yml allows you to deploy this infrastructure using GitHub Actions on demand.

Trigger it manually via the "Actions" tab > "Deploy Infra" > Run workflow.


## 🔐 Required GitHub Secrets
In each repository (required_remainder_fe, required_remainder_be, required_remainder_infra), set the following secrets:

|Name	| How to obtain it
--------|--------
AWS_ACCESS_KEY_ID	| From AWS IAM > Users > Security credentials (recommended: scoped user for CI/CD)
AWS_SECRET_ACCESS_KEY	| From AWS IAM > Users > Security credentials
CLOUDFRONT_DISTRIBUTION_ID	| (Only in frontend repo) Run aws cloudformation describe-stacks or view outputs
