---
title: OCP Download
layout: default
parent: Downloads
---

# **OpenShift Container Platform (OCP) Download & Mirror Setup**

## **Why Download OCP Locally?**  
Downloading OpenShift Container Platform (OCP) and setting up a local mirror repository ensures a **stable, secure, and offline installation**. This is essential in environments where internet access is restricted or unreliable. The downloaded binaries and container images can be stored on a **Bastion node** and later used for **air-gapped installations** via a private Quay repository.

---

## **Downloading CoreOS Image**  
Red Hat CoreOS (RHCOS) is the recommended operating system for OpenShift nodes. Download the necessary RHCOS images from the official OpenShift repository:  

- **RHCOS Metal Image (Standard):** [Download](https://mirror.openshift.com/pub/openshift-v4/dependencies/rhcos/4.18/latest/)  
  - `rhcos-4.18.1-x86_64-metal.x86_64.raw.gz`  
- **RHCOS 4K Image (For NVMe or 4K sector disks):**  
  - `rhcos-4.18.1-x86_64-metal4k.x86_64.raw.gz`  

---

## **Downloading OCP Locally**  

### **Procedure**  
1. Go to the **OpenShift Container Platform downloads page** on the [Red Hat Customer Portal](https://access.redhat.com/downloads/).  
2. Select **x86_64** from the **Product Variant** drop-down list.  
3. Choose the **latest OpenShift version (4.18 or newer)** from the **Version** drop-down list.  
4. Download the following files:  
   - **OpenShift v4.18 Linux Clients**  
   - **OpenShift v4.18 Linux Installer**  
5. Extract the downloaded files:  
   tar xvf <file>  
6. Move the `oc` binary to a directory in your **PATH** to make it accessible:  
   echo $PATH  

---

## **Verifying the OpenShift CLI (`oc`)**  
After installation, verify that the `oc` command-line interface (CLI) is functional:  
```sh
oc <command>  
```   

---

## **Setting Up Required Environment Variables**  

1. **Define the OpenShift release version:**  
```sh
OCP_RELEASE=<release_version>  
```   
   *(e.g., `4.18.4`)*  

2. **Set the local registry details:** 
```sh 
LOCAL_REGISTRY='<local_registry_host_name>:<local_registry_host_port>'  
```   

3. **Specify the local repository name:**  
```sh
LOCAL_REPOSITORY='<local_repository_name>'  
 ```  
*(e.g., `ocp4/openshift4`)*  

4. **Define the OpenShift repository for mirroring:**  
```sh
PRODUCT_REPO='openshift-release-dev'  
```

5. **Provide the path to the pull secret:**  
```sh
LOCAL_SECRET_JSON='<path_to_pull_secret>'  
```

6. **Set the release name for mirroring:**  
```sh
RELEASE_NAME="ocp-release"  
```sh

7. **Specify the cluster architecture:**
```sh  
ARCHITECTURE=x86_64  
```
8. **Define the storage path for mirrored images:**  
```sh
REMOVABLE_MEDIA_PATH=<path>  
```
---

## **Downloading OCP Release**  
```sh
oc adm release mirror -a ${LOCAL_SECRET_JSON} --to-dir=${REMOVABLE_MEDIA_PATH}/mirror quay.io/${PRODUCT_REPO}/${RELEASE_NAME}:${OCP_RELEASE}-${ARCHITECTURE}  
```

This command **mirrors the OpenShift release images** into a local directory, enabling **offline installation**.

---

## **Uploading to Quay Repository**  
Once the images are mirrored, upload them to your **private Quay registry**:  
```sh
oc image mirror -a ${LOCAL_SECRET_JSON} --from-dir=${REMOVABLE_MEDIA_PATH}/mirror "file://openshift/release:${OCP_RELEASE}*" ${LOCAL_REGISTRY}/${LOCAL_REPOSITORY}  
```

This ensures that OpenShift nodes can pull images from the **local registry** instead of the internet.

---

## **Using `oc mirror` for Air-Gapped Installations**  

The `oc mirror` plugin is essential for mirroring OpenShift images from Red Hat repositories to a **private registry**, allowing **disconnected installations** of OpenShift and Cloud Pak for Data (CP4D).

### **Installing `oc mirror` Plugin**  

1. **Download the latest `oc mirror` plugin** for your OpenShift version:  
```sh
curl -LO https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/oc-mirror.tar.gz  
```

2. **Extract the binary:**  
```sh
tar -xvzf oc-mirror.tar.gz  
```

3. **Move it to `/usr/local/bin/` and make it executable:**  
```sh
   sudo mv oc-mirror /usr/local/bin/  
   chmod +x /usr/local/bin/oc-mirror  
```

By following these steps, you ensure that **OCP can be installed in a secure, offline environment** with minimal reliance on external networks.
