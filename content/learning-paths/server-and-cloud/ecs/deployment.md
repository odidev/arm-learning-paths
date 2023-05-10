---
# User change
title: "Deploy ECS containers on AWS Graviton processor"

weight: 2 # 1 is first, 2 is second, etc.

# Do not modify these elements
layout: "learningpathall"
---

## Before you begin

You should have the prerequisite tools installed before starting the Learning Path. 

Any computer which has the required tools installed can be used for this section. The computer can be your desktop or laptop computer or a virtual machine with the required tools. 

You will need an [AWS account](https://portal.aws.amazon.com/billing/signup?nc2=h_ct&src=default&redirect_url=https%3A%2F%2Faws.amazon.com%2Fregistration-confirmation#/start) to complete this Learning Path. Create an account if you don't have one.

## Deploy ECS containers on AWS Graviton processor

Amazon ECS is a fully managed container orchestration service that makes it easy for you to deploy, manage, and scale containerized applications.

## Create an IAM User and assign permissions

Login to your AWS account as a root user and search for IAM.

![image](https://user-images.githubusercontent.com/87687468/235642307-130785da-6ddd-4eb4-afc3-382d441c1c9d.png)

From the IAM dashboard select `Users` from the left menu and click on `Add user` from the top of the page.

![user](https://user-images.githubusercontent.com/87687468/235642557-f2db9563-7c4f-4882-a40a-d81a3c84b9d3.png)

On the `Add user` screen enter a username and select the check box before `Provide user access to the AWS Management Console`. Then select `I want to create an IAM user` and click on `next`

![image](https://user-images.githubusercontent.com/87687468/236792673-6e6f4690-f06e-45b3-b87d-243872ddc3a6.png)

## Create an ECR policy

We will need to have access to ECR to store our images but there is no Amazon managed policy option. We must create a new policy to attach to our IAM user.
To do so, select `Create policy`.

![permission](https://user-images.githubusercontent.com/87687468/237015604-85e79e95-20c8-42b4-a489-f8453693c6ce.png)

Under `Service`, select `Elastic Container Registry`. Now select `All Elastic Container Registry actions (ecr:*)` under `Actions allowed`. 

![policy](https://user-images.githubusercontent.com/87687468/237007344-ef0af46f-d96c-49ed-96e2-9cae5415cc95.png)

Under `Resources`, select `specific` and `Add ARN`. Here we will select the `region` and select `Any` for Repository name under `This account` and click on `Add ARNs`.

![image](https://user-images.githubusercontent.com/87687468/237008844-0f3268be-3941-4e74-9c39-9ed521ee43ac.png)

Skip the tags by clicking `Next`. Fill in an appropriate policy name. We will use `ECR_FullAccess` and select `Create policy`.

## Attaching the access policy

ECS requires permissions for many services such as listing roles and creating clusters in addition to permissions that are explicitly ECS. The best way to add all of these permissions to our new IAM user is to use an Amazon managed policy to grant access to the new user.

Select `Attach existing policies directly` under `Set permissions` and search for `AmazonECS_FullAccess` & `ECR_FullAccess`. Select checkbox next to the policies.

![permission1](https://github.com/akhandpuresoftware/arm-learning-paths/assets/87687468/fb69eced-d5be-413f-b550-bef713cad2cc)

Select `Next` to review our work and create the user.

![image](https://user-images.githubusercontent.com/87687468/237018931-b11edaa3-a78e-40e1-9680-b87cdca27a3e.png)

When you submit this page you will get a confirmation screen. Save all of the information there in safe place we will need all of it when we deploy our container.
A new user will get created on **IAM>>User** page. Click on the user and goto `Security credentials` section. Click on `create access key`

![image](https://user-images.githubusercontent.com/87687468/236796346-390f5193-b5cf-4132-a18d-37ea23eba5a9.png)
![image](https://user-images.githubusercontent.com/87687468/236796580-521971ca-d3ad-4ce6-a5c4-47aa59d62427.png)

Select `Command Line Interface (CLI)` and click on `Next`

![image](https://user-images.githubusercontent.com/87687468/236796940-8a5dcb6a-2008-49c2-a117-72379df22f9d.png)

Add description and create access key.

![image](https://user-images.githubusercontent.com/87687468/236797205-a6a795af-6988-41ed-96da-e2da63bd0a4a.png)

Save `Access key` and `Secret access key` somewhere safe, we will need the same while configuring AWS CLI. 

## Create an ECR registry

In this step we are going to create the repository in ECR to store our image. We will need the ARN (Amazon Resource Name — a unique identifier for all AWS resources) of this repository to properly tag and upload our image.
First login to the AWS console with the `test_user` credentials we created earlier. Amazon will ask for your account id, username, and password.

![image](https://user-images.githubusercontent.com/87687468/236799998-55f90ae7-cb65-49c8-8185-17b66ee70257.png)

It will ask to change your default password, change the same as per your choice.

![image](https://user-images.githubusercontent.com/87687468/236800802-72fbc1ad-63c4-480f-9392-d27ab2721d26.png)

Once you are in, search for Elastic Container Registry and select it.

![image](https://user-images.githubusercontent.com/87687468/236801302-7ea5a6ff-09ff-4a35-81d6-576880e240bd.png)

From there fill in the name of the repository as myapp and leave everything else default.

![image](https://user-images.githubusercontent.com/87687468/236801796-a6558808-f20a-4422-8ce5-d4b5544d58f2.png)

Select `Create Repository` in the lower right of the page and your repository will be created. You will see your repository in the repository list, and most importantly the ARN(here called a URI) which we will need to push up our image. Copy the URI for the next step.

![image](https://user-images.githubusercontent.com/87687468/236802115-216ecf39-0a2b-47b5-be78-030f0ee23c68.png)

## Create the Docker image

For the purpose of this demo we are going to use a simple Nginx app. we can either pull the image from Dockerhub or build the same from source files. Since the image is available on Dockerhub, we can directly pull the same on Linux/Arm64 paltform using below command. 

```console
docker pull nginx
```
Now In order for ECR to know which repository we are pushing our image to we must tag the image with that URI.
```console
docker tag nginx [use your uri here]
```
The full command for our ECR registry will look like:
```console
docker tag nginx 173141235168.dkr.ecr.us-east-2.amazonaws.com/myapp
```
## Give the Docker CLI permission to access your Amazon account

Use below command to to configure AWS CLI
```console
aws configure
```
It will ask us for our credentials which we have saved while creating the IAM user. Use those credentials to authenticate.
Next, we need to generate a ECR login token for docker. This step is best combined with the following step but its good to take a deeper look to see what is going on. When you run the followign command it generated a token. Docker needs that token to push to your repository.
```console
aws ecr get-login-password --region us-east-2
```
We can pipe that token straight into Docker like this. Make sure to replace [your account number] with your account number. The ARN at the end is the same as the one we used earlier without the name of the repository at the end.
```console
aws ecr get-login-password --region us-east-2 | docker login --username AWS --password-stdin [your account number].dkr.ecr.us-east-1.amazonaws.com
```
If all goes well, the response will be Login Succeeded.

## Upload your docker image to ECR

Use below command to push the image to the ECR repository.
```console
docker push [your account number].dkr.ecr.us-east-1.amazonaws.com/myapp
```

## Create a Fargate Cluster

Search for `Elastic Container Service` and select `Elastic Container Service`. From the left menu select `Clusters` and then select `Create cluster`.

![image](https://user-images.githubusercontent.com/87687468/235840042-e7461d64-1c1f-4a61-b930-fb1c24d36281.png)

Name the cluster and the rest we can leave as it is. Select `Create`.

![image](https://user-images.githubusercontent.com/87687468/235840668-d13d607d-b546-4d5f-bf95-00b0f57e8322.png)

A cluster will be created as below.

![image](https://user-images.githubusercontent.com/87687468/235840972-51355567-ac19-476d-b969-7c010cb41688.png)

## Create an ECS Task

The ECS Task is the action that takes our image and deploys it to a container. To create an ECS Task do the following:
* Select `Task Definitions` from the left menu. Then select `Create new Task Definition`.

![image](https://user-images.githubusercontent.com/87687468/235845002-667547ac-5cb4-4dfb-b81c-0c379bd45745.png)

Now, Enter the name of the `Task definition family` in  `Task definition configuration`. Also Enter the name of your container and ARN of our image in the Image box. You can copy this from the ECR dashboard if you haven’t already. Leave everything as it is and click on `Next`.

![image](https://user-images.githubusercontent.com/87687468/237050951-2a4f3195-24ce-4503-beca-6ce424626b2b.png)

**NOTE:** Here we are not mapping any other port as Nginx runs on port 80 by default.

Under Environment Section, select `Operating system/Architecture` as  `Linux/ARM64` and leave everything else set to its default value and click `Next` in the lower Right corner of the dialog.

![image](https://user-images.githubusercontent.com/87687468/235848013-599bfcbe-27a1-4a47-a7ab-2914081b9b2d.png)

Review everything and click on `create`. 

Go to the ECS page, select Task Definitions and we should see our new task with a status of ACTIVE.

![image](https://user-images.githubusercontent.com/87687468/235849100-2865c98a-77fd-45f9-8d49-5c9acac0f5e9.png)

Now select the task in the Task definition list and click `Deploy` and select `Run Task`

![image](https://user-images.githubusercontent.com/87687468/235880090-aad4cd44-51fd-4e2d-aaf4-d4450db656e5.png)

Now select your cluster from drop down menu of `Existing cluster`. In Networking section, select a vpc from the list. If you are building a custom app this should be the vpc assigned to any other AWS services you will need to access from your instance. For our app, any will do. Add at least one subnet.
Edit the security group. Because `Nginx` runs on port 80 by default, and we opened port 80 on our container, we also need to open port 80 in the security group. Select `Create a new security group` and enter the Security group name and security group desciption and add a Custom TCP inbound rule that opens port 80.
Auto-assign public IP should be set to ENBABLED and click on `create`.

![1](https://user-images.githubusercontent.com/87687468/235882089-9d7064d5-d2e2-44f6-99fa-1a94947ca246.JPG)
![image](https://user-images.githubusercontent.com/87687468/236178142-dd2d264d-4f5f-44aa-90c9-87f9601acac4.png)

And finally, run the task by clicking `Create` in the lower Right corner of the page.

## Check to see if our app is running

After you run the Task, you will be forwarded to the fargate-cluster page. When the Last Status for your cluster changes to RUNNING, your app is up and running. You may have to refresh the table a couple of times before the status is RUNNING. This can take a few minutes.

![image](https://user-images.githubusercontent.com/87687468/236180290-963d6e6b-a67c-4a74-a102-20f8faa871f5.png)

Click on the link in the Task column and find the Public IP address in the `Configuration` section of the Task page.

![image](https://user-images.githubusercontent.com/87687468/236181529-38d2bb22-59d6-4cd5-a7bc-123fcbe39917.png)

Enter the public IP address followed by :Port(In our case :80) in your browser to see your app in action.

![image](https://user-images.githubusercontent.com/87687468/236188907-5953f69d-98c2-4def-b5b2-b6b71186af19.png)

## Shut down the app

When you are done, you’ll want to shut down your app to avoid charges. From the ECS page select Clusters from the left menu, and select your cluster from the list of clusters.

![image](https://user-images.githubusercontent.com/87687468/236189556-7516dd61-f9fd-4807-96c3-a9a47d08c9b2.png)

From the table at the bottom of the page select `tasks`. Check the box next to the running task and select `stop` from the dropdown menu at the top of the table

![image](https://user-images.githubusercontent.com/87687468/236190146-48ec2000-50dc-4772-b4c4-f440577b50b4.png)
