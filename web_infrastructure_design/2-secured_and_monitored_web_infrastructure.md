<p align="center">
  <img src="/web_infrastructure_design/images/secured.webp" alt="Secured" width="900" />
</p>









# Secure Three-Server Web Infrastructure (Beginner Friendly)

This README explains a **secure**, **encrypted**, and **monitored** web infrastructure for `www.foobar.com`.  
It uses **1 load balancer**, **2 web/app servers**, **3 firewalls**, **HTTPS with an SSL certificate**, and **3 monitoring clients**.

---

## 1) What we are building

- **Domain**: `foobar.com`
  - `www` DNS record → points to the **Load Balancer public IP** (A record).
- **1 Load Balancer**: HAProxy (public)
- **2 Servers**: each runs Nginx (web), Application server (your code), and MySQL
  - One MySQL acts as **Primary** (writes)
  - The other MySQL acts as **Replica** (reads)
- **Security**:
  - **3 Firewalls**:
    - **FW-LB**: in front of the Load Balancer (Internet → LB)
    - **FW-S1**: in front of Server 1 (LB → S1)
    - **FW-S2**: in front of Server 2 (LB → S2)
  - **HTTPS** with **1 SSL certificate** for `www.foobar.com` (on the Load Balancer)
- **Monitoring**:
  - **3 Monitoring clients (agents)**:
    - On the **Load Balancer**
    - On **Server 1**
    - On **Server 2**
  - They send logs/metrics to a monitoring platform (e.g., Sumo Logic, Datadog, etc.)

