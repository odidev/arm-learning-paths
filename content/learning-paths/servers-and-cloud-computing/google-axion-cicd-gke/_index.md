---
title: Deploy a .NET application on GKE using github action workflow

minutes_to_complete: 60   

who_is_this_for: This is an advanced topic for software developers who want to develop cloud-native applications using GitHub Actions and Google Kubernetes Engine (GKE), and run them on google Axion VMs.

learning_objectives: 
    - Configure a google Axion VM as a self-hosted GitHub runner.
    - Create a GKE cluster with Arm-based google Axion nodes using Terraform.
    - Deploy a .NET application to GKE with GitHub Actions using the self-hosted Arm64-based runner.

prerequisites:
    - A GCP account. 
    - A GitHub account.
    - A machine with [Terraform](/install-guides/terraform/),[gcloud CLI](/install-guides/gcloud.md), and [Kubectl](/install-guides/kubectl/) installed.

author: odidev

### Tags
skilllevels: Advanced
subjects: Containers and Virtualization
cloud_service_providers: Google cloud platform

armips:
    - Neoverse

tools_software_languages:
    - .NET
    - Kubernetes
    - Docker

operatingsystems:
    - Linux

further_reading:
    - resource:
        title: GKE documentation
        link: https://cloud.google.com/kubernetes-engine/docs
        type: documentation
    - resource:
        title: Google Developer connect documentation
        link: https://cloud.google.com/developer-connect/docs
        type: documentation
    - resource:
        title: Kubernetes documentation
        link:  https://kubernetes.io/docs/home/
        type: documentation



### FIXED, DO NOT MODIFY
# ================================================================================
weight: 1                       # _index.md always has weight of 1 to order correctly
layout: "learningpathall"       # All files under learning paths have this same wrapper
learning_path_main_page: "yes"  # This should be surfaced when looking for related content. Only set for _index.md of learning path content.
---
