---
title: Manifest and Ignition Files
layout: default
parent: Installation
nav_order: 2
---

# **Manifest and Ignition Files**  

## **Purpose of Manifests and Ignition Files**  
During OpenShift installation, **manifests** define the cluster configuration, while **Ignition files** initialize and configure cluster nodes. These files ensure the correct deployment of cluster components, including **control plane nodes, worker nodes, and operators** that manage networking, storage, security, and monitoring.

---

## **Creating Manifests**  
Run the following command to generate manifest files:
```sh
./openshift-install create manifests --dir <installation_directory>  
```
### **Why Are Manifests Important?**  
- **Defines OpenShift configuration** before the cluster is deployed.  
- **Contains control plane and worker node settings**, including networking, authentication, and security policies.  
- **Operators depend on manifests** to deploy services like the cluster version operator, machine config operator, and networking operator.  
- **Manifests transform into Ignition configs**, which OpenShift nodes (masters and workers) use during boot-up.  
- **Without manifests**, nodes **cannot apply the necessary configurations** to join the cluster.  

---

## **Creating Ignition Files**  
Once the manifests are ready, generate Ignition files using:
```sh
./openshift-install create ignition-configs --dir <installation_directory>  
```
### **Purpose of Ignition Files**  
- **Converts manifests into Ignition configuration files**, which are used to configure cluster nodes during the boot process.  
- **Bootstrap.ign** – Initializes the bootstrap node, which temporarily aids cluster setup.  
- **Master.ign** – Configures master nodes responsible for cluster management.  
- **Worker.ign** – Configures worker nodes that run application workloads.  

### **Understanding the Bootstrap Node**  
- The **bootstrap node is temporary** and is used only for **initializing the cluster**.  
- Once the control plane is established and masters take over, **the bootstrap node can be removed**.  

---

## **Preparing Ignition Files for Deployment**  
After creating the Ignition files, place them along with the RHCOS image on the **bastion server** for network-based provisioning.

### **Copy the Ignition Files and RHCOS Image**  
Ensure that the required files are accessible via HTTP:

- Copy the **Ignition files** (`bootstrap.ign`, `master.ign`, `worker.ign`) and **RHCOS raw.gz file** to:

  /var/www/html  

To copy the ignition files:  

```sh
cp *.ign /var/www/html/
```


This ensures that nodes can download the necessary configuration during the OpenShift installation process.
