---
title: Troublshooting and tips
layout: default
nav_order: 5
---

# **Cloud Pak for Data (CP4D) Deployment and Troubleshooting Guide**  

This document covers key considerations, troubleshooting steps, and best practices for deploying **IBM Cloud Pak for Data (CP4D)** on **OpenShift Container Platform (OCP)**.

---


### **a. Docker Storage Considerations on Bastion**  

By default, **Docker** stores container runtime data (**runroot**) and image/container storage (**graphroot**) under `/var/lib/docker`. However, in large-scale deployments, modifying these locations can improve **performance, reliability, and storage management**.  

**Why Change `runroot` and `graphroot` Paths?**  
- **Free up disk space** – `/var` may have limited capacity in some environments.  
- **Improve performance** – Using a dedicated SSD or NVMe disk can **enhance read/write speeds**.  
- **Better storage management** – Helps in **optimizing disk usage** for enterprise setups.  

#### **Steps to Change `runroot` and `graphroot`**  

1. **Stop Docker Service**  
```sh
systemctl stop docker  
```

2. **Edit Docker Storage Configuration**  
   Modify `/etc/docker/daemon.json` (create it if it doesn't exist):  
```sh
   {
     "data-root": "/new/path/docker",
     "exec-root": "/new/path/docker-run"
   }  
```

   - `"data-root"` (previously **graphroot**) → Stores images, volumes, and container data.  
   - `"exec-root"` (previously **runroot**) → Stores temporary runtime files.  

3. **Move Existing Data (Optional)**  
```sh
mv /var/lib/docker /new/path/docker  
```
4. **Restart Docker Service**  
```sh
systemctl daemon-reload  
systemctl restart docker  
```

5. **Verify the Changes**  
```sh
docker info | grep -E 'Root Dir|Exec Root'  
```
---

### **b. Troubleshooting: CrashLoopBackOff, Failed, Pending, and ImagePullBackOff Pods**  

#### **CrashLoopBackOff / Failed Pods**  
Pods enter a **CrashLoopBackOff** or **Failed** state when they restart repeatedly due to **application errors, misconfigurations, or resource limitations**.  

##### **Steps to Troubleshoot:**  

1. **Inspect Container Logs**  
```sh
oc logs -f <pod-name> -n <namespace>  
```

If multiple containers exist in the pod: 

```sh 
oc logs -f <pod-name> -n <namespace> -c <container-name>  
```

2. **Check Operator Logs** (If managed by an Operator)  

```sh
oc logs -f <operator-pod> -n <operator-namespace>  
```

3. **Describe the Pod for Events**

```sh  
oc describe pod <pod-name> -n <namespace>  
```
Look for:  
- `CrashLoopBackOff` errors  
- **Insufficient memory or CPU** (`OOMKilled`)  
- **Misconfigured environment variables**  

---

#### **Pending / ImagePullBackOff Pods**  
Pods get stuck in a **Pending** or **ImagePullBackOff** state due to **insufficient resources, scheduling issues, or missing container images**.  

##### **Steps to Troubleshoot:**  

1. **Check Pod Events**  

```sh
oc describe pod <pod-name> -n <namespace>  
```
Look for:  
- **No suitable worker node** (resource constraints)  
- **Scheduling issues** (taints, tolerations, or affinity rules)  

2. **Verify Image Availability in Quay**  

If the error is `ImagePullBackOff`, ensure the image exists and is accessible:


```sh
oc get pods -o wide | grep <pod-name>  
```
**Manually pull the image** to confirm availability:  
```sh
podman pull quay.io/<repo>/<image>:<tag>  
 ```

---

### **c. Managing Host Entries**  

After deploying **IBM Cloud Pak for Data (CP4D)** on **OpenShift**, updating **host entries** ensures correct **DNS resolution and service accessibility**.  

1. **Retrieve OpenShift Routes:**  

```sh
oc get routes -n <namespace>  
```

2. **Update `/etc/hosts` on the bastion host:**  

```sh
192.168.100.50  cpd.apps.example.com  
192.168.100.50  api.apps.example.com  
```
---

### **d. Configuring Application Timeouts**  

**LLM-based applications** often require **longer execution times**, and default timeout settings may cause premature **request terminations**. To prevent this, increase the **HAProxy route timeout** in OpenShift.  

**Set Timeout to 5 Minutes:**  

```sh
oc annotate route <route-name> \  
  --overwrite haproxy.router.openshift.io/timeout=5m  
```

---

### **e. Ensuring Correct Binary Versions**  

Using the correct CLI versions is **critical** when installing **OpenShift 4.15** and **Cloud Pak for Data (CP4D) 5.0.3** to **avoid compatibility issues**.  

- **OpenShift CLI (oc)** – Must match the OpenShift cluster version.  
- **CPD CLI (cpd-cli)** – Use the recommended version in the IBM documentation.  

This ensures a **smooth installation process**, avoiding **unexpected failures** or **incompatibilities**.  
