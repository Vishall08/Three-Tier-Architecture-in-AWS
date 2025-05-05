# 🚀 3-Tier Architecture on AWS with RDS (Manual Setup)

## 📌 Overview

This project demonstrates how to build a **3-Tier Architecture** on AWS using a custom VPC, Subnets, EC2 instances, NAT Gateway, and Nginx with PHP backend setup. The tiers include:

* **Web Tier** (Public Subnet)
* **App Tier** (Private Subnet)
* **DB Tier** (Private Subnet, planned for RDS integration)

---

## 🧱 Architecture Components

* **Custom VPC** with CIDR Block
* **Subnets**:

  * `customVpcWebSubnet` – Public (Web Tier)
  * `customVpcAppSubnet` – Private (App Tier)
  * `customVpcDBSubnet` – Private (DB Tier)
* **Route Tables & IGW/NAT** for internet access
* **EC2 Instances** for Web and App tiers
* **Elastic IP** attached to NAT Gateway for outbound traffic from private subnet
* **Nginx + PHP-FPM** installed manually

---

## 🛠️ Step-by-Step Implementation

### 1. VPC Setup

* Created custom VPC with name `customVPC` and CIDR block
* Created subnets:

  * `customVpcWebSubnet`
  * `customVpcAppSubnet`
  * `customVpcDBSubnet`
* Assigned subnets to appropriate Availability Zones

### 2. Internet and NAT Configuration

* Created and attached Internet Gateway `customIgw` to VPC
* Created Route Table `customVpcPublicRouteTable`, added route to Internet Gateway
* Associated Web Subnet with Public Route Table
* Created NAT Gateway in Web Subnet, allocated Elastic IP
* Updated App Subnet route table to forward traffic to NAT Gateway

### 3. EC2 Instances

* Launched Web Server in `customVpcWebSubnet` with public IP
* Launched App Server in `customVpcAppSubnet` without public IP

### 4. Server Configuration

* **Web Server:**

  * Installed Nginx (`sudo yum install nginx -y`)
  * Created `index.html` in `/usr/share/nginx/html`
  * Opened port 80 in Security Group
* **App Server:**

  * Connected from Web Server via SSH using private key
  * Installed PHP-FPM and Nginx
  * Created `test.php` in `/usr/share/nginx/html` with `<?php phpinfo(); ?>`
  * Tested with `curl http://localhost/test.php`

### 5. Nginx Proxy Configuration

* On Web Server, edited `/etc/nginx/nginx.conf` to proxy `.php` requests to App Server using its private IP

```nginx
location ~ \.php$ {
    proxy_pass http://<AppServerPrivateIP>;
}
```

* Reloaded Nginx to apply changes

### 6. Security Group Configuration

* Web Server: Opened port 80 (HTTP)
* App Server: Allowed traffic on port 80 from Web Server private IP

### 7. SSH Setup

* Transferred App Server private key to Web Server using SCP
* Set correct file permission (`chmod 400`) before using it

---

## 📅 Final Result

* Web Server handles HTTP traffic
* Nginx forwards PHP requests to App Server
* PHP processing confirmed via test file
* Infrastructure modular and ready for RDS addition

---

## 📈 Future Enhancements

* Add RDS in `customVpcDBSubnet`
* Replace manual SSH/SCP with AWS Systems Manager (SSM)
* Automate deployment using Terraform or CloudFormation
* Add Load Balancer for Web Tier scalability

---

## 📷 Architecture Diagram *(To be added)*

> Optional: Include a visual representation showing Web, App, and DB tiers with route flows.

