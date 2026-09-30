# Binary Calculator Application: System Design Document

+ Github link: https://github.com/CrisH2307/SOFE3980U-Lab3-Part2
+ +Video link: 
+ https://drive.google.com/file/d/1_2R6iO79vrIvznP7euyep--EdyzKvSGa/view?usp=drive_link
+ https://drive.google.com/file/d/1Iv6xqqf-SCfmBnE9LJ5EqgVhppWJh6oj/view?usp=drive_link

## Discussion
### Understanding Jenkins Terminology

#### 1. Core Concepts Overview
When working with Jenkins, understanding the basic building blocks makes it much easier to write, debug, and scale your automation workflows. Here is a breakdown of what the most common terms mean.

#### 2. Key Terms Explained
* **Pipeline:** The overarching blueprint or script that defines your entire continuous integration and deployment process, from code checkout to production rollout.
* **Node:** A physical or virtual machine configured to join the Jenkins environment and host workloads.
* **Agent:** The directive that tells Jenkins where and how to run your pipeline. It often points to a specific node or spins up an isolated container to keep the build environment clean.
* **Stage:** A distinct, logical phase within your workflow. Stages help you organize progress visually, separating steps like building, testing, and deploying.
* **Steps:** The individual, actionable tasks executed sequentially inside a stage. For example, running a shell command is an individual step.

#### 3. How They Fit Together
To picture how these components work in harmony: a **Pipeline** contains **Stages**, which contain **Steps**, and the whole process runs on an **Agent** hosted by a **Node**.

## 1. Overview
This document outlines the architecture for deploying the Binary Calculator application using a modern cloud-native stack. The primary goal is to automate the deployment process from a Jenkins pipeline directly into a Google Kubernetes Engine cluster, making the app accessible to users via a public load balancer.

## 2. Architecture Components
The deployment relies on three main layers:
* **Jenkins CI/CD Server:** Orchestrates the deployment steps. It executes tasks inside a containerized environment to keep resources isolated and clean.
* **Google Kubernetes Engine (GKE):** Acts as our managed container orchestration platform, handling node management and cluster availability.
* **Kubernetes Resources:** 
  * **Deployment:** Manages the binary calculator application pods and ensures the app stays running.
  * **Service (LoadBalancer):** Exposes the application to the outside world by requesting a cloud load balancer with a public IP.

## 3. Pipeline Workflow
1. **Authentication:** Jenkins logs into Google Cloud using a secure service account key.
2. **Context Setup:** The pipeline selects the correct GCP project and retrieves the cluster credentials for `kubectl`.
3. **Rollout:** The deployment and service are created inside the cluster automatically.

---

# Binary Calculator Deployment Report

## 1. Executive Summary
The deployment of the Binary Calculator application to our Google Kubernetes Engine cluster finished successfully. The Jenkins pipeline automated the entire rollout, from cloud authentication down to service exposure. This report reviews the outcome, explains a minor timing detail regarding the public IP address, and suggests a few practical improvements for the future.

## 2. Deployment Results
The pipeline executed smoothly and the `binarycalculator-service` was created without issues. 

You might have noticed that the initial log output did not show a public IP address. This happens because cloud load balancers take a minute or two to provision behind the scenes. When Jenkins ran the lookup command immediately, the IP had not quite arrived yet. Adding a short wait loop in the pipeline resolves this nicely by letting Jenkins check back every few seconds until the address is ready.

## 3. Key Insights and Next Steps
* **Reliable Automation:** Running the pipeline through containerized agents keeps everything consistent and repeatable.
* **Asynchronous Networking:** Always account for a slight delay when provisioning LoadBalancer services in cloud environments. 
* **Next Steps:** We should look into adding automated health checks, setting up a custom domain name, and securing the traffic with an Ingress controller and SSL certificates.