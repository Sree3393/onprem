---
title: CP4D Download
layout: default
parent: Cloud Pak for Data (CP4D)
nav_order: 1
---

# **CP4D Download Guide**  

IBM **Cloud Pak for Data (CP4D)** is an integrated data and AI platform that simplifies data management and AI lifecycle automation. To deploy CP4D efficiently, administrators need to download necessary software components, container images, and configurations.  

---

## **1. Download CPD-CLI**  
**CPD-CLI (Cloud Pak for Data Command Line Interface)** is a command-line tool that facilitates the installation, configuration, and management of CP4D. It allows administrators to automate deployment, update components, and troubleshoot efficiently.  

Download the latest **CPD-CLI package** from IBM’s official repository:  

https://github.com/IBM/cpd-cli/releases/download/v14.1.1/cpd-cli-linux-SE-14.1.1.tgz  

Once downloaded, extract and install it on the system where CP4D will be managed.  

---

## **2. Authenticate with IBM Entitled Registry**  
To access and download required container images, log in to the IBM Entitled Registry using your **IBM Entitlement Key**:  
[Visit here to obtain your IBM Entitlement Key](https://myibm.ibm.com/products-services/containerlibrary)

```sh
cpd-cli manage login-entitled-registry \ ${IBM_ENTITLEMENT_KEY}  
```
This ensures that your system can pull necessary CP4D images from IBM's private registry.  

---

## **3. Download Required Cases**  
In **IBM Cloud Pak for Data (CP4D)**, "cases" are structured software bundles containing necessary components, installation files, and dependencies. They ensure that the deployment is complete and up to date.  

To download specific CP4D cases, run:  
```sh
cpd-cli manage case-download \ --components=cpd_platform,watsonx_ai,cpfs \ --release=5.1.1 \ --from_oci=true  
```
This command fetches installation files for the selected components (**CPD Platform, Watsonx AI, and CPFS**) from the IBM registry.  

---

## **4. List Available Container Images**  
Before proceeding with installation, it's essential to verify the required container images. This helps in identifying missing or outdated images before mirroring them to an internal repository.  

Run the following command to list available container images for CP4D:  
```sh
cpd-cli manage list-images \ --components=cpd_platform,watsonx_ai,cpfs \ --release=5.1.1 \ --inspect_source_registry=true  
```
This provides a list of all images required for the deployment and their current availability in the IBM registry.  

---

## **5. Download and Mirror Images to a Local Registry**  
To complete the setup, CP4D images must be mirrored to an **intermediate local repository**. This step is crucial for air-gapped environments or on-premise installations where external access to IBM’s registry is restricted.  

Execute the following command to start mirroring images:  
```sh
cpd-cli manage mirror-images \ --components=cpd_platform,watsonx_ai,cpfs \ --release=5.1.1 \ --target_registry=127.0.0.1:12443 \ --arch=amd64 \ --case_download=false  
```
This command:  
- Downloads required container images for the specified components.  
- Mirrors them to the local registry at **127.0.0.1:12443** for internal use.  
- Ensures compatibility with **AMD64** architecture.  
- Avoids redundant case downloads by setting `--case_download=false`.  


---

## **6. Mirror NFS Provisioner Images**  

CP4D requires an **NFS provisioner** for managing shared storage in OpenShift clusters. To download and mirror the NFS provisioner images:  
```sh
cpd-cli manage mirror-nfs-provisioner \  
--target_registry=127.0.0.1:12443 \  
--source_registry=registry.k8s.io/sig-storage  
```
This ensures that storage provisioning is available for CP4D components.  

---

## **7. Download Foundation Models for Watsonx.ai**  

Watsonx.ai supports **foundation models** that require large-scale AI models to be downloaded and mirrored.  

For example, to download the **Flan-UL2-20B model**:  
```sh
cpd-cli manage mirror-images \  
--components=watsonx_ai_ifm \  
--release=5.0.3 \  
--target_registry=127.0.0.1:12443 \  
--arch=${IMAGE_ARCH} \  
--case_download=false \  
--groups=ibmwxGoogleFlanul2  
```
Additional models can be found in the **IBM Documentation**:  
[[IBM Model Repository](https://www.ibm.com/docs/en/software-hub/5.1.x?topic=registry-mirroring-images-using-intermediary-container)

---

## **8. Download OLM Utils Image**  

The **OLM (Operator Lifecycle Manager) Utils Image** contains essential utilities for managing OpenShift operators, debugging installations, and maintaining cluster health.  

To download and save the **OLM utils image**, run:  
```sh
cpd-cli manage save-image \  
--from=icr.io/cpopen/cpd/olm-utils-v3:latest  
```
Load the image onto the **Bastion Host**:  
```sh
cpd-cli manage load-image \  
--source-image=icr.io/cpopen/cpd/olm-utils-v3:latest  
```
Finally, set the **environment variable** for easy access:  
```sh
export OLM_UTILS_IMAGE=icr.io/cpopen/cpd/olm-utils-v3:latest  
```
---

## **9. Validate Mirrored Images**  

Once images are mirrored to the **private container registry**, validate them to ensure all required images are available.  

List mirrored images:  
```sh
cpd-cli manage list-images \  
--components=pd_platform,watsonx_ai,cpfs\  
--release=5.1.1 \  
--target_registry=${PRIVATE_REGISTRY_LOCATION} \  
--case_download=false  
```
The output is saved in **list_images.csv** within the **work/offline/${VERSION}** directory.  

Check for errors in the output:  
```sh
grep "level=fatal" list_images.csv  
```
This completes the **CP4D and Watsonx.AI download and image mirroring process**, ensuring all necessary components are ready for deployment.




