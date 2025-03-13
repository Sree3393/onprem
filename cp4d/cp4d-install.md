---
title: CP4D Installation
layout: default
parent: Cloud Pak for Data (CP4D)
nav_order: 3
---

# **Cloud Pak for Data (CP4D) Installation Guide**  

This guide outlines the step-by-step process to install **IBM Cloud Pak for Data (CP4D)** in an **OpenShift** environment, including namespace creation, permissions setup, and essential component installations.  

---

## **Step 1: Creating Required Namespaces**  

Namespaces are essential in OpenShift to logically separate workloads and manage access control. CP4D requires dedicated namespaces for operators, services, and critical components.  

Refer to the official IBM documentation for more details:  
[Manually Creating Projects and Namespaces](https://www.ibm.com/docs/en/software-hub/5.1.x?topic=hub-manually-creating-projects-namespaces)  

Run the following commands to create the required namespaces:  
```sh
oc new-project cpd-operators  
oc new-project cpd-services  
oc new-project ibm-cert-manager  
oc new-project ibm-licensing-manager  
oc new-project ibm-scheduler  
```

- **cpd-operators** → Hosts CP4D operator components.  
- **cpd-services** → Contains service instances and workloads.  
- **ibm-cert-manager** → Manages certificates and security automation.  
- **ibm-licensing-manager** → Ensures compliance with IBM licensing requirements.  
- **ibm-scheduler** → Handles workload scheduling and resource allocation.  

---

## **Step 2: Granting Permissions to Namespaces**  

CP4D requires appropriate permissions for its operators and services to function correctly. The `authorize-instance-topology` command ensures proper access control.  

Refer to IBM’s official documentation:  
[Applying Required Permissions](https://www.ibm.com/docs/en/cloud-paks/cp-data/5.0.x?topic=arppn-applying-required-permissions-by-running-authorize-instance-topology-command)  

Run the following command to grant necessary permissions:  

```sh
cpd-cli manage authorize-instance-topology \  
--cpd_operator_ns=cpd-operators \  
--cpd_instance_ns=cpd-services  
```

This step ensures that CP4D components can communicate effectively and operate within their assigned namespaces.  

---

## **Step 3: Installing Essential Cluster Components**  

Certain IBM components are crucial for CP4D operations, including:  
- **IBM Scheduler** – Manages workload distribution across the cluster.  
- **IBM Cert Manager** – Automates SSL/TLS certificate management.  
- **IBM Licensing** – Ensures compliance with IBM licensing policies.  

Install these components using the following commands:  
```sh
cpd-cli manage apply-cluster-components \  
--release=5.1.1 \  
--license_acceptance=true \  
--cert_manager_ns=ibm-cert-manager \  
--licensing_ns=ibm-licensing-manager  
```

Scheduler

```sh
cpd-cli manage apply-scheduler \  
--release=5.1.1 \  
--license_acceptance=true \  
--scheduler_ns=ibm-scheduler  
```

This step ensures that **certificates, licensing, and scheduling** functionalities are properly configured before deploying CP4D services.  

---

## **Step 4: Installing IBM Software Hub**  

The IBM **Software Hub** is a core component of CP4D that provides access to various AI and data services. The following command sets up the software hub with appropriate storage configurations:  

```sh
cpd-cli manage setup-instance \  
--release=${VERSION} \  
--license_acceptance=true \  
--cpd_operator_ns=cpd-operators \  
--cpd_instance_ns=cpd-services \  
--block_storage_class=${STG_CLASS_BLOCK} \  
--file_storage_class=${STG_CLASS_FILE} \  
--run_storage_tests=true  
```

- **${VERSION}** → Specifies the CP4D version being installed.  
- **${STG_CLASS_BLOCK} & ${STG_CLASS_FILE}** → Define the block and file storage classes to be used.  
- **block_storage_class and file_storage_class** → Are the Storage class names which were set in the dynamic nfs storage provisioning step.
- **run_storage_tests=true** → Ensures that the storage setup is functional before proceeding.  


---

## **Step 5: Validating the Installation**  

After installation, verify the status of CP4D components to ensure that everything is running correctly.  

### **Check Operand Status**  

Run the following command to confirm if all operands have been successfully installed and their status is `Completed`:  
```sh
cpd-cli manage get-cr-status \  
--cpd_instance_ns=${PROJECT_CPD_INST_OPERANDS}  
```

### **Retrieve CP4D Web Client URL and Credentials**  

To access the CP4D UI, retrieve the instance details using:  
```sh
cpd-cli manage get-cpd-instance-details \  
--cpd_instance_ns=${PROJECT_CPD_INST_OPERANDS} \  
--get_admin_initial_credentials=true  
```

This command will provide the **URL** and **default admin credentials** for logging into the CP4D web interface.  

---

## **Final Notes**  

Following these steps ensures a **fully functional** CP4D environment in OpenShift. Proper namespace separation, permission management, and component installations are critical for a **secure and scalable AI platform**. 
