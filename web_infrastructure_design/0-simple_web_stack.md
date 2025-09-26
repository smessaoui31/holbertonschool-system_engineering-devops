<p align="center">
  <img src="/web_infrastructure_design/images/1*iBstDyO1wFNbfs3X70Jy7w.webp" alt="Webstackserver" width="800" />
</p>

# Simple Web Infrastructure

This project describes a simple one-server web infrastructure that hosts the website `www.foobar.com`, served at IP **8.8.8.8** using a classic **LAMP-like stack**.

---

## Architecture Overview

- **Domain name:** `foobar.com`  
  - `www` record pointing to **8.8.8.8**  
  - Since it points directly to an IP, `www` is an **A record** (not a CNAME).

- **1 server (Linux) running:**
  - **Nginx** (Web server)
  - **Application server** (e.g., PHP-FPM for PHP, Gunicorn/uWSGI for Python, Node.js runtime, etc.)
  - **Application files** (your codebase)
  - **MySQL** (Database)

---

## Request Flow

1. User enters `https://www.foobar.com/` in the browser.  
2. **DNS resolution:**  
   - The client resolves `www.foobar.com` → **8.8.8.8** (A record).  
3. **TCP/TLS connection:**  
   - Browser connects to **8.8.8.8** on port **443** (HTTPS) or **80** (HTTP).  
   - (Best practice: redirect HTTP → HTTPS.)  
4. **Nginx (Web server)** receives the request:  
   - If the path matches **static assets**, Nginx serves them directly from disk.  
   - Otherwise, Nginx **proxies the request** to the Application server:  
     - PHP → via FastCGI to php-fpm (e.g., `unix:/run/php/php-fpm.sock`).  
     - Python/Node/etc. → via HTTP to a local port (e.g., `127.0.0.1:8000`).  
5. **Application server** runs the application code, which may:  
   - Query **MySQL** on port **3306** (localhost).  
   - Read configuration, templates, and business logic.  
6. **Response** travels back: Application server → Nginx → Browser.  

---

## Components Explained

- **Server**  
  A physical or virtual machine (or program) that provides functionality/services to clients (e.g., browsers).

- **Domain name**  
  A human-readable alias that maps to an IP address so users can reach your server easily.

- **DNS record for `www` in `www.foobar.com`**  
  An **A record**, pointing `www.foobar.com` → **8.8.8.8**.

- **Web server (Nginx)**  
  Terminates HTTP/HTTPS, serves static files efficiently, handles TLS, routing, compression, caching, and reverse-proxies dynamic requests to the application server.

- **Application server**  
  Executes your application code (business logic, templates, APIs).  
  Examples: **PHP-FPM**, **Gunicorn/uWSGI**, **Node.js** runtime.

- **Database (MySQL)**  
  Stores and retrieves structured data reliably (e.g., users, orders, posts).

- **Server-to-user communication**  
  **TCP/IP** with **HTTP/HTTPS** (HTTPS recommended: TLS encryption over port 443).

---

## Issues with this Infrastructure

- **SPOF (Single Point of Failure):**  
  If the server fails, the entire service goes down (DNS still resolves, but there’s no service).

- **Downtime for maintenance/deploys:**  
  Restarting Nginx, redeploying code, or applying DB migrations may cause visible downtime.

- **No scalability under high traffic:**  
  A single server limits CPU/RAM/IO. Traffic spikes can saturate the machine (slow pages, timeouts).

---

## Future Improvements (Mitigations)

If scaling is needed later:
- Add a **load balancer** + multiple application servers.  
- Separate database host(s) with replication.  
- Use **managed DNS** with health checks.  
- CI/CD pipeline for **zero-downtime deploys**.  
- Add **caching layers / CDN** for static assets.

---