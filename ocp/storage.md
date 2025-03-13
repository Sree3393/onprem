---
title: Storage
layout: default
parent: Requirements
---

# Storage Requirements  

For **on-premise installations** of OpenShift and IBM Watsonx.ai, selecting the right **storage solution** is crucial for performance, reliability, and scalability.  

## **Primary Storage Requirement**  
- **Network File System (NFS)** – The preferred storage type, provided by the customer, for **persistent storage** and shared data access in on-premise deployments.  
- **Storage Types Used** – Throughout the installation, we will be utilizing both **file-based** and **block-based** storage solutions provided by **NFS** to accommodate different workload needs.  

## **Local Disk Requirements**  
- **SSD or NVMe** – Local disks must be either **Solid State Drives (SSD)** or **Non-Volatile Memory Express (NVMe)** to ensure **high-speed data access, lower latency, and improved I/O performance** for AI/ML workloads.  

By leveraging **NFS for shared storage** and **high-speed local disks**, we ensure a **stable, efficient, and high-performance** on-premise environment for **Cloud Pak for Data (CP4D) and Watsonx.ai**. 
