# Web Infrastructure Design

This document outlines the design and explanation of three progressive web infrastructure setups for hosting the website **www.foobar.com**.

---

## **0. Simple Web Stack (One Server)**

### **Overview**
A basic one-server infrastructure that hosts both the application and database, using:
- **1 Server**
- **1 Web Server (Nginx)**
- **1 Application Server**
- **1 MySQL Database**
- **Application Files**
- **Domain:** `foobar.com` with a `www` A record pointing to `8.8.8.8`

### **How It Works**
1. User requests `www.foobar.com`.
2. DNS resolves `www` A record → IP `8.8.8.8`.
3. Nginx handles HTTP/HTTPS request.
4. Static files served directly; dynamic requests forwarded to Application Server.
5. Application Server queries MySQL Database if needed.
6. Response sent back to the client.

### **Roles**
- **Server:** Physical/virtual machine running app and database.
- **Domain Name:** Human-readable address for the website.
- **DNS Record (A record):** Maps `www.foobar.com` to IP address.
- **Web Server (Nginx):** Handles requests, serves static content, passes dynamic requests to app server.
- **Application Server:** Runs backend code.
- **Database (MySQL):** Stores persistent data.
- **Communication Protocol:** HTTP/HTTPS over TCP/IP.

### **Issues**
- Single Point of Failure (SPOF)
- Downtime during maintenance
- No scalability for high traffic

---

## **1. Distributed Web Infrastructure (Three Servers)**

### **Overview**
A fault-tolerant and scalable setup with multiple servers:
- **2 Servers** (Web + App)
- **1 Load Balancer (HAProxy)**
- **1 Web Server (Nginx)**
- **1 Application Server**
- **1 MySQL Database**
- **Application Files**

### **How It Works**
1. DNS resolves `www.foobar.com` → Load Balancer IP.
2. Load Balancer distributes requests across backend servers.
3. Web Server handles static content; forwards dynamic requests to Application Server.
4. Application Server queries the database and returns results.

### **Load Balancer**
- **Algorithm:** Round Robin (distributes requests evenly across servers).
- **Setup:** Active-Active (all servers handle traffic).

### **Database**
- **Primary-Replica Setup:**  
  - Primary handles writes.
  - Replica handles reads.
  - Replica updates via replication from Primary.

### **Issues**
- SPOF: Load Balancer and Primary DB.
- No firewall or HTTPS (security risk).
- No monitoring (failures may go unnoticed).

---

## **2. Secured & Monitored Web Infrastructure**

### **Overview**
An enhanced infrastructure with security and monitoring:
- **3 Servers**
- **1 Load Balancer (HAProxy)**
- **1 Web Server (Nginx)**
- **1 Application Server**
- **1 MySQL Database**
- **3 Firewalls**
- **1 SSL Certificate**
- **3 Monitoring Clients** (e.g., Datadog, Prometheus)

### **Security Features**
- **Firewalls:**
  - Firewall 1: Between internet and Load Balancer.
  - Firewall 2: Between Load Balancer and App/Web servers.
  - Firewall 3: Between App Server and Database.
- **HTTPS via SSL Certificate:** Encrypts traffic between clients and server.

### **Monitoring**
- **Purpose:** Detect issues, measure performance, generate alerts.
- **How It Works:** Agents collect metrics/logs and send them to a central monitoring service.
- **Example:** Monitor Web Server QPS by parsing Nginx logs and sending metrics to dashboard.

### **Issues**
- SSL termination at Load Balancer means backend traffic is unencrypted.
- Single MySQL write node → write downtime if it fails.
- Servers with identical components may be vulnerable to the same attack.

---

## **Summary Table**

| Feature                | Simple Stack       | Distributed Infra  | Secured & Monitored Infra |
|------------------------|-------------------|-------------------|---------------------------|
| # of Servers           | 1                 | 3                 | 3                         |
| Load Balancer          | No                | Yes               | Yes                       |
| Web Server             | Yes               | Yes               | Yes                       |
| Application Server     | Yes               | Yes               | Yes                       |
| Database               | 1 MySQL           | Primary + Replica | Primary + Replica         |
| HTTPS                  | No                | No                | Yes                       |
| Firewalls              | No                | No                | 3                         |
| Monitoring             | No                | No                | Yes                       |
| SPOF                   | Yes               | Yes               | Reduced                   |
| Scalability            | Low               | Medium            | Medium-High               |

---

**Author:** Your Name  
**Project:** Web Infrastructure Design
