---
title: Dynamic NFS Provisioning
layout: default
parent: Cloud Pak for Data (CP4D)
nav_order: 2
---

# **Setting Up Dynamic NFS Provisioning**  

To enable **dynamic storage provisioning** in OpenShift using **NFS (Network File System)**, you need to configure an **NFS Client Provisioner**. This provisioner automates the creation of **persistent storage volumes (PVs)** and their bindings to **persistent volume claims (PVCs)** within a Kubernetes environment.

## **Prerequisites**  
- A **running NFS server** with an **exported share** for Kubernetes storage.  
- **OpenShift or Kubernetes cluster** with administrative access.  
- The **cpd-cli** (Cloud Pak for Data CLI) installed.  

---

## **Configuring Environment Variables**  
Before setting up dynamic provisioning, define the following **environment variables** to specify the NFS server details and storage class configuration.

### **1. Define NFS Server Location**  
Set the IP address or **Fully Qualified Domain Name (FQDN)** of the NFS server:
```sh  
export NFS_SERVER_LOCATION=<server-address>  
```

### **2. Specify NFS Path**  
Define the **exported directory** on the NFS server where the provisioner will create subdirectories for persistent storage. The default path is `/`:  
```sh
export NFS_PATH=<path>  
```

### **3. Set the Kubernetes Namespace**  
Specify the **namespace (project) name** where the **NFS Client Provisioner** will be deployed. The recommended project is `nfs-provisioner`, but you can choose any namespace:  
```sh
export PROJECT_NFS_PROVISIONER=<project-name>  
```

### **4. Define Storage Class Name**  
Set a custom name for the **NFS Storage Class**, which will be used when requesting persistent storage volumes:  
```sh
export NFS_STORAGE_CLASS=<storage-class-name>  
```

### **5. Specify NFS Provisioner Image**  
Set the **container image location** for the `nfs-subdir-external-provisioner`. This image enables Kubernetes to dynamically manage storage allocation using NFS:  
```sh
export NFS_IMAGE=${PRIVATE_REGISTRY_LOCATION}/nfs-subdir-external-provisioner:v4.0.2  
```

---

## **Deploying the NFS Client Provisioner**  
Once the **environment variables** are set, execute the following command to install and configure the **NFS Client Provisioner** using the `cpd-cli`:

```sh
cpd-cli manage setup-nfs-provisioner \
--nfs_server=${NFS_SERVER_LOCATION} \
--nfs_path=${NFS_PATH} \
--nfs_provisioner_ns=${PROJECT_NFS_PROVISIONER} \
--nfs_storageclass_name=${NFS_STORAGE_CLASS} \
--nfs_provisioner_image=${NFS_IMAGE}  
```
---

## **Verification**  
1. **Check if the provisioner is deployed correctly:**  
   Run the following command to verify the provisioner is running in the assigned namespace:  
   kubectl get pods -n ${PROJECT_NFS_PROVISIONER}  

2. **Validate the Storage Class:**  
   Confirm that the newly created NFS storage class is available:  
   kubectl get storageclass  

3. **Test Dynamic Provisioning:**  
   - Create a **Persistent Volume Claim (PVC)** using the configured storage class.  
   - Check if a corresponding **Persistent Volume (PV)** is automatically created and bound to the PVC.  

---

## **Conclusion**  
By setting up the **NFS Client Provisioner**, you enable Kubernetes to dynamically create and manage **persistent volumes** backed by an **NFS server**. This simplifies storage provisioning and enhances flexibility for workloads requiring shared file storage. 
