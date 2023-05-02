---
# User change
title: "deploy ECS containers on AWS Graviton processor"

weight: 2 # 1 is first, 2 is second, etc.

# Do not modify these elements
layout: "learningpathall"
---

## Prerequisites

* An AWS account
* A computer with Node installed
* A computer with Docker installed

## Deploy ECS containers on AWS Graviton processor
Amazon ECS is a fully managed container orchestration service that makes it easy for you to deploy, manage, and scale containerized applications.

## Create an IAM User and assign permissions
Login to your AWS account as a root user and search for IAM.

![image](https://user-images.githubusercontent.com/87687468/235642307-130785da-6ddd-4eb4-afc3-382d441c1c9d.png)

From the IAM dashboard select `Users` from the left menu and click on `Add user` from the top of the page.

![user](https://user-images.githubusercontent.com/87687468/235642557-f2db9563-7c4f-4882-a40a-d81a3c84b9d3.png)

On the `Add user` screen enter a username and click on `next`

![image](https://user-images.githubusercontent.com/87687468/235643519-a0463ba5-d347-4e3d-905e-0f8790008954.png)
