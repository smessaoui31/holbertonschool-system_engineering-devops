# Highly Available Web Infrastructure 

This README shows how to **upgrade** a simple web stack to be **more reliable** and **easier to scale** by:
- Adding **1 extra server**
- Adding **1 extra load balancer** (HAProxy) and clustering it with the first one (so there is **no single point of failure** at the entry)
- **Splitting components** so each has its **own server** (Web, App, DB)

The result is a small but realistic production-style setup that beginners can understand.

---

## What we ADD 

1) **One more Load Balancer (HAProxy)**  
   - **Why:** The previous single LB was a **Single Point of Failure (SPOF)**.  
   - With **two LBs in a cluster** (using a virtual IP), if one LB dies, the other takes over automatically.

2) **Split the stack across dedicated servers**  
   - **Web Server (Nginx) on its own server**  
     - **Why:** Serves static files, terminates HTTP/HTTPS to the app tier (or re-encrypts), and can scale independently.
   - **Application Server on its own server**  
     - **Why:** Runs your business logic. Separating it makes **deploys, scaling, and troubleshooting** easier.
   - **Database Server (MySQL) on its own server**  
     - **Why:** Databases need stable I/O and resources. Isolation improves **performance**, **security**, and **backup strategy**.

3) **One extra generic server** (if you are starting from a compact 2-box setup)  
   - **Why:** You need at least **three** separate boxes to run **web, app, and DB** as independent roles.  
   - This keeps each component cleanly separated and ready to scale on its own.

> In short: we **remove SPOFs** at the edge (LB), and we **isolate roles** (web/app/db) to make the system healthier and easier to grow.

---
