---
title: OpenShift AI Operator
layout: default
parent: Operator Installations
nav_order: 3
---

# **OpenShift AI Operator**  

### **Overview**  
The **OpenShift AI Operator** (formerly known as **Open Data Hub**) is a **Red Hat-certified** Operator designed to enable and streamline **AI/ML workloads** on **Red Hat OpenShift**. It provides a **scalable, enterprise-grade MLOps platform**, integrating essential tools such as:  
- **Jupyter Notebooks** for interactive development  
- **Model training and inference** tools  
- **Deployment and monitoring solutions**  
- **Seamless integration with OpenShift and IBM Cloud Pak for Data (CP4D)**  

This Operator simplifies **AI/ML lifecycle management** by automating infrastructure setup, dependency management, and resource allocation.  


---

## **Installing OpenShift AI Operator**  

### **Step 1: Create Namespace, Operator Group, and Subscription**  
To deploy the OpenShift AI Operator, first create the necessary **namespace, operator group, and subscription** for **Red Hat OpenShift AI**.  

Create a **YAML file** with the following contents:  

```yaml
apiVersion: v1  
kind: Namespace  
metadata:  
  name: redhat-ods-operator  

---  

apiVersion: operators.coreos.com/v1  
kind: OperatorGroup  
metadata:  
  name: rhods-operator  
  namespace: redhat-ods-operator  

---  

apiVersion: operators.coreos.com/v1alpha1  
kind: Subscription  
metadata:  
  name: rhods-operator  
  namespace: redhat-ods-operator  
spec:  
  name: rhods-operator  
  channel: embedded  
  source: redhat-operators  
  sourceNamespace: openshift-marketplace  
  config:  
     env:  
        - name: "DISABLE_DSC_CONFIG"  
```
#### **Apply the YAML file**  
Run the following command to apply the configuration:  
```sh
oc apply -f ai.yaml  
```
---

## **Step 2: Initialize OpenShift AI with DSC Initialization**  
**DSCInitialization** is a critical step in setting up and configuring **OpenShift AI** or **IBM Cloud Pak for Data (CP4D)**. It ensures all necessary components and dependencies are **properly initialized**, allowing AI/ML workloads to run smoothly.  

Create another **YAML file** for **DSCInitialization**:  
```yaml
apiVersion: dscinitialization.opendatahub.io/v1  
kind: DSCInitialization  
metadata:  
  name: default-dsci  
spec:  
  applicationsNamespace: redhat-ods-applications  
  monitoring:  
    managementState: Managed  
    namespace: redhat-ods-monitoring  
  serviceMesh:  
    managementState: Removed  
  trustedCABundle:  
    managementState: Managed  
    customCABundle: ""  
```
#### **Apply the DSCInitialization YAML**  
Run the command below to initialize:  
```sh
oc create -f <dsci_file_name>.yaml  
```
#### **Verification**  
Check the status of the **AI Operator** using:  
```sh
oc get pods -n redhat-ods-operator  
```
---

## **Step 3: Manage OpenShift AI Components**  

To manage the **various components** of OpenShift AI, create another YAML file with the following configuration:  
```yaml
apiVersion: datasciencecluster.opendatahub.io/v1  
kind: DataScienceCluster  
metadata:  
  name: default-dsc  
spec:  
  components:  
    codeflare:  
      managementState: Removed  
    dashboard:  
      managementState: Removed  
    datasciencepipelines:  
      managementState: Removed  
    kserve:  
      managementState: Managed  
      defaultDeploymentMode: RawDeployment  
      serving:  
        managementState: Removed  
        name: knative-serving  
    kueue:  
      managementState: Removed  
    modelmeshserving:  
      managementState: Removed  
    ray:  
      managementState: Removed  
    trainingoperator:  
      managementState: Managed  
    trustyai:  
      managementState: Removed  
    workbenches:  
      managementState: Removed  
```
#### **Apply the YAML file**  
Run the following command:  
```sh
oc create -f <file_name>.yaml  
```
#### **Verification**  
Check the status of the AI components using:  
```sh
oc get pods -n redhat-ods-applications  
```
---


For detailed documentation, refer to:  
[Red Hat OpenShift AI Installation Guide](https://docs.redhat.com/en/documentation/red_hat_openshift_ai_self-managed/2.11/html/installing_and_uninstalling_openshift_ai_self-managed/preparing-openshift-ai-for-ibm-cpd_prepare-openshift-ai-ibm-cpd)  


### **Conclusion**  
The **OpenShift AI Operator** simplifies AI/ML workloads by providing a **fully integrated** and **scalable** MLOps platform. By following the above steps, you can successfully deploy, configure, and manage OpenShift AI components using the CLI. 
