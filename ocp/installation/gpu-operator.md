---
title: GPU-Operator
layout: default
parent: Operator Installations
nav_order: 2
---

# **NVIDIA GPU Operator for OpenShift**  

## **Overview**  
The **NVIDIA GPU Operator** simplifies the deployment and management of **NVIDIA GPU drivers, CUDA runtime, and monitoring tools** within Kubernetes and OpenShift clusters. It ensures that GPU resources are properly configured and available for workloads running in the cluster.  

### **Key Components**  
- **NVIDIA Driver** – Installs the required GPU drivers on worker nodes.  
- **NVIDIA Container Toolkit** – Enables GPU support for containerized applications.  
- **NVIDIA Device Plugin** – Detects and advertises GPUs to the cluster scheduler for resource allocation.  

---

## **Installing the NVIDIA GPU Operator**  

### **1. Create a Namespace**  

The GPU Operator requires a dedicated namespace. Create it using the following YAML configuration:  
```yaml
apiVersion: v1  
kind: Namespace  
metadata:  
  name: nvidia-gpu-operator  
```

Apply the configuration:  
```sh
oc create -f nvidia-gpu-operator.yaml  
```
---

### **2. Install the Operator Group**  

To ensure the GPU Operator functions within the correct namespace, create an **OperatorGroup**:  
```yaml
apiVersion: operators.coreos.com/v1  
kind: OperatorGroup  
metadata:  
  name: nvidia-gpu-operator-group  
  namespace: nvidia-gpu-operator  
spec:  
  targetNamespaces:  
  - nvidia-gpu-operator  
```

Apply the configuration:  
```sh
oc create -f nvidia-gpu-operatorgroup.yaml  
```
---

### **3. Determine the Operator Channel**  

The installation requires a **channel value** to specify the correct version of the GPU Operator. Run the following command to fetch it:  
```sh
oc get packagemanifest gpu-operator-certified -n openshift-marketplace -o jsonpath='{.status.defaultChannel}'  
```

Example output:  
```sh
v22.9  
```
---

### **4. Retrieve the StartingCSV Value**  

The **startingCSV** value is required for the Subscription CR. Run the following command to get it:  
```sh
Export CHANNEL=v22.9  
oc get packagemanifests/gpu-operator-certified -n openshift-marketplace -ojson | jq -r '.status.channels[] | select(.name == "'$CHANNEL'") | .currentCSV'  
```

---

### **5. Create a Subscription for the GPU Operator**  

The Subscription CR ensures that the operator is installed and managed automatically.  
```yaml
apiVersion: operators.coreos.com/v1alpha1  
kind: Subscription  
metadata:  
  name: gpu-operator-certified  
  namespace: nvidia-gpu-operator  
spec:  
  channel: "v22.9"  
  installPlanApproval: Manual  
  name: gpu-operator-certified  
  source: certified-operators  
  sourceNamespace: openshift-marketplace  
  startingCSV: "gpu-operator-certified.v22.9.0"  
```

Apply the configuration:  
```sh
oc create -f nvidia-gpu-sub.yaml  
```
---

### **6. Approve the Install Plan**  

After creating the subscription, an **install plan** is generated. Verify its existence:  
```sh
oc get installplan -n nvidia-gpu-operator  
```

Example output:  
```sh
NAME            CSV                              APPROVAL   APPROVED  
install-wwhfj   gpu-operator-certified.v22.9.0   Manual     false  
```
Approve the install plan manually:  
```sh
INSTALL_PLAN=$(oc get installplan -n nvidia-gpu-operator -oname)  
oc patch $INSTALL_PLAN -n nvidia-gpu-operator --type merge --patch '{"spec":{"approved":true }}'  
```
Example output:  
```sh
installplan.operators.coreos.com/install-wwhfj patched  
```
---

### **7. Create the ClusterPolicy**  

A **ClusterPolicy** is required to configure and deploy the NVIDIA GPU Operator. This policy ensures proper **GPU driver installation, monitoring, and device management** across the cluster.  

Retrieve the **ClusterPolicy resource** and apply it:  
```sh
oc get csv -n nvidia-gpu-operator gpu-operator-certified.v22.9.0 -ojsonpath={.metadata.annotations.alm-examples} | jq .[0] > clusterpolicy.json  
oc apply -f clusterpolicy.json  
```

Example output:  

```sh
clusterpolicy.nvidia.com/gpu-cluster-policy created  
```
---

### **8. Verify the Installation**  
Check if all the necessary pods and daemons are running successfully:  
```sh
oc get pods,daemonset -n nvidia-gpu-operator  
```


**Reference:**  
[NVIDIA GPU Operator Installation Guide](https://docs.nvidia.com/datacenter/cloud-native/openshift/23.9.2/install-gpu-ocp.html)  

This confirms that the NVIDIA GPU Operator is **installed, configured, and ready** for GPU-accelerated workloads in OpenShift.