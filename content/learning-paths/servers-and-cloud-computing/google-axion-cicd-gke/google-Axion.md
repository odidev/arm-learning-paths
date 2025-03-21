---
title: "Build and deploy a .NET application"

weight: 3

layout: "learningpathall"
---

In this Learning Path, you will build a .NET 6-based web application using a self-hosted GitHub Actions Arm64 runner. You will deploy the application in a GKE Cluster, running on Google Axion based VMs. Self-hosted runners offer increased control and flexibility in terms of infrastructure, operating systems, and tools, in comparison to GitHub-hosted runners.

{{% notice Note %}}
* GitHub-hosted Arm64 runners have now reached General Availability. If your GitHub account is part of a Team or an Enterprise Cloud plan, you can use GitHub-hosted Arm64 runners. 

* To learn how you can configure a GitHub-managed runner, see the Learning Path [*Build multi-architecture container images with GitHub Arm-hosted runners*](/learning-paths/cross-platform/github-arm-runners/).
{{% /notice %}}

## How do I create a Virtual Machine in GCP?
Creating a virtual machine based on Google Axion is no different from creating any other VM in Azure. To create a Google Axion virtual machine, launch the GCP portal and navigate to Virtual Machines. 

Select `Create Instance`, and fill in the details such as `Name`, and `Region`. 

In the `Machine Type` field, click on `C4A` series and select the `c4s-standard-2`(or based or your requirment) family of VMs.
===============================================
![azure-cobalt-vm #center](_images/azure-cobalt-vm.png)
===============================================

{{% notice Note %}}
To learn more about Arm-based VMs in GCP, refer to "Create an Arm-based VM instance with Google Axion CPU" in  [*Create an Axion instance*](/learning-paths/servers-and-cloud-computing/java-on-axion/1-create-instance/).
{{% /notice %}}

## How do I configure the GitHub repository?

The source code for the application and configuration files that you require to follow this Learning Path are hosted in this [SampleDotNetApp github repository](https://github.com/odidev/SampleDotNetApp). This repository also contains the Dockerfile and Kubernetes deployment manifests that you require to deploy the .NET 6 based application. 

Follow these steps:

* Start by forking the repository.

* Once the GitHub repository is forked, navigate to the `Settings` tab, and click on `Actions` in the left navigation pane. 

 * In `Runners`, select `New self-hosted runner`, which opens up a new page to configure the runner. 

* For `Runner image`, select `Linux`, and for `Architecture`, select `ARM64`. 

* Using the commands shown, execute them on the `c4a-standard-2` VM you created in the previous step.

* Once you have configured the runner successfully, you will see a self-hosted runner appear on the same page in GitHub.

{{% notice Note %}}
To learn more about creating an Arm-based self-hosted runner, see this Learning Path [*Use Self-Hosted Arm64-based runners in GitHub Actions for CI/CD*](/learning-paths/laptops-and-desktops/self_hosted_cicd_github/).
{{% /notice %}}

## How do I create an GKE cluster with Arm-based Axion nodes using Terraform?

You can create an Arm-based GKE cluster by following the steps in this Learning Path [*Deploy an Arm-based GKE Cluster of  Axion VMs using Terraform*](/learning-paths/servers-and-cloud-computing/gke/cluster_deploymen/). 

Make sure to update the `main.tf` file with the correct VM as shown below:

```console
`machine_type ` = `c4a-standard-2`
`disk_type` = `hyperdisk-balanced`
```
Once you have successfully created the cluster, you can proceed to the next section.

## How do I create a container registry with Azure Container Registry (ACR)?

Creating a Container Registry in Google Cloud Platform (GCP) is a straightforward process. Google Container Registry (GCR) is a private registry for storing and managing Docker container images. Here's a step-by-step guide to creating and using a container registry in GCP:
1. Enable Required APIs
	- Enable the Container Registry API:
		- Navigate to the API & Services section in the Cloud Console.
		- Click on "Enable APIs and Services"
		- Search for "Container Registry API" and enable it.

	- Enable the Google Cloud Storage API (if not already enabled):
		- Container Registry uses Google Cloud Storage to store container images, so this API must be enabled.

2. Generate a Service Account Key
	- In the Service Accounts list, click on the service account you just created.
		- Go to the "Keys" tab.
		- Click "Add Key" and select "Create New Key".
		- Choose the key type as JSON and click "Create"
	Download the Key File:
		- A JSON key file will be downloaded to your computer. This file contains the credentials for the service account.

3. Authenticate gcloud with the Service Account
	- Set the GOOGLE_APPLICATION_CREDENTIALS environment variable to point to the JSON key file:
	```console
		export GOOGLE_APPLICATION_CREDENTIALS="PATH_TO_JSON_KEY_FILE"
	```
	- Use the following command to authenticate gcloud with the service account:
	```console
	gcloud auth activate-service-account SERVICE_ACCOUNT_EMAIL --key-file=PATH_TO_JSON_KEY_FILE
	
	```
	Replace:
    SERVICE_ACCOUNT_EMAIL with the email address of the service account (e.g., my-service-account@my-project.iam.gserviceaccount.com).
	PATH_TO_JSON_KEY_FILE with the full path to the JSON key file.
4. Run the following command to verify that gcloud is authenticated with the service account:
	```console	
	gcloud auth list
	```
	You should see the service account email listed as the active account.
5. Set Your Project:
	- Set the default project for the SDK:
	```console
	gcloud config set project PROJECT_ID
	```
	Replace PROJECT_ID with your actual GCP project ID.

6. Choose a Registry Location:
	- GCR supports multiple regions. Choose a location as 'us-central1' to your deployment.

## How do I set up GitHub Secrets?

The next step allows GitHub Actions to access the google Container Registry to push application docker images and GKE to deploy application pods. 

Create the following secrets in your GitHub repository:

- Populate `GCP_PROJECT_ID` with the name of your Project ID.
- Populate `GCP_SA_KEY` with json key of a Service account you have downloaded in above steps.
- Populate `GKE_CLUSTER_NAME` with the name of your GKE cluster.
- Populate `GKE_REGION` with the name of your region.

## Deploy a .NET-based application 

.NET added support for Arm64 applications starting with version 6. Several performance enhancements have been made in later versions. The latest version that supports Arm64 targets is .NET 9. In this Learning Path, you will use the .NET 6 SDK for application development.

Follow these steps:

* In your fork of the GitHub repository, inspect the `SampleDotNetApp.csproj` file. 

* Verify that the `TargetFramework` field has `net6.0` as the value. 

The contents of the file are shown below:

```console
<Project Sdk="Microsoft.NET.Sdk.Web">

  <PropertyGroup>
    <TargetFramework>net6.0</TargetFramework>
    <Nullable>enable</Nullable>
    <ImplicitUsings>enable</ImplicitUsings>
  </PropertyGroup>

</Project>
```

You can inspect the contents of the `Dockerfile` within your repository as well. This is a multi-stage Dockerfile with the following stages: 

1. `base` stage - prepares the base environment with the `.NET 6 SDK` and exposes ports 80 and 443.

2. `build` stage - restores dependencies and builds the application.

3. `publish` stage - publishes the application making it ready for deployment.

4. `final` stage - copies the published application into the final image and sets the entry point to run the application.

```console
# Use the .NET SDK image for building
FROM mcr.microsoft.com/dotnet/sdk:6.0 AS build
WORKDIR /src

# Copy the project file and restore dependencies
COPY ["SampleDotNetApp.csproj", "./"]
RUN dotnet restore

# Copy the rest of the application code
COPY . .

# Build the application
RUN dotnet build -c Release -o /app/build

# Publish the application
RUN dotnet publish -c Release -o /app/publish

# Use the .NET runtime image for running the application
FROM mcr.microsoft.com/dotnet/aspnet:6.0 AS runtime
WORKDIR /app

# Copy the published application from the build stage
COPY --from=build /app/publish .

# Expose ports
EXPOSE 80
EXPOSE 443
EXPOSE 5000
EXPOSE 8080

# Define the entry point for the container
ENTRYPOINT ["dotnet", "SampleDotNetApp.dll"]
```
Next, navigate to the k8s folder and check the Kubernetes yaml files. The deployment.yml file defines a deployment for the application. It specifies the container image to use from gcr.io and exposes port 80 for the application. The deployment ensures that the application runs with the defined resource constraints and is accessible on the specified port.
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: githubactions-gke-demo
spec:
  selector:
    matchLabels:
      app: githubactions-gke-demo
  replicas: 3 # Adjust the number of replicas as needed
  template:
    metadata:
      labels:
        app: githubactions-gke-demo
    spec:
      containers:
      - name: githubactions-gke-demo
        image: gcr.io/<PROJECT_ID>/test:latest # Replace with your GCR image path
        resources:
          limits:
            memory: "128Mi"
            cpu: "250m"
        ports:
        - containerPort: 80
      imagePullSecrets:
      - name: gcr-json-key
      tolerations:
      - key: "kubernetes.io/arch"
        operator: "Equal"
        value: "arm64"
        effect: "NoSchedule"
```
The `service.yml` file defines a `Service` and uses `LoadBalancer` to expose the service externally on port 8080, directing traffic to the application’s container on port 80.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: githubactions-gke-demo-service
spec:
  selector:
    app: githubactions-gke-demo # Matches the app label in the Deployment
  type: LoadBalancer # Creates a GCP Load Balancer
  ports:
  - port: 8080 # External port exposed by the Load Balancer
    targetPort: 80 # Internal port the container is listening on
```

Finally, have a look at the GitHub Actions file located at `.github/workflows/deploytoAKS.yml` 

```yaml
name: Deploy .NET app to GCP

on:
  workflow_dispatch:
  push:

jobs:
  deploy:
    name: Deploy application to GKE
    runs-on: self-hosted     # Use GCP-hosted runner or self-hosted if preferred

    steps:
      - name: Checkout repo
        uses: actions/checkout@v3

      - name: Set up Google Cloud SDK
        uses: google-github-actions/setup-gcloud@v1
        with:
          project_id: ${{ secrets.GCP_PROJECT_ID }}
          service_account_key: ${{ secrets.GCP_SA_KEY }}
          export_default_credentials: true

      - name: Build Docker image
        run: docker buildx build --platform linux/arm64 -t githubactions-gke-demo:'${{ github.sha }}' .

      - name: Tag and push Docker image to GCR
        run: |
          docker tag githubactions-gke-demo:'${{ github.sha }}' gcr.io/${{ secrets.GCP_PROJECT_ID }}/githubactions-gke-demo:'${{ github.sha }}'
          docker push gcr.io/${{ secrets.GCP_PROJECT_ID }}/githubactions-gke-demo:'${{ github.sha }}'

      - name: Get GKE credentials
        run: |
          gcloud container clusters get-credentials ${{ secrets.GKE_CLUSTER_NAME }} \
            --region ${{ secrets.GKE_REGION }} \
            --project ${{ secrets.GCP_PROJECT_ID }}

      - name: Deploy application to GKE
        run: kubectl apply -f k8s/deployment.yml -f k8s/service.yml
```

This GitHub Actions yaml file defines a workflow to deploy a .NET application to Google Kubernetes Engine (GKE). This workflow runs on the self-hosted GitHub Actions runner that you configured in a previous step. This workflow can be triggered manually, or on a push to the repository. 

It has the following main steps:

1. `Checkout repo` - checks out the repository code.
2. `Build image` - builds a Docker image of the application.
3. `Tag and push image` - tags and pushes the Docker image to Azure Container Registry.
4. `Get GKE credentials` - retrieves Google Kubernetes Cluster credentials.
7. `Deploy application` - deploys the application to GKE using specified Kubernetes manifests.

## How do I run the CI/CD pipeline?

The next step is to trigger the pipeline manually by navigating to `Actions` tab in the GitHub repository. Select `Deploy .NET app`, and click on `Run Workflow`. You can also execute the pipeline by making a commit to the repository. Once the pipeline executes successfully, you will see the Actions output in a format similar to what is shown below:

+==========================================

You can check your kubernetes cluster and see new application pods deployed on the cluster as shown below:





