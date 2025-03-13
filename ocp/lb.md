---
title: Load Balancer
layout: default
parent: Subordinate Softwares
---

# Load Balancer  

## **Why is a Load Balancer Needed?**  
A **Load Balancer (LB)** is a critical component for **OpenShift** and cloud-based architectures. It ensures:  

- **High Availability:** Prevents downtime by distributing traffic across multiple nodes.  
- **Fault Tolerance:** If a node fails, traffic is automatically redirected to healthy nodes.  
- **Optimized Performance:** Balances workloads to prevent any single node from becoming overloaded.  
- **Scalability:** Supports dynamic scaling of resources based on demand.  
- **Security:** Helps prevent **DDoS attacks** and provides SSL termination for secure communication.  

In OpenShift deployments, a **Load Balancer** manages traffic for essential services such as the **API server, machine configuration server, and application ingress controllers**.  

---

## **Installation**  

```sh
yum install haproxy -y
```

## **Load Balancer Configuration**  

To configure a **Load Balancer** using **HAProxy**, create a configuration file named **`haproxy.cfg`** in the following path:  

📂 **Path:** `/etc/haproxy/haproxy.cfg`  

### **Sample HAProxy Configuration (`haproxy.cfg`)**  

```bash
#---------------------------------------------------------------------
# Global settings
#---------------------------------------------------------------------
global
# to have these messages end up in /var/log/haproxy.log you will
# need to:
#
# 1) configure syslog to accept network log events. This is done
# by adding the '-r' option to the SYSLOGD_OPTIONS in
# /etc/sysconfig/syslog
#
# 2) configure local2 events to go to the /var/log/haproxy.log
# file. A line like the following can be added to
# /etc/sysconfig/syslog
#
# local2.* /var/log/haproxy.log
#
log 127.0.0.1 local2

chroot /var/lib/haproxy
pidfile /var/run/haproxy.pid
maxconn 4000
user haproxy
group haproxy
daemon

# turn on stats unix socket
stats socket /var/lib/haproxy/stats

# utilize system-wide crypto-policies
ssl-default-bind-ciphers PROFILE=SYSTEM
ssl-default-server-ciphers PROFILE=SYSTEM

#---------------------------------------------------------------------
# common defaults that all the 'listen' and 'backend' sections will
# use if not designated in their block
#---------------------------------------------------------------------
defaults
mode http
log global
option httplog
option dontlognull
option http-server-close
option forwardfor except 127.0.0.0/8
option redispatch
retries 3
timeout http-request 10s
timeout queue 1m
timeout connect 10s
timeout client 1m
timeout server 1m
timeout http-keep-alive 10s
timeout check 10s
maxconn 3000

listen stats
bind :9000
mode http
stats enable
stats uri /
monitor-uri /healthz

#---------------------------------------------------------------------
# main frontend which proxys to the backends
#---------------------------------------------------------------------

frontend openshift-api-server
bind *:6443
default_backend openshift-api-server
mode tcp
option tcplog

frontend machine-config-server
bind *:22623
default_backend machine-config-server
mode tcp
option tcplog

frontend ingress-http
bind *:80
default_backend ingress-http
mode tcp
option tcplog

frontend ingress-https
bind *:443
default_backend ingress-https
mode tcp
#---------------------------------------------------------------------
# round robin balancing between the various backends
#---------------------------------------------------------------------

backend openshift-api-server
balance source
mode tcp
server bootstrap.mycluster.com 172.31.197.27:6443 check
# server registry.mycluster.com 172.31.197.10:6443 check
server hrmaster01.mycluster.com 172.31.197.15:6443 check
server hrmaster02.mycluster.com 172.31.197.16:6443 check
server hrmaster03.mycluster.com 172.31.197.17:6443 check

backend machine-config-server
balance source
mode tcp
server bootstrap.mycluster.com 172.31.197.27:22623 check
server hrmaster01.mycluster.com 172.31.197.15:22623 check
server hrmaster02.mycluster.com 172.31.197.16:22623 check
server hrmaster03.mycluster.com 172.31.197.17:22623 check

backend ingress-http
balance source
mode tcp
server hrworker01.mycluster.com 172.31.197.21:80 check
server hrworker02.mycluster.com 172.31.197.22:80 check
server hrworker03.mycluster.com 172.31.197.23:80 check
server hrworker04.mycluster.com 172.31.197.24:80 check
server hrworker05.mycluster.com 172.31.197.25:80 check
server hrworkerdb.mycluster.com 172.31.197.75:80 check


backend ingress-https
balance source
mode tcp
server hrworker01.mycluster.com 172.31.197.21:443 check
server hrworker02.mycluster.com 172.31.197.22:443 check
server hrworker03.mycluster.com 172.31.197.23:443 check
server hrworker04.mycluster.com 172.31.197.24:443 check
server hrworker05.mycluster.com 172.31.197.25:443 check
server hrworkerdb.mycluster.com 172.31.197.75:443 check
```


## **Start the bind haproxy service using command**  

```sh
systemctl start haproxy.service
```

## **To  autostart on next boot**  

```sh
systemctl enable haproxy.service
```

## **To  check status**  

```sh
systemctl status haproxy.service
```