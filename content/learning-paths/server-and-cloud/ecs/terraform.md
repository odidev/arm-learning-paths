---
# User change
title: "Deploy ECS containers on AWS Graviton processor using Terraform"

weight: 2 # 1 is first, 2 is second, etc.

# Do not modify these elements
layout: "learningpathall"
---


## Deploy a Dockerised Application on AWS ECS With Terraform

Another way to deploy Dockerised Application on AWS ECS is with Terraform. You can follow the steps here to automate your ECS deployment.

## Before you begin

You should have the prerequisite tools installed before starting the Learning Path. 

Any computer which has the required tools installed can be used for this section. The computer can be your desktop or laptop computer or a virtual machine with the required tools. 

You will need an [AWS account](https://portal.aws.amazon.com/billing/signup?nc2=h_ct&src=default&redirect_url=https%3A%2F%2Faws.amazon.com%2Fregistration-confirmation#/start) to complete this Learning Path. Create an account if you don't have one.

## Create an IAM User and assign permissions
To provision the infrastructure running on AWS ECS, you need an Identity and Access Management (IAM) user account. IAM enables you to manage access to AWS resources securely. You can manage who is authenticated (signed in) and permitted (has permissions) to use resources using IAM. To create an IAm user and assign permission follow [this](/learning-paths/server-and-cloud/ecs/deployment.md#create-an-iam-user-and-assign-permissions)

Use below command to configure AWS CLI

```console
aws configure
```
It will ask us for the credentials which we have saved while creating the IAM user. Use those credentials to authenticate.

## Creat an Elastic Container Registry (ECR) on AWS ECS

ECR is an AWS service for sharing and deploying container applications. This service offers a fully managed container registry that makes the process of storing, managing, sharing, and deploying your containers easier and faster. To set up an ECR, create a `main.tf` file inside your working directory and put below code in it:

```console
# main.tf
terraform {
  required_providers {
    aws = {
      source = "hashicorp/aws"
      version = "4.45.0"
    }
  }
}
resource "aws_ecr_repository" "app_ecr_repo" {
  name = "my-app-terraform"
}
```

Start by running the `terraform init` command. This should be the first command executed after creating a new Terraform configuration. It creates the Terraform configuration files in your working directory.

```console
terraform init
```
The message `Terraform has been successfully initialized!` should be displayed on your terminal, as shown:

![image](https://github.com/akhandpuresoftware/arm-learning-paths/assets/87687468/d54fc2cd-c2fd-40b7-93f1-c7483591f8e9)

To create an ECR, run the `plan` command, you’ll be able to preview the above Terraform configuration file and the resource that will be created:

```console
terraform plan
```
Terraform plan will let you see the resource that will be added, changed, or deployed to AWS. In this case, one resource, aws_ecr_repository.app_ecr_repo, will be added to AWS.

![image](https://github.com/akhandpuresoftware/arm-learning-paths/assets/87687468/d12e7aa0-364e-4b46-b2d5-f9e96e370c1c)

To provision the displayed configuration infrastructure on AWS, apply the above execution plan:

```console
terraform apply
```
enter yes when prompted to allow Terraform to execute this command as expected.

![image](https://github.com/akhandpuresoftware/arm-learning-paths/assets/87687468/3728a859-cc9c-419a-b0d9-3e707e6e986c)

Terraform will create the ECR. You can confirm this on your Amazon Elastic Container Registry Repositories list.

![image](https://github.com/akhandpuresoftware/arm-learning-paths/assets/87687468/208951cd-140c-4a4b-9ea1-70ca1c14b332)

Now that you have an ECR repository ready, it’s time to create the docker image and upload it to ECR

