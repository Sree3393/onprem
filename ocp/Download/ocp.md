---
title: OCP Download
layout: default
parent: Downloads
---

# **OpenShift Container Platform (OCP) Download & Mirror Setup**

## **Why Download OCP Locally?**  
To ensure a **reliable, offline installation**, you can first download **OCP binaries** onto your local machine and later transfer them to the **Bastion node** for setup with a **Quay repository**.

---

## **Download CoreOS image**  
Visit RHCOS Downloads and download rhcos-4.18.1-x86_64-metal.x86_64.raw.gz [Link](https://mirror.openshift.com/pub/openshift-v4/dependencies/rhcos/4.18/latest/)
Download 4K image rhcos-4.18.1-x86_64-metal4k.x86_64.raw.gz as well
## **Downloading OCP Locally**  

### **Procedure**  
1. Navigate to the **OpenShift Container Platform downloads page** on the [Red Hat Customer Portal](https://access.redhat.com/downloads/).  
2. Select the **architecture** from the **Product Variant** drop-down list.  
3. Select the **appropriate version** from the **Version** drop-down list.  --x86_64  
4. Click **Download Now** next to the **OpenShift v4.18 Linux Clients** entry and save the file.  
5. **Download** the  OpenShift v4.18 Linux Installer as well
6. Unpack the archive:  
```sh
 tar xvf <file>
 ```

6. Place the `oc` binary in a directory that is on your **PATH**.  

   - To check your **PATH**, execute the following command:  
```sh
      echo $PATH
```
---

## **Verifying the OpenShift CLI (`oc`)**  
After installation, ensure the `oc` CLI is available:  
```sh
oc <command>  
```
---

## **Setting Up Required Environment Variables**  

1. **Export the OpenShift release version:**  
```sh
    OCP_RELEASE=<release_version>  
```
   - Replace `<release_version>` with the version you want to install, e.g., `4.18.4`.  

2. **Export the local registry name and host port:**  
```sh
    LOCAL_REGISTRY='<local_registry_host_name>:<local_registry_host_port>'  
```
   - `<local_registry_host_name>` → Domain name of your **mirror repository**.  
   - `<local_registry_host_port>` → Port where content is served.  

3. **Export the local repository name:**  
```sh
    LOCAL_REPOSITORY='<local_repository_name>'  
```
   - `<local_repository_name>` → Name of the **repository** in your registry, e.g., `ocp4/openshift4`.  

4. **Export the repository to mirror:**  
```sh
    PRODUCT_REPO='openshift-release-dev'  
```
   - For production releases, always use `openshift-release-dev`.  

5. **Export the path to the registry pull secret:**  
```sh
   LOCAL_SECRET_JSON='<path_to_pull_secret>'  
```
   - `<path_to_pull_secret>` → Absolute path to the **pull secret** for your mirror registry.  

6. **Export the release mirror name:**  
```sh
    RELEASE_NAME="ocp-release"  
```
   - For production releases, always use `ocp-release`.  

7. **Export the cluster architecture:**  
```sh
   ARCHITECTURE=<cluster_architecture>  
```
   - Specify the architecture: `x86_64`, `aarch64`, `s390x`, or `ppc64le`.  
   - in our case it should be x86_64

8. **Export the path to the directory to store mirrored images:**  
```sh
   REMOVABLE_MEDIA_PATH=<path>  
```
   - Provide the full path, **including the initial `/` character**.  

---

## **Downloading OCP Release**  
```sh
oc adm release mirror -a ${LOCAL_SECRET_JSON} --to-dir=${REMOVABLE_MEDIA_PATH}/mirror quay.io/${PRODUCT_REPO}/${RELEASE_NAME}:${OCP_RELEASE}-${ARCHITECTURE}  
```
---

## **Uploading to Quay Repository**  

> **Note:** Perform this step **after** setting up the **offline Quay repository**.  
```sh
oc image mirror -a ${LOCAL_SECRET_JSON} --from-dir=${REMOVABLE_MEDIA_PATH}/mirror "file://openshift/release:${OCP_RELEASE}*" ${LOCAL_REGISTRY}/${LOCAL_REPOSITORY}  
```
The above steps ensures **OCP can be installed in a secure, offline environment** with minimal dependencies on external networks. 
