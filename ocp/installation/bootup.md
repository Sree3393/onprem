---
title: Booting Up
layout: default
parent: Installation
nav_order: 3
---

# **Booting Up OpenShift Nodes**  

## **Purpose of This Step**  
Bootstrapping is a critical phase in the **OpenShift cluster installation** process. It involves setting up the initial **bootstrap node**, which temporarily assists in configuring the **master nodes**. Once the cluster is operational, the bootstrap node is no longer needed.  

The following steps will help you:  
- Assign **static IP, gateway, and DNS** to the nodes.  
- Set the **correct timezone** to ensure consistency.  
- Install **CoreOS (RHCOS)** on the nodes using the **coreos-installer**.  

---

## **Step 1: Configure the Bootstrap Node**  
On the **bootstrap node**, run the following command to configure the **IP address, gateway, and DNS settings**:  
```sh
sudo nmtui
```
![nmtui](https://raw.githubusercontent.com/pratiks360/onprem/refs/heads/main/ocp/Download/installation/ntmui.png)
This command opens a **network configuration interface** where you should:  
- Set the **IPv4 address**.  
- Define the **gateway** and **DNS server** settings.  
- Save and apply the configuration.  

---

## **Step 2: Set Timezone**  
Ensure that the correct **timezone** is set:  
```sh![alt text](image.png)
sudo timedatectl set-timezone Asia/Kolkata  
```
To verify the timezone:  
```sh
sudo timedatectl  
```
Having the correct timezone ensures **synchronized logging and coordination** across cluster nodes.  

---

## **Step 3: Install CoreOS on the Bootstrap Node**  
Now, install **CoreOS (RHCOS)** using the `coreos-installer`:  
```sh
sudo coreos-installer install /dev/sda --ignition-url=http://{bastion_server_ip}:8080/bootstrap.ign  --insecure-ignition --image-url=http://{bastion_server_ip}:8080/rhcos.raw.gz --insecure --append-karg ip={node_server_ip}::{gateway}:{subnet}:bootstrap.domainname.com:enp1s0:none --append-kargnameserver={bastion_server_ip}
```
### **What This Command Does**  
- Installs **Red Hat CoreOS (RHCOS)** on the node’s primary disk (`/dev/sda`).  
- Fetches the **Ignition file** (`bootstrap.ign`) from the **Bastion server**.  
- Downloads the **RHCOS image** for installation.  
- Appends necessary **kernel arguments** for network settings.  

---

## **Step 4: Configure the Master Nodes**  
Once the **bootstrap installation** is complete, repeat the **same steps** for the **master nodes**. However, when running the `coreos-installer`, replace `bootstrap.ign` with **master.ign**:
```sh
sudo coreos-installer install /dev/sda --ignition-url=http://{bastion_server_ip}:8080/master.ign  --insecure-ignition --image-url=http://{bastion_server_ip}:8080/rhcos.raw.gz --insecure --append-karg ip={node_server_ip}::{gateway}:{subnet}:master.domainname.com:enp1s0:none --append-kargnameserver={bastion_server_ip}
```

## **BootStrapping Complete**  
After the installation is complete on all 3 masters, run the below command in bastion node:
```sh
./openshift-install --dir <installation_directory> wait-for bootstrap-complete \ 
--log-level=info 
```
![complete](https://raw.githubusercontent.com/pratiks360/onprem/refs/heads/main/ocp/Download/installation/complete.png)

## **Step 5: Configure the Worker Nodes** 
Follow the same procedure to configure and install CoreOS on **worker nodes**. 
```sh
sudo coreos-installer install /dev/sda --ignition-url=http://{bastion_server_ip}:8080/worker.ign  --insecure-ignition --image-url=http://{bastion_server_ip}:8080/rhcos.raw.gz --insecure --append-karg ip={node_server_ip}::{gateway}:{subnet}:worker.domainname.com:enp1s0:none --append-kargnameserver={bastion_server_ip}
```

## **Step 5: Verify Cluster Initialization**  
Once all nodes are installed and booted, switch to the **bastion server** and verify the cluster setup:  

1. **Export Kubeconfig Credentials**  
   The OpenShift installation generates a kubeconfig file that must be sourced to interact with the cluster:  
```sh
export KUBECONFIG=<installation_directory>/auth/kubeconfig  
```
2. **Approve Worker Node Certificates**  
   Worker nodes require certificate signing requests (CSRs) to be approved before they can join the cluster. Approve the pending CSRs using:  
```sh
oc adm certificate approve <csr_name>  
```
3. **Verify Node Status**  
   Check whether all nodes are in the **Ready** state:  
```sh
oc get nodes  
```

**Expected output:**
![nodes](https://raw.githubusercontent.com/pratiks360/onprem/refs/heads/main/ocp/Download/installation/nodes.png)

Once all nodes are in the **Ready** state, the OpenShift cluster is fully initialized and operational.  
Following these steps ensures a **smooth OpenShift installation** by correctly setting up the **bootstrap, master, and worker nodes**. 

