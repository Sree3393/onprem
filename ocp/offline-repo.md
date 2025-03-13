---
title: Offline Repository
layout: default
parent: Subordinate Softwares
---

# Offline Repository  

## **Overview**  
An **offline repository** is essential for **air-gapped environments** where internet access is restricted. This setup allows organizations to **mirror and store container images locally**, ensuring seamless **deployment and updates** without an external network connection.  

## **Downloading the Mirror Registry**  
You can download the **mirror-registry** tool from the following URL:  

🔗 [Mirror Registry v2.0.3](https://github.com/quay/mirror-registry/releases/download/v2.0.3/mirror-registry-offline.tar.gz)  

## **Installation**  
To install the **mirror registry**, use the following command:  

```sh
./mirror-registry install --quayHostname=registry.hostname.com --quayRoot=/home/someuser/quay-install/root --quayStorage=/home/someuser/quay-install/storage --pgStorage=/home/someuser/quay-install/pgress --initUser=init --initPassword=something@123
```
- --quayHostname → Sets the hostname for the registry.
- --quayRoot → Specifies the root directory for Quay installation.
- --quayStorage → Defines the storage location for Quay container images.
- --pgStorage → Sets the storage path for PostgreSQL.
- --initUser → Creates the initial admin user.
- --initPassword → Sets the password for the initial user.


This setup will:  
- **Deploy Quay** as the **container registry**  
- **Configure storage directories** for registry and PostgreSQL  
- **Initialize the repository** with a user and password  

Setting up an **offline repository** ensures **reliable, secure, and independent** access to required **container images and dependencies**, even in **restricted network environments**. 