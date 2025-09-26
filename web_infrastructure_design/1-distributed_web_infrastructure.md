<p align="center">
  <img src="/web_infrastructure_design/images/Network_Architecture_Diagram_-_Distributed_Web_Infrastructure.pdf.jpg" alt="NetworkArchitecture" width="600" />
</p>


# Web Infrastructure Designs

This README explains two web infrastructures for hosting the website `www.foobar.com`:

1. **Single-server setup** (basic, simple)  
2. **Three-server setup** (with load balancer and redundancy)  

The goal is to understand how websites are hosted and how the design evolves to improve reliability, scalability, and security.

---

## 1. Single-Server Infrastructure

### Architecture

- **Domain name:** `foobar.com`  
  - `www` record → points to IP **8.8.8.8**  
  - `www` is an **A record**

- **1 Server (Linux)** running:  
  - **Nginx** (web server)  
  - **Application server** (PHP-FPM, Gunicorn, Node.js, etc.)  
  - **Codebase** (the app files)  
  - **MySQL database**

### Diagram

```mermaid
graph LR
    U[User's Browser] -->|DNS www.foobar.com -> 8.8.8.8| S[Server 8.8.8.8]
    S --> N[Nginx (Web Server)]
    N --> A[Application Server + Codebase]
    A --> D[(MySQL Database)]

Request Flow
	1.	User enters https://www.foobar.com/.
	2.	DNS resolves www.foobar.com → Load Balancer IP.
	3.	HAProxy forwards request to Server 1 or Server 2 (e.g., round robin).
	4.	Nginx serves static files or forwards to Application server.
	5.	Application may query MySQL (writes to Primary, reads from Replica).
	6.	Response returns: App → Nginx → HAProxy → Browser.

Key Concepts
	•	Load balancer: avoids putting all users on one server, improves reliability.
	•	Round robin: sends each request to the next server in turn.
	•	Active-Active setup: both servers handle traffic at the same time.
	•	Database Primary-Replica:
	•	Primary = accepts writes
	•	Replica = copies data from Primary, used for reads

Issues
	•	SPOF: load balancer is a SPOF; Primary DB is also a SPOF for writes.
	•	Security: no HTTPS, no firewall.
	•	No monitoring: cannot detect failures or performance issues.

Improvements
	•	Add a second load balancer for redundancy.
	•	Secure with HTTPS and firewalls.
	•	Add monitoring tools (Prometheus, Grafana, uptime checks).
	•	Make the app stateless so servers can scale easily.

⸻

Conclusion
	•	The single-server setup is simple but fragile.
	•	The three-server setup adds reliability and scalability but still has weak points (SPOFs, no security, no monitoring).
	•	Real-world infrastructures go further: multiple load balancers, dedicated database clusters, HTTPS everywhere, monitoring and alerts.

