---
title: Installation
layout: default
parent: OCP
nav_order: 3
---

# **OpenShift Installation Process**  

## **Overview**  
The OpenShift installation process involves multiple stages, starting from creating configuration files to bootstrapping and cluster deployment. The **Operators** play a critical role in managing and automating OpenShift components during and after installation. Operators ensure seamless updates, scaling, monitoring, and configuration of cluster services.

## **1. Creating install-config.yaml**  
The installation process begins by generating the `install-config.yaml` file, which defines:  
- Cluster networking details  
- Compute and control plane configurations  
- Storage requirements  
- Platform-specific settings (e.g., AWS, Bare Metal, VMware)  

This file serves as the primary configuration source for generating manifests and ignition files.  

## **2. Bootstrapping**  
During the bootstrapping phase:  
- A temporary bootstrap node is deployed to **provision the master nodes**.  
- The bootstrap node runs essential components, including the **Cluster Version Operator (CVO)**, which applies initial Kubernetes resources.  
- Once the control plane is fully configured, the bootstrap node is **removed from the cluster**.  

## **3. Generating Manifests and Ignition Files**  
The OpenShift installer uses `install-config.yaml` to create:  
- **Manifests** – YAML files that define **Operators**, cluster policies, and infrastructure settings.  
- **Ignition Files** – Configuration files required to provision **Bootstrap, Master, and Worker** nodes.  

Ignition files have a **validity of 24 hours** from the time of creation, meaning they must be used immediately for deployment.  

## **4. Copying Ignition and RHCOS Images to HTTPD**  
After generating the ignition files, they must be copied to a publicly accessible location. In our setup, we use an **HTTPD server** to store and serve these files.  



Additionally, the **RHCOS image** required for node provisioning is placed in the same directory:  


The HTTPD server ensures that cluster nodes can retrieve the necessary files during deployment.  

## **5. Deploying the OpenShift Cluster**  
Once all configurations are in place, the cluster deployment can begin. The installer provisions nodes, applies manifests, and configures **Operators** to maintain cluster health and performance.  

This structured approach ensures a **smooth, automated, and repeatable** OpenShift deployment. 
