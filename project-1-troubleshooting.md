# Project 1: EC2 Web Server Deployment & Troubleshooting

This project documents my first hands-on experience deploying and troubleshooting a web server on AWS. Beyond simply launching infrastructure, I intentionally introduced failures to understand how cloud systems break and how to diagnose issues methodically.

This helped me move from “I built it” to **“I understand why it works — and why it fails.”**

---

## Initial Deployment

I launched an EC2 instance running **Amazon Linux 2023**, installed **NGINX**, and deployed a static website accessible through the instance’s public IP.

![Website Working](screenshots/website-working.png)

---

## Experiment 1: Breaking HTTP Access (Network-Level Failure)

I removed **port 80 (HTTP)** from the EC2 security group inbound rules.

### Symptom
The website became unreachable in the browser.

![Browser HTTP Timeout](screenshots/browser-http-timeout.png)

### Investigation
I verified that:
- The EC2 instance was still running
- NGINX was still installed
- HTTP traffic was no longer allowed in the security group

![HTTP Rule Removed](screenshots/security-group-http-removed.png)

### Root Cause
Inbound HTTP traffic was blocked at the security group level.

### Resolution
I re-added port 80 to the inbound rules, restoring public access.

---

## Experiment 2: Breaking SSH Access (Access-Level Failure)

Next, I removed **port 22 (SSH)** from the security group.

### Symptom
SSH attempts hung and eventually timed out.

![SSH Timeout](screenshots/ssh-timeout.png)

### Key Learning
- SSH is strictly for **administrative access**
- Blocking SSH does **not** affect website availability
- A timeout indicates traffic is being blocked before reaching the instance

### Resolution
After restoring port 22, SSH access was immediately restored.

![SSH Restored](screenshots/ssh-restored-login.png)

---

## Experiment 3: Stopping NGINX (Service-Level Failure)

I then stopped the NGINX service directly on the instance.

```bash
sudo systemctl stop nginx
sudo systemctl status nginx


After stopping NGINX, the website became unreachable from the browser even though:

- The EC2 instance was still running  
- Port 80 (HTTP) was still allowed in the security group  
- Network configuration remained unchanged  

This indicated that the failure was **service-related**, not a networking issue.

### Verifying the Service Locally with `curl`

To confirm whether the issue was internal to the server or related to external access, I tested the web server locally from within the EC2 instance:

```bash
curl -I http://localhost
