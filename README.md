

# DevOps Inventory Management System (AWS Deployment)

This project focuses on the **Solutions Architecture** and **Cloud Deployment** of a full-stack inventory management dashboard. The goal of this repository is to demonstrate a production-grade AWS infrastructure setup, encompassing networking, secure database management, and scalable compute services.

## Architecture Overview

The application follows a traditional 3-tier architecture deployed on AWS:

* **Frontend Tier:** Next.js application hosted on **AWS Amplify** for global scaling and managed CI/CD.
* **Application Tier:** Node.js/Express backend running on an **Amazon EC2 (T2 Micro)** instance within a Public Subnet.
* **Database Tier:** **Amazon RDS (PostgreSQL)** instance located in a Private Subnet for maximum security.
* **Storage Tier:** **Amazon S3** for persistent storage of product images and assets.
* **Connectivity:** **Amazon API Gateway** acts as the secure bridge between the frontend and the backend EC2 instance, providing HTTPS termination.

## 🌐 Networking Details (VPC Configuration)

The infrastructure is built within a custom Virtual Private Cloud (VPC) to ensure complete isolation and control:

* **CIDR Block:** `10.0.0.0/16`
* **Public Subnet:** Hosts the EC2 backend. Associated with an **Internet Gateway** and a Public Route Table [[06:03:51](http://www.youtube.com/watch?v=ddKQ8sZo_v8&t=21831)].
* **Private Subnets:** Two subnets in different Availability Zones (Multi-AZ) to host the RDS database [[06:31:56](http://www.youtube.com/watch?v=ddKQ8sZo_v8&t=23516)].
* **Security Groups:**
* **Public SG (EC2):** Allows inbound HTTP/HTTPS and SSH for administration [[06:13:27](http://www.youtube.com/watch?v=ddKQ8sZo_v8&t=22407)].
* **Private SG (RDS):** Restricts inbound traffic strictly to the EC2 security group on Port 5432 [[06:37:15](http://www.youtube.com/watch?v=ddKQ8sZo_v8&t=23835)].



## 🚀 Deployment Steps

### 1. Database Setup (RDS)

* Deploy a PostgreSQL instance using the **Free Tier** template 
* Configure a **DB Subnet Group** using the two private subnets.
* Initialize the schema using **Prisma** migrations from the EC2 instance 

### 2. Backend Compute (EC2)

* Launch an Amazon Linux 2023 instance
* Install environment dependencies: **Node.js (NVM)** and **Git** 
* Utilize **PM2** (Process Manager) to ensure the Node.js server remains running after reboots or crashes.

### 3. API Gateway Integration

* Create an **HTTP API** to route requests from the Amplify frontend to the EC2 public IP 
* Enables HTTPS communication for the frontend to avoid "Mixed Content" security blocks 

### 4. Static Assets (S3)

* Bucket created for product images with **Public Access** enabled specifically for the asset folder 
* Configured **Bucket Policies** to allow `s3:GetObject` for public image rendering 

### 5. Frontend & CI/CD (Amplify)

* Connected the GitHub repository to **AWS Amplify** 
* Configured build settings to target the `/client` directory in the monorepo 
* Environmental variables mapped to the API Gateway Invoke URL 

## 🛠️ Tech Stack

* **Cloud:** AWS (EC2, RDS, S3, VPC, API Gateway, Amplify)
* **Backend:** Node.js, Express, Prisma ORM
* **Frontend:** Next.js, Tailwind CSS, Redux Toolkit Query
* **Database:** PostgreSQL
* **DevOps:** Git, PM2

## 🔒 Security Best Practices Implemented

* **Least Privilege:** Security groups are scoped to specific ports and source IDs 
* **Data Isolation:** Database is not reachable from the public internet 
* **Secure Secrets:** Database credentials managed via `.env` files (simulating AWS Secrets Manager behavior).
* **HTTPS:** Encrypted transit for all user-facing data via API Gateway.

---

*This README was generated based on the project architecture detailed in [EdRoh's project tutorial](https://www.google.com/search?q=https://youtu.be/ddKQ8sZo_v8).*

---

That’s a pro-level move. In the software engineering world, we call this **decoupling**. By splitting these into focused retrospective files, you're making your repository much easier to navigate for recruiters or colleagues who want to dive deep into a specific technical challenge without being overwhelmed.

Here is a clean, professional summary you can put in your main **README.md** (or a top-level `/docs/README.md`) that links to the individual files.

---

## 🛠️ Technical Retrospectives

Here I have maintained detailed documentation of the architectural challenges and infrastructure optimizations encountered during this project. These retrospectives serve as a deep dive into my engineering process and problem-solving methodology.

### 📂 [Retrospective 1: Solving CI/CD Build Failures](./docs/technical_retrospective/TR_build_failures)

**Focus:** *Environment Parity & Transpilation Logic*
This doc covers the investigation into a "Leaky Abstraction" where a TypeScript-based Tailwind configuration failed in the AWS Amplify headless Linux environment.

* **Key Solution:** Refactored configuration to CommonJS and aligned Node.js runtimes with AWS-supported LTS versions.
* **Outcome:** Established a deterministic build pipeline and successfully deployed a Next.js monorepo.

### 📂 [Retrospective 2: AWS Cost Optimization & "Ghost" API Mitigation](./docs/technical_retrospective/TR_optimizing_costs_eliminating_ghost_api_usage)

**Focus:** *Cloud Economics & Infrastructure Hardening*
An engineering audit of the AWS Billing dashboard revealed unauthorized API usage in the N. Virginia region. This document traces that usage back to Amplify Gen 2's auto-discovery mechanisms.

* **Key Solution:** Implemented the `AMPLIFY_SKIP_BACKEND_BUILD` flag to muzzle unnecessary backend scanning.
* **Outcome:** Reduced build times by 15-20s, eliminated "ghost" API costs, and hardened the security surface of the build container.
