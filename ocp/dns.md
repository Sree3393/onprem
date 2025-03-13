---
title: DNS
layout: default
parent: Subordinate Softwares
---

# DNS (Domain Name System) Configuration  

A properly configured **DNS (Domain Name System)** is critical for OpenShift to ensure **cluster node resolution, internal communication, and service discovery**.  

## **DNS Requirements**  

- **DNS Zone Files** – Contain mappings between **domain names and IP addresses**, enabling name resolution within the cluster.  
- **Forward & Reverse DNS** – Both are required for proper communication:  
  - **Forward DNS** (Domain Name → IP Address)  
  - **Reverse DNS** (IP Address → Domain Name)  
- **DNS Software** – OpenShift relies on **BIND DNS** or an equivalent DNS server for internal name resolution.  

## **Installation & Configuration**  

```sh
yum install bind bind-utils -y
```

### **Step 1: Create the Named Configuration File**  
Create a `named.conf` file in the following path:  

📂 **Path:** `/etc/named.conf`  

#### **Sample Configuration (`named.conf`):**  

```sh
// Provided by Red Hat bind package to configure the ISC BIND named(8) DNS
// server as a caching-only nameserver.

options {
    directory "/var/named";
    dump-file "/var/named/data/cache_dump.db";
    statistics-file "/var/named/data/named_stats.txt";
    memstatistics-file "/var/named/data/named_mem_stats.txt";
    secroots-file "/var/named/data/named.secroots";
    recursing-file "/var/named/data/named.recursing";
    allow-query { any; };

    /* Enable recursion for caching DNS servers */
    recursion yes;

    dnssec-enable yes;
    dnssec-validation yes;
    managed-keys-directory "/var/named/dynamic";

    pid-file "/run/named/named.pid";
    session-keyfile "/run/named/session.key";

    /* Fedora CryptoPolicy */
    include "/etc/crypto-policies/back-ends/bind.config";
};

logging {
    channel default_debug {
        file "data/named.run";
        severity dynamic;
    };
};

zone "." IN {
    type hint;
    file "named.ca";
};

include "/etc/named.rfc1912.zones";
include "/etc/named.root.key";

// your foward file config
zone "mycluster.com" IN {
    type master;
    file "/var/named/db.mycluster.com.zone";
};

// your reverse file config
zone "197.31.172.in-addr.arpa" IN {
    type master;
    file "/var/named/db.197.31.172.zone";
};

```



### **Step 2: Zone files**  
Create two DNS Zone files (forward and reverse) in the following path "/var/named/{filename}.zone"

#### **Sample forward file**  
```sh
$TTL 86400
@ IN SOA  mycluster.com. admin.mycluster.com. (
2024030101 ; Serial
3600 ; Refresh
1800 ; Retry
604800 ; Expire
86400 ) ; Minimum TTL

@ IN NS ns1.mycluster.com.

ns1 IN A 172.31.197.10

bastion IN A 172.31.197.10
registry IN A 172.31.197.10
bootstrap IN A 172.31.197.27
hrmaster01 IN A 172.31.197.15
hrmaster02 IN A 172.31.197.16
hrmaster03 IN A 172.31.197.17
hrworker01 IN A 172.31.197.21
hrworker02 IN A 172.31.197.22
hrworker03 IN A 172.31.197.23
hrworker04 IN A 172.31.197.24
hrworkerdb IN A 172.31.197.75
hrworker05 IN A 172.31.197.25
```

#### **Sample Reverse file** 

```sh
$TTL 86400
@ IN SOA ns1.mycluster.com. admin.mycluster.com. (
2024030101 ; Serial
3600 ; Refresh
1800 ; Retry
604800 ; Expire
86400 ) ; Minimum TTL
)

@ IN NS ns1.mycluster.com.

10 IN PTR bastion.mycluster.com.
10 IN PTR registry.mycluster.com.
27 IN PTR bootstrap.mycluster.com.
15 IN PTR hrmaster01.mycluster.com.
16 IN PTR hrmaster02.mycluster.com.
17 IN PTR hrmaster03.mycluster.com.
21 IN PTR hrworker01.mycluster.com.
22 IN PTR hrworker02.mycluster.com.
23 IN PTR hrworker03.mycluster.com.
24 IN PTR hrworker04.mycluster.com.
25 IN PTR hrworker05.mycluster.com.
75 IN PTR hrworker06.mycluster.com.
```

## **Start the bind service using command**  

```sh
systemctl start named
```

## **To check the status of the service**  

```sh
systemctl status named
```

