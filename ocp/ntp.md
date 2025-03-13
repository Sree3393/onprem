---
title: NTP
layout: default
parent: Subordinate Softwares
---

# **Network Time Protocol (NTP)**  

## **Why Use NTP?**  
NTP (Network Time Protocol) ensures **accurate time synchronization** across all cluster nodes, which is crucial for:  
- Maintaining **consistent timestamps** across logs and events.  
- Preventing **clock drift**, which can impact authentication and distributed services.  
- Synchronizing time across **masters, workers, and supporting services** in OpenShift deployments.  

---

## **Installing and Configuring NTP**  

### **1. Install NTP**  
Run the following command to install the **NTP package**:  

```bash
yum install -y chrony
```


### **2. Enable and Start NTP Service**  
Once installed, enable the **Chrony NTP service** and start it:  

```bash
systemctl enable chronyd systemctl start chronyd
```


### **3. Verify Synchronization Status**  
To check if NTP is working and synchronizing correctly, run:  

```bash
chronyc tracking chronyc sources -v
```


---

## **Configuring Custom NTP Servers**  
To specify **custom NTP servers**, edit the Chrony configuration file:  

```bash
vi /etc/chrony.conf
```


Modify or add the following lines with **your preferred NTP servers**:  
```bash
server 0.rhel.pool.ntp.org iburst 
server 1.rhel.pool.ntp.org iburst 
server 2.rhel.pool.ntp.org iburst 
server 3.rhel.pool.ntp.org iburst
```


Save and exit, then restart the service:  
```bash
systemctl restart chronyd
```


---

## **Allowing NTP Traffic Through Firewall**  
Ensure NTP communication is not blocked by the firewall:  
```bash
firewall-cmd --permanent --add-service=ntp firewall-cmd --reload
```


---

## **Verifying Time Sync on Nodes**  
After completing the setup, verify time synchronization on all cluster nodes:  
```bash
timedatectl
```


A properly configured NTP setup ensures that **OpenShift and its dependent services run smoothly** without time-related inconsistencies. 
