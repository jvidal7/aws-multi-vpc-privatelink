# aws-multi-vpc-privatelink
Secure multi-VPC AWS architecture using PrivateLink, Interface Endpoints, an internal NLB, and private EC2 services.

# AWS Multi-VPC PrivateLink Architecture

---

## 3.1 Project Overview

### Overview of Project

---

### Scenario

A growing fintech company runs multiple internal systems:

- **Payments** — Handles customer transactions
- **Analytics** — Runs reports and business insights
- **Shared Services** — Hosts internal APIs used by other teams

For security and compliance, each system lives in its **own VPC**.

However, the Payments and Analytics teams still need to call shared internal APIs without:

- Exposing anything to the public internet
- Managing complex VPC peering meshes
- Opening SSH or public endpoints to backend services

The company wants a **clean, private-only connectivity model** where shared services are consumed through **AWS PrivateLink** and internal traffic remains within the AWS network.

---

### My Role as the Cloud Engineer

In this project, I will design and implement a **multi-VPC AWS architecture** where:

- Each application is isolated inside its own **VPC and subnets**
- A **Shared Services VPC** hosts a private internal web application
- The **Payments** and **Analytics** VPCs consume the application through **AWS PrivateLink**
- No public IP addresses or direct internet access are required for internal application traffic
- **Security Groups** and network configuration enforce **least-privilege access**

This architecture demonstrates how enterprises can securely expose internal platform services to other teams while maintaining strong network isolation.

---

### Our Solution

The environment will contain **three VPCs**:

1. **Payments VPC**
2. **Analytics VPC**
3. **Shared Services VPC**

The Shared Services VPC will host a private internal web application.

The application will be exposed through an **internal Network Load Balancer (NLB)** and an **AWS PrivateLink VPC Endpoint Service**.

The Payments and Analytics VPCs will connect to the shared service using **Interface VPC Endpoints**.

This allows both client VPCs to access the service privately without requiring:

- VPC Peering
- NAT Gateways for application traffic
- Internet Gateways for application traffic
- Public IP addresses
- Public-facing backend services

---

## About the Project

In this hands-on project, I will:

- Create **three isolated VPCs** with public and private subnets
- Create **Security Groups** for the Shared Services application and client VPCs
- Deploy a **private-only EC2 instance** running an internal web application
- Place the application behind an **internal Network Load Balancer**
- Create a **VPC Endpoint Service** for the Network Load Balancer
- Create **Interface Endpoints** in the Payments and Analytics VPCs
- Approve PrivateLink endpoint connections
- Test private-only connectivity between the client VPCs and the Shared Services application

By the end of the project, the environment will demonstrate a **production-style multi-VPC PrivateLink architecture** used to securely share internal services across isolated networks.

---

## Steps To Be Performed 👩‍💻

The project will be completed through the following major steps:

1. Build three isolated VPCs:
   - Payments
   - Analytics
   - Shared Services

2. Configure subnets, routing, and network components

3. Create Security Groups for:
   - Shared Services application
   - Payments client
   - Analytics client

4. Deploy the **Shared Services internal web application** on a private EC2 instance

5. Place the application behind an **internal Network Load Balancer**

6. Create an **AWS PrivateLink VPC Endpoint Service**

7. Create **Interface Endpoints** in:
   - Payments VPC
   - Analytics VPC

8. Approve endpoint connections

9. Test **private-only access** to the internal service

---

## Services Used 🛠

| AWS Service | Purpose |
|---|---|
| **Amazon VPC** | Provides isolated networks for Payments, Analytics, and Shared Services |
| **Subnets** | Separates resources into different network segments |
| **Route Tables** | Controls traffic flow within each VPC |
| **Internet Gateways** | Provides internet connectivity where required |
| **Amazon EC2** | Hosts the private internal web application |
| **Security Groups** | Provides stateful network access control |
| **Network Load Balancer (NLB)** | Distributes traffic to the internal application |
| **AWS PrivateLink** | Provides private service connectivity between VPCs |
| **VPC Endpoint Service** | Exposes the Shared Services application through PrivateLink |
| **Interface VPC Endpoints** | Allows Payments and Analytics to privately consume the shared service |

---

## Architectural Diagram

The following diagram represents the planned architecture:

![AWS Multi-VPC PrivateLink Architecture](aws-multi-vpc-privatelink-architecture.jpg)

---

## Final Result

At the end of this project, the environment will include:

- **Three isolated VPCs**
  - Payments
  - Analytics
  - Shared Services

- A private internal service hosted inside the **Shared Services VPC**

- An application that is **not directly exposed to the public internet**

- An **internal Network Load Balancer**

- An **AWS PrivateLink Endpoint Service**

- **Interface Endpoints** inside the Payments and Analytics VPCs

- Working private connectivity between the client VPCs and the Shared Services application

- A real-world example of how enterprises securely share internal APIs across isolated AWS environments

![PrivateLink Final Result](https://uploads.teachablecdn.com/attachments/eba00f26b1704ffb940a8ef5367bcea7.png)

---

## Key Security Concepts Demonstrated

This project demonstrates several important cloud security concepts:

- **Network segmentation**
- **Private service connectivity**
- **Least-privilege network access**
- **VPC isolation**
- **Reduction of public attack surface**
- **AWS PrivateLink**
- **Interface VPC Endpoints**
- **Internal load balancing**
- **Secure cross-VPC service consumption**

---
