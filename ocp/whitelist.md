---
title: Whitelisting
layout: default
parent: Network
---

# Whitelisting  

## **FOR BASTION NODE ONLY** (Over Proxy)  

### **OpenShift-Specific URLs to be Whitelisted**  

| URL | Port | Function |
|:----|:----:|:---------|
| registry.redhat.io | 443, 80 | Provides core container images. |
| access.redhat.com | 443, 80 | Hosts all container images stored in the Red Hat Ecosystem Catalog. |
| quay.io | 443, 80 | Provides core container images. |
| sso.redhat.com | 443, 80 | Used for authentication on Red Hat OpenShift Console. |
| cert-api.access.redhat.com | 443, 80 | Required for telemetry. |
| api.access.redhat.com | 443, 80 | Required for telemetry. |
| infogw.api.openshift.com | 443, 80 | Required for telemetry. |
| console.redhat.com/api/ingress, cloud.redhat.com/api/ingress | 443, 80 | Required for telemetry and insights-operator. |
| mirror.openshift.com | 443, 80 | Required for mirrored installation content and release image signatures. |
| storage.googleapis.com/openshift-release | 443, 80 | A source of release image signatures. |
| \*.apps.<Cluster_Name>.<Base_Domain> | 443, 80 | Required to access default cluster routes unless ingress wildcard is set. |
| quay-registry.s3.amazonaws.com | 443, 80 | Required to access Quay image content in AWS. |
| api.openshift.com | 443, 80 | Required for cluster token and update checks. |
| art-rhcos-ci.s3.amazonaws.com | 443, 80 | Required to download Red Hat Enterprise Linux CoreOS (RHCOS) images. |
| console.redhat.com/openshift | 443, 80 | Required for cluster token. |
| registry.access.redhat.com | 443, 80 | Required for `odo` CLI. |
| registry.connect.redhat.com | 443, 80 | Required for third-party images and certified operators. |
| oso-rhc4tp-docker-registry.s3-us-west-2.amazonaws.com | 443, 80 | Required for Sonatype Nexus, F5 Big IP operators. |
| 1.rhel.pool.ntp.org | 443, 80 | Default Red Hat Network Time Protocol (NTP) server. |
| 2.rhel.pool.ntp.org | 443, 80 | Default Red Hat Network Time Protocol (NTP) server. |
| 3.rhel.pool.ntp.org | 443, 80 | Default Red Hat Network Time Protocol (NTP) server. |

---

## **Cloud Pak URLs to be Whitelisted**  

| URL | Port | Function |
|:----|:----:|:---------|
| cp.icr.io | 443 | IBM Cloud Pak image repository. |
| cp.icr.io/cp | 443 | IBM Cloud Pak image repository. |
| dd0.icr.io/cpopen, dd2.icr.io/cpopen, dd4.icr.io/cpopen, dd6.icr.io/cpopen | 443 | Publicly available IBM images that do not require an entitlement key. |
| icr.io/Guardium-insights | 443 | IBM Guardium Insights repository. |
| cdn.quay.io/opencloudio, cdn01.quay.io/opencloudio, cdn02.quay.io/opencloudio, cdn03.quay.io/opencloudio | 443, 83 | IBM open-source images used by Cloud Pak foundational services. |
| github.com | 443 | Required for CASEs and tools. |

---

## **Internal Communication Requirements**  

### **All Cluster Nodes to All Cluster Nodes**  

| Protocol | Port | Description |
|:---------|:----:|:------------|
| ICMP | N/A | Network reachability tests. |
| TCP | 1936 | Metrics. |
| TCP | 9000-9999 | Host-level services (e.g., node exporter on 9100-9101, Cluster Version Operator on 9099). |
| TCP | 10250-10259 | Kubernetes reserved ports. |
| TCP | 10256 | OpenShift SDN. |
| UDP | 4789 | VXLAN and Geneve. |
| UDP | 6081 | VXLAN and Geneve. |
| UDP | 9000-9999 | Host-level services (e.g., node exporter on 9100-9101). |
| TCP/UDP | 30000-32767 | Kubernetes node port. |

**Source:** All nodes  
**Target:** All nodes  

---

### **All Machines to Master Nodes**  

| Protocol | Port | Description |
|:---------|:----:|:------------|
| TCP | 6443 | Kubernetes API. |

**Source:** All nodes  
**Target:** Master nodes  

---

### **Master Nodes to Master Nodes**  

| Protocol | Port | Description |
|:---------|:----:|:------------|
| TCP | 2379-2380 | etcd server and peer ports. |

**Source:** Master nodes  
**Target:** Master nodes  
