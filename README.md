# Private EKS with Internal Application (Terraform + Kubernetes)

---

## 1. Project Summary

This project demonstrates the design and deployment of a **secure, private Amazon EKS cluster** using **Terraform**, running an **internal application stack (HAProxy + Tomcat)** without exposing any public endpoints.

The focus is on:
- Security-first design  
- Controlled access  
- Production-style Kubernetes architecture  

🎥 **Loom Walkthrough Video**  
https://www.loom.com/share/4bd95cbdf6e24fe7bac61065a9ed60bc

---

## 2. Design Approach

The solution was designed with the following principles:

1. **Private by default**
   - No public EKS API endpoint
   - No public IPs on worker nodes

2. **Infrastructure as Code**
   - Entire infrastructure provisioned using Terraform
   - No manual AWS console configuration

3. **Clear access boundaries**
   - Admin access separated from node access
   - Application access isolated inside the cluster

4. **Internal-only applications**
   - HAProxy and Tomcat exposed using `ClusterIP` services only

This approach aligns with real **production Kubernetes environments**.

---

## 3. Architecture Overview

![Private EKS Architecture](screenshots/private-eks-architecture.png)

### Key Architecture Decisions
- Custom AWS VPC with private subnets across two AZs
- Amazon EKS with private API endpoint only
- Worker nodes with no public IP addresses
- Dedicated Admin EC2 (same VPC) used as a control node
- AWS SSM used instead of SSH for node access
- HAProxy and Tomcat exposed via ClusterIP services

---

## 4. Infrastructure Provisioning (Terraform)

Terraform is used to provision all AWS infrastructure in a reproducible manner.

### Resources Created
- VPC, private subnets, and route tables
- NAT Gateway for outbound internet access
- Amazon EKS cluster (private endpoint)
- Managed node group
- IAM roles for:
  - EKS control plane
  - Worker nodes
  - AWS Systems Manager (SSM)

### Terraform Folder Structure
![Terraform Structure](screenshots/terraform-structure.png)

### Terraform Apply Output
![Terraform Apply](screenshots/terraform-apply.png)

### Worker Nodes (Private IP Only)
![Private Nodes](screenshots/private-nodes.png)

---

## 5. Kubernetes Application Deployment

### 5.1 Backend – Tomcat
- Deployed using a Kubernetes Deployment
- 2 replicas for high availability
- Exposed internally using a ClusterIP service

### 5.2 Frontend – HAProxy
- Deployed as an internal load balancer
- Routes traffic to the Tomcat service
- Configuration managed using a ConfigMap
- Exposed internally using a ClusterIP service

![Kubernetes Resources](screenshots/kubectl-get-all.png)

---

## 6. Application Access Flow (Internal Only)

Since the application is internal, access is validated from within the cluster.

### Traffic Flow
