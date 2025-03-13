---
title: HTTPD
layout: default
parent: Subordinate Softwares
---

# **HTTPD Server**  

## **Why Use HTTPD?**  
**HTTPD (Apache HTTP Server)** is a widely used web server that facilitates **hosted file sharing** over HTTP/HTTPS protocols. In our setup, **HTTPD** will be used for:  
- Storing and serving **Ignition files** for OpenShift installations.  
- Hosting **RHCOS (Red Hat CoreOS) images** for cluster provisioning.  
- Providing a **lightweight, scalable, and secure** web-based file server.  

---

## **Installing HTTPD**  
To install HTTPD on your system, run the following command:  

```bash
yum install httpd -y
```



## **Start the httpd service using command**  

```sh
systemctl start httpd
```

## **To enable httpd service**  

```sh
systemctl enable httpd
```

## **Configuring HTTPD for OpenShift**
```sh
mkdir -p /var/www/html
chmod -R 755 /var/www/html
```

## **Verify HTTPD is running correctly**
```sh
systemctl status httpd
```