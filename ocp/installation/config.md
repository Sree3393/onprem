---
title: Install-config
layout: default
parent: Installation
nav_order: 1
---

# **Install-config**

## **Overview**  
The **install-config.yaml** file is a crucial configuration file for OpenShift installation. It defines **cluster settings**, including **DNS, node configuration, authentication**, and **image sources**.  

---

## **Key Fields in install-config.yaml**  

| **Field**                 | **Purpose**  |  
|:--------------------------|:------------|  
| `baseDomain`              | Defines the **DNS base domain** for the cluster. |  
| `controlPlane`            | Identifies **master nodes** and sets the **number of masters**. |  
| `compute`                 | Identifies **worker nodes** and sets the **number of workers**. |  
| `metadata.name`           | Specifies the **OpenShift cluster name**. |  
| `pullSecret`              | The **Red Hat pull secret** required for accessing container images. |  
| `sshKey`                  | The **SSH public key** used for accessing cluster nodes. |  
| `additionalTrustBundle`   | Ensures OpenShift **trusts a custom CA certificate** (for local registries). |  
| `imageContentSources`     | Directs OpenShift to **pull images from a local registry** instead of the internet. |  

---

## **Example install-config.yaml**  

```yaml
apiVersion: v1
baseDomain: mycluster.com
compute:
- hyperthreading: Enabled
  name: worker
  replicas: 0
controlPlane: 
  hyperthreading: Enabled
  name: master
  replicas: 3
metadata:
  name: wx
networking:
  clusterNetwork:
  - cidr: 10.128.0.0/14
    hostPrefix: 23
  networkType: OVNKubernetes
  serviceNetwork:
  - 172.30.0.0/16
platform:
  none: {}
fips: false
pullSecret: '<your_redhat_pull_secret>'
sshKey: '<your_ssh_public_key>'
additionalTrustBundle: |
  -----BEGIN CERTIFICATE-----
  <custom_ca_certificate>
  -----END CERTIFICATE-----
imageContentSources:
  - mirrors:
      - registry.local.example.com
    source: registry.redhat.io
```