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

### Attaching the ECS access policy
ECS requires permissions for many services such as listing roles and creating clusters in addition to permissions that are explicitly ECS. The best way to add all of these permissions to our new IAM user is to use an Amazon managed policy to grant access to the new user.
Select `Attach existing policies directly` under Set permissions and search for `AmazonECS_FullAccess`. Select checkbox next to the policy.

![image](https://user-images.githubusercontent.com/87687468/235838644-9e96ae76-6f10-4164-818a-aa9f40fd36ef.png)

Select `Next` to review our work and create the user.

![image](https://user-images.githubusercontent.com/87687468/235839326-cba80b0d-50e0-48b3-8584-1593240d4792.png)

A new user will get created on **IAM>>User** page.

## Create a Fargate Cluster
Search for `Elastic Container Service` and select Elastic Container Service. From the left menu select `Clusters` and then select `Create cluster`.

![image](https://user-images.githubusercontent.com/87687468/235840042-e7461d64-1c1f-4a61-b930-fb1c24d36281.png)

Name the cluster and the rest we can leave as is. Select `Create`.

![image](https://user-images.githubusercontent.com/87687468/235840668-d13d607d-b546-4d5f-bf95-00b0f57e8322.png)

A cluster will be created as below.

![image](https://user-images.githubusercontent.com/87687468/235840972-51355567-ac19-476d-b969-7c010cb41688.png)

## Create an ECS Task
The ECS Task is the action that takes our image and deploys it to a container. To create an ECS Task lets go back to the ECS page and do the following:
* Select `Task Definitions` from the left menu. Then select `Create new Task Definition`.

![image](https://user-images.githubusercontent.com/87687468/235845002-667547ac-5cb4-4dfb-b81c-0c379bd45745.png)

Now, Enter the name of the `Task definition family` in  `Task definition configuration`. Also Enter the name of your first container and name of Docker image which you want to use. Here we are using `nginx` docker image to Deploy docker container. Leave everything as it is and click on `Next`.

![image](https://user-images.githubusercontent.com/87687468/235846324-c9d15fec-ba3b-47b9-8f3c-5b72761c1903.png)

**NOTE:** Here we are not mapping any other port as Nginx runs on port 80 by default.

Under Environment Section, select `Operating system/Architecture` as  `Linux/ARM64` and leave everything else set to its default value and click `Next` in the lower Right corner of the dialog.

![image](https://user-images.githubusercontent.com/87687468/235848013-599bfcbe-27a1-4a47-a7ab-2914081b9b2d.png)

Review everything and click on `create`. 

Go back to the ECS page, select Task Definitions and we should see our new task with a status of ACTIVE.

![image](https://user-images.githubusercontent.com/87687468/235849100-2865c98a-77fd-45f9-8d49-5c9acac0f5e9.png)

Now select the task in the Task definition list and click `Deploy` and select `Run Task`

![image](https://user-images.githubusercontent.com/87687468/235880090-aad4cd44-51fd-4e2d-aaf4-d4450db656e5.png)

Now select your cluster from drop down menu of Existing cluster. Select a vpc from the list. If you are building a custom app this should be the vpc assigned to any other AWS services you will need to access from your instance. For our app, any will do. Add at least one subnet. Auto-assign public IP should be set to ENBABLED and click on `create`.

![1](https://user-images.githubusercontent.com/87687468/235882089-9d7064d5-d2e2-44f6-99fa-1a94947ca246.JPG)
![image](https://user-images.githubusercontent.com/87687468/235881999-c9e34017-1b72-4d81-adf1-f57edd57e434.png)




