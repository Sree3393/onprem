---
title: Operators
layout: default
parent: Downloads
---

# **Operators Download and Mirroring**  

## **Overview**  
Operators in OpenShift are **packaged software components** that automate the installation, configuration, and lifecycle management of applications and services. They extend Kubernetes capabilities by ensuring **continuous monitoring, self-healing, and seamless upgrades**.  

In **disconnected or air-gapped environments**, operators are downloaded using the **`oc mirror`** command with **ImageSetConfigurations**. This process ensures that the required operators are available from a **private registry** without needing a direct internet connection.  

The **`oc mirror`** command allows:  
- **Mirroring container images** from external to private registries.  
- **Managing versioned updates** of operators.  
- **Ensuring compliance** in restricted network environments.  

   
<span style="color:red">After every **push to the mirror registry**, this process generates **`catalogsource.yaml`** and **`imagecontentsource.yaml`** files. These must be **applied after OpenShift is installed** to enable operator usage.</span>

---

## **Downloading Operators**  

### **1. Node Feature Discovery (NFD)**  
The **NFD operator** detects hardware features and labels worker nodes accordingly, allowing for **better workload scheduling** based on node capabilities.  

Create an **ImageSetConfiguration** YAML file with the following content:  



```yaml
kind: ImageSetConfiguration
apiVersion: mirror.openshift.io/v1alpha2
mirror:
  operators:
    - catalog: registry.redhat.io/redhat/redhat-operator-index:v4.18
      packages:
        - name: nfd
```


**Download the NFD operator:**  

```sh
oc mirror --dest-skip-tls=true --config=./imageset-config-nfd.yaml docker://${MIRROR_HOST}:${PORT}
```


---

### **2. GPU Operator**  
The **GPU Operator** enables **GPU-based workloads** on OpenShift by automating the deployment of **NVIDIA drivers, CUDA, and runtime dependencies**.  

Create an **ImageSetConfiguration** YAML file with the following content

```yaml
kind: ImageSetConfiguration
apiVersion: mirror.openshift.io/v1alpha2
mirror:
  platform:
    channels:
      - name: stable-4.18
        type: ocp
  operators:
    - catalog: registry.redhat.io/redhat/certified-operator-index:v4.18
      packages:
        - name: gpu-operator-certified
```


**Download the GPU operator:**  
```sh
oc-mirror --dest-skip-tls=true --skip-missing --continue-on-error --config=./imageset-config-gpu.yaml docker://${MIRROR_HOST}:${PORT}
```


---

### **3. OpenShift AI Operator**  
The **OpenShift AI (RHODS) operator** provides **AI/ML workloads** with Jupyter Notebooks, Model Serving, and MLOps tools. It includes **OpenShift Pipelines, Serverless, and Service Mesh** for better AI/ML orchestration.  

Create an **ImageSetConfiguration** YAML file with the following content

```yaml
kind: ImageSetConfiguration
apiVersion: mirror.openshift.io/v1alpha2
mirror:
  platforms:
    architectures:
      - amd64
  operators:
    - catalog: registry.redhat.io/redhat/redhat-operator-index:v4.18
      packages:
        - name: rhods-operator
          channels:
            - name: stable
              minVersion: 2.11.0
              maxVersion: 2.11.0
        - name: openshift-pipelines-operator-rh
          channels:
            - name: stable
        - name: serverless-operator
          channels:
            - name: stable
        - name: servicemeshoperator
          channels:
            - name: stable

```


**Download the OpenShift AI operator:**  
```sh
oc-mirror --dest-skip-tls=true --skip-missing --continue-on-error --config=./imageset-config-gpu.yaml docker://${MIRROR_HOST}:${PORT}
```


---

## **Summary**  
- **NFD Operator**: Helps in workload scheduling by detecting hardware capabilities.  
- **GPU Operator**: Enables GPU workloads by managing NVIDIA drivers and dependencies.  
- **OpenShift AI Operator**: Provides AI/ML capabilities within OpenShift, including Jupyter Notebooks and MLOps tools.  

Using **operator mirroring**, OpenShift ensures that critical software dependencies are available **even in air-gapped environments**, making deployments more **resilient and scalable**. 
