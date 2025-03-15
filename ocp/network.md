---
title: Network
layout: default
parent: Requirements
---

# **Network Requirements**  

A well-designed network infrastructure is crucial for the **performance, scalability, and reliability** of an OpenShift deployment. It facilitates efficient **cluster communication, workload traffic, and management operations**.  

---

## **Recommended Network Bandwidth**  

- **Minimum:** **1 GbE (Gigabit Ethernet)** for small-scale deployments.  
- **Preferred:** **10 GbE or higher** for better throughput and lower latency, especially for larger environments with high container workloads and storage traffic.  
- **Bare Metal Deployments:** **25 Gbps networking** is recommended for optimal cluster-wide communication, ensuring seamless operation of:  
  - **Core OpenShift services**  
  - **Containerized workloads**  
  - **Storage services**  
  - **East-west traffic** (inter-container communication)  

---

## **Logical Network Segmentation**  

To ensure **efficient traffic management and security**, OpenShift deployments categorize network traffic into three distinct **logical networks**:  

### **1. External Network**  
- Handles communication **outside the cluster**.  
- Used for:  
  - OpenShift **Control Plane API** (kubectl, oc CLI)  
  - OpenShift **web interface** (console access)  
  - **Exposed applications** via OpenShift **routes** and **services**  

### **2. Internal Network**  
- **Primary non-routable network** for **cluster management** and **inter-node communication**.  
- Used for:  
  - **Cluster provisioning** via PXE and HTTP  
  - **Domain Name Services (DNS)** and **Dynamic Host Configuration Protocol (DHCP)**  
  - **Container network traffic** between worker nodes  
  - **Bastion node NAT configuration** to allow external access when required  

### **3. Out-of-Band/Management Network**  
- **Isolated and secured network** dedicated to **hardware and switch management**.  
- Used for:  
  - **Integrated Management Module (IMM) access** for remote server management  
  - **Serial-over-LAN (SoL)** for troubleshooting hardware remotely  
  - **Network switch configuration and monitoring**  

---

A well-structured **network architecture** ensures **high availability, security, and optimal performance** for OpenShift deployments, reducing bottlenecks and improving overall system stability. 🚀  
