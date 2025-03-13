---
title: Hardware Requirements
layout: default
parent: Requirements
---

# Hardware Requirements

## CPU ARCH: x86_64  
The **x86_64** architecture is the industry standard for enterprise computing, providing high performance, scalability, and compatibility with OpenShift, CP4D, and Watsonx.ai deployments.

## SIZING  
Proper sizing ensures optimal performance and resource allocation for various workloads. The table below outlines the required vCPU, RAM, and disk capacity for different node types in an airgapped OpenShift environment.  

| Type       | VCPU | RAM (GB) | Disk (GB) | OS |
|:-------------|:------------------|:------|:------|         
| Master      | 4 | 16  | 100  | CoreOS |
| Worker      | 16 | 64  | 300  | CoreOS |
| GPU Worker  | 32 | 256 | 500  | CoreOS |
| Bastion  | 8 | 16 | 1000  | RHEL 9 |
| Load balancer  | 4 | 8 | 100  | RHEL 9 |
