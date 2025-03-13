---
title: NFD
layout: default
parent: Operator Installations
nav_order: 1
---

# **Node Feature Discovery (NFD) Operator**  

## **Why Use NFD?**  
The **Node Feature Discovery (NFD) Operator** is an essential component in **Kubernetes and OpenShift** that automates the detection of hardware and software capabilities on cluster nodes. It helps in **efficient workload scheduling** by adding node labels based on:  
- **CPU architecture**  
- **Kernel version**  
- **PCI devices, storage controllers, and accelerators (e.g., GPUs, FPGAs)**  
- **Security features like SGX**  
- **Other specialized hardware capabilities**  

This enables **intelligent workload placement**, ensuring that **compute-intensive, GPU-based, or specialized workloads** run on the most suitable nodes.



---

## **Installing NFD Operator**  

### **Step 1: Create a Namespace**  
The NFD Operator runs within its own namespace. Create a namespace called **openshift-nfd**:  
```yaml
apiVersion: v1  
kind: Namespace  
metadata:  
  name: openshift-nfd  
  labels:  
    name: openshift-nfd  
    openshift.io/cluster-monitoring: "true"  
```


Apply the namespace:  
```sh
oc create -f nfd-namespace.yaml  
```
---

### **Step 2: Create an Operator Group**  
The **OperatorGroup CR** allows the NFD Operator to function in its namespace:  

```yaml
apiVersion: operators.coreos.com/v1  
kind: OperatorGroup  
metadata:  
  generateName: openshift-nfd-  
  name: openshift-nfd  
  namespace: openshift-nfd  
spec:  
  targetNamespaces:  
  - openshift-nfd  
```

Apply the YAML file:  
```sh
oc create -f nfd-operatorgroup.yaml  
```
---

### **Step 3: Subscribe to the Operator**  

The **Subscription CR** ensures the NFD Operator is installed automatically:  
```yaml
apiVersion: operators.coreos.com/v1alpha1  
kind: Subscription  
metadata:  
  name: nfd  
  namespace: openshift-nfd  
spec:  
  channel: "stable"  
  installPlanApproval: Automatic  
  name: nfd  
  source: redhat-operators  
  sourceNamespace: openshift-marketplace  
```

Apply the subscription:  
```sh
oc create -f nfd-sub.yaml  
```
---

## **Verifying Installation**  

To check if the NFD Operator pods are running:  
```sh
oc get pods -n openshift-nfd  
```
---

## **Deploying NFD in a Disconnected Environment**  

In an **air-gapped (offline) environment**, follow these steps:

### **Step 1: Install skopeo**  
🔗 Follow this guide: [Skopeo Installation](https://github.com/containers/skopeo/blob/main/install.md)  

### **Step 2: Inspect NFD Image**  

Use skopeo to inspect the NFD image from Red Hat’s container registry:  
```sh
skopeo inspect docker://registry.redhat.io/openshift4/ose-node-feature-discovery:v4.18
```

### **Step 3: Mirror the Image**  

Copy the image from Red Hat’s registry to your private mirror registry:  
```sh
skopeo copy docker://registry.redhat.io/openshift4/ose-node-feature-discovery@ docker:///openshift4/ose-node-feature-discovery@<image_digest>  
```
---

## **Deploying Node Feature Discovery CR**  

### **Step 1: Create the NodeFeatureDiscovery CR**  

This CR configures NFD to detect and label hardware features on nodes.  
```yaml
apiVersion: nfd.openshift.io/v1  
kind: NodeFeatureDiscovery  
metadata:  
  name: nfd-instance  
spec:  
  operand:  
    image: <mirror_registry>/openshift4/ose-node-feature-discovery@<image_digest>  
    imagePullPolicy: Always  
  workerConfig:  
    configData: |  
      core:  
        sleepInterval: 60s  
      sources:  
        cpu:  
          cpuid:  
            attributeBlacklist:  
              - "BMI1"  
              - "BMI2"  
              - "CLMUL"  
              - "CMOV"  
              - "CX16"  
              - "ERMS"  
              - "F16C"  
              - "HTT"  
              - "LZCNT"  
              - "MMX"  
              - "MMXEXT"  
              - "NX"  
              - "POPCNT"  
              - "RDRAND"  
              - "RDSEED"  
              - "RDTSCP"  
              - "SGX"  
              - "SSE"  
              - "SSE2"  
              - "SSE3"  
              - "SSE4.1"  
              - "SSE4.2"  
              - "SSSE3"  
        kernel:  
          kconfigFile: "/path/to/kconfig"  
          configOpts:  
            - "NO_HZ"  
            - "X86"  
            - "DMI"  
        pci:  
          deviceClassWhitelist:  
            - "0200"  
            - "03"  
            - "12"  
          deviceLabelFields:  
            - "class"  
  customConfig:  
    configData: |  
      - name: "more.kernel.features"  
        matchOn:  
        - loadedKMod: ["example_kmod3"]  
```

Apply the configuration:  
```sh
oc apply -f <filename>  
```
---

## **Verifying Node Feature Discovery**  

To check the status of the NFD instance:  
```sh
oc get nodefeaturediscovery nfd-instance -o yaml  
```

For detailed documentation, refer to:  
🔗 [OpenShift NFD Operator Documentation](https://docs.redhat.com/en/documentation/openshift_container_platform/4.18/html/specialized_hardware_and_driver_enablement/psap-node-feature-discovery-operator)  


This setup ensures that NFD efficiently detects and labels node features, enabling **intelligent workload scheduling** in your Kubernetes or OpenShift cluster. 
