---
title: Operator Installations
layout: default
parent: Installation
nav_order: 4
---

# **Operator Installations**  

To ensure a successful installation of required OpenShift operators, it is crucial to **apply the `imagecontentsource.yaml` and `catalogsources.yaml`** after pushing the offline mirror images obtained in the previous steps. This ensures that OpenShift can pull the necessary images from the mirror registry.  

## **1. NFD Operator (Node Feature Discovery)**  
The **NFD (Node Feature Discovery) Operator** helps detect and advertise node hardware features, such as CPU, memory, and PCI devices. It is essential for scheduling workloads that require specific node capabilities, ensuring optimal resource utilization in the OpenShift cluster.  

## **2. NVIDIA GPU Operator**  
The **NVIDIA GPU Operator** automates the deployment and management of **NVIDIA drivers, GPU device plugins, and monitoring tools** in OpenShift. It enables seamless GPU acceleration for AI/ML workloads by providing the necessary components to expose GPUs to containers efficiently.  

## **3. OpenShift AI Operator**  
The **OpenShift AI Operator** (formerly known as Open Data Hub) simplifies the deployment of AI/ML workloads by integrating popular frameworks like **TensorFlow, PyTorch, and JupyterHub**. It provides a **scalable, cloud-native** environment for building and running machine learning models within OpenShift.  
