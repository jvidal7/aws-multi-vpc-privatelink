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

![AWS Multi-VPC PrivateLink Architecture](./images/aws-multi-vpc-privatelink-architecture.jpg)

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

## 3.2 Build and Configure All Three VPCs

Before configuring AWS PrivateLink, I first built the network foundation for the environment.

For this stage of the project, I created three isolated VPCs:

- **Payments VPC**
- **Analytics VPC**
- **Shared Services VPC**

Each VPC contains its own subnets, route tables, and networking components.

This provides separation between the different environments and prepares the infrastructure for PrivateLink connectivity later in the project.

---

## Step 1: Create the Payments VPC

The first environment I created was the **Payments VPC**.

### Payments VPC Configuration

- **VPC Name:** `payments-vpc`
- **IPv4 CIDR:** `10.10.0.0/16`

I created the VPC manually so I could configure each networking component individually.

![Payments VPC](./images/payments-vpc.png)

---

### Payments Subnets

Inside the Payments VPC, I created one public subnet and two private subnets.

| Subnet | CIDR |
|---|---|
| `payments-public-subnet-1` | `10.10.1.0/24` |
| `payments-private-subnet-1` | `10.10.2.0/24` |
| `payments-private-subnet-2` | `10.10.3.0/24` |

![Payments Subnets](./images/payments-subnets.png)

---
### Payments Route Table Setup

I created a dedicated public route table named:

`payments-public-rt`

The public subnet was associated with this route table.

![Payments Public Subnet Association](./images/payments-public-subnet-association.png)

---

### Payments Internet Gateway

I created:

`payments-igw`

and attached it to:

`payments-vpc`

![Payments Internet Gateway](./images/payments-internet-gateway.png)

---

### Payments Public Route

After attaching the Internet Gateway, I added the following routes to `payments-public-rt`:

| Destination | Target |
|---|---|
| `10.10.0.0/16` | `local` |
| `0.0.0.0/0` | `payments-igw` |

![Payments Public Route Table](./images/payments-public-route-table.png)

---

At this point, the **Payments VPC network configuration was complete**.

---
---

## Step 2: Create the Analytics VPC

Next, I created the **Analytics VPC**.

### Analytics VPC Configuration

- **VPC Name:** `analytics-vpc`
- **IPv4 CIDR:** `10.20.0.0/16`

![Analytics VPC](./images/analytics-vpc.png)

---

### Analytics Subnets

I created one public subnet and two private subnets.

| Subnet | CIDR |
|---|---|
| `analytics-public-subnet-1` | `10.20.1.0/24` |
| `analytics-private-subnet-1` | `10.20.2.0/24` |
| `analytics-private-subnet-2` | `10.20.3.0/24` |

![Analytics Subnets](./images/analytics-subnets.png)

---

### Analytics Route Table Setup

I created a dedicated public route table named:

`analytics-public-rt`

The public subnet was associated with this route table.

![Analytics Public Subnet Association](./images/analytics-public-subnet-association.png)

---

### Analytics Internet Gateway

I created:

`analytics-igw`

and attached it to:

`analytics-vpc`

![Analytics Internet Gateway](./images/analytics-internet-gateway.png)

---

### Analytics Public Route

After attaching the Internet Gateway, I added the following default route to `analytics-public-rt`:

| Destination | Target |
|---|---|
| `0.0.0.0/0` | `analytics-igw` |
| `10.20.0.0/16` | `local` |

![Analytics Public Route Table](./images/analytics-public-route-table.png)

---

## Step 3: Create the Shared Services VPC

The **Shared Services VPC** will host the internal application and later provide the AWS PrivateLink Endpoint Service.

### Shared Services VPC Configuration

- **VPC Name:** `shared-services-vpc`
- **IPv4 CIDR:** `10.30.0.0/16`

![Shared Services VPC](./images/shared-services-vpc.png)

---

### Shared Services Subnets

I created one public subnet and two private subnets.

| Subnet | CIDR |
|---|---|
| `shared-public-subnet-1` | `10.30.1.0/24` |
| `shared-private-subnet-1` | `10.30.2.0/24` |
| `shared-private-subnet-2` | `10.30.3.0/24` |

The private subnets were placed in separate Availability Zones.

![Shared Services Subnets](./images/shared-services-subnets.png)

---

### Shared Services Route Table Setup

I created a dedicated public route table named:

`shared-public-rt`

The public subnet was associated with this route table.

**Screenshot:** `shared-public-subnet-association.png`

![Shared Public Subnet Association](./images/shared-public-subnet-association.png)

---

### Shared Services Internet Gateway

I created:

`shared-igw`

and attached it to:

`shared-services-vpc`

**Screenshot:** `shared-services-internet-gateway.png`

![Shared Services Internet Gateway](./images/shared-services-internet-gateway.png)

---

### Shared Services Public Route

After attaching the Internet Gateway, I added the following routes to `shared-public-rt`:

| Destination | Target |
|---|---|
| `10.30.0.0/16` | `local` |
| `0.0.0.0/0` | `shared-igw` |

**Screenshot:** `shared-services-public-route-table.png`

![Shared Services Public Route Table](./images/shared-services-public-route-table.png)

---

At this point, the **Shared Services VPC network configuration was complete**.

---
## Step 4: Configure Security Groups

After building the VPCs, I created dedicated Security Groups for each environment.

| Security Group | VPC | Purpose |
|---|---|---|
| `shared-app-sg` | Shared Services VPC | Protects the internal application |
| `payments-client-sg` | Payments VPC | Protects Payments client resources |
| `analytics-client-sg` | Analytics VPC | Protects Analytics client resources |

---

### Shared Services Application Security Group

- **Security Group:** `shared-app-sg`
- **VPC:** `shared-services-vpc`
- **Port:** `80`
- **Protocol:** HTTP
- **Source:** `10.0.0.0/8`

**Screenshot:** `shared-app-security-group.png`

![Shared App Security Group](./images/shared-app-security-group.png)

---

### Payments Client Security Group

- **Security Group:** `payments-client-sg`
- **VPC:** `payments-vpc`

**Screenshot:** `payments-client-security-group.png`

![Payments Client Security Group](./images/payments-client-security-group.png)

---

### Analytics Client Security Group

- **Security Group:** `analytics-client-sg`
- **VPC:** `analytics-vpc`

**Screenshot:** `analytics-client-security-group.png`

![Analytics Client Security Group](./images/analytics-client-security-group.png)

---

## Network Configuration Summary

At the end of this stage, all three VPC environments were successfully created and configured.

| VPC | CIDR | Public Subnet | Private Subnets |
|---|---|---|---|
| **Payments** | `10.10.0.0/16` | `10.10.1.0/24` | `10.10.2.0/24`, `10.10.3.0/24` |
| **Analytics** | `10.20.0.0/16` | `10.20.1.0/24` | `10.20.2.0/24`, `10.20.3.0/24` |
| **Shared Services** | `10.30.0.0/16` | `10.30.1.0/24` | `10.30.2.0/24`, `10.30.3.0/24` |

This networking foundation prepares the environment for the **AWS PrivateLink configuration** in the next stage.

---
