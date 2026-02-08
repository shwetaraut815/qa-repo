Private EKS with Internal Application (Terraform + Kubernetes)
📌 Project Summary

This project demonstrates the design and deployment of a secure, private Amazon EKS cluster using Terraform, hosting an internal application stack (HAProxy + Tomcat) without exposing any public endpoints.

The goal was to focus on security-first design, controlled access, and production-aligned architecture, rather than a public demo setup.

🎥 Loom Walkthrough Video
https://www.loom.com/share/4bd95cbdf6e24fe7bac61065a9ed60bc

🧠 Design Approach

I intentionally designed this solution around the following principles:

Private by default
No public EKS API endpoint and no public worker nodes

Infrastructure as Code
Entire infrastructure provisioned using Terraform

Clear access boundaries
Separate mechanisms for admin access, node access, and application access

Internal-only applications
HAProxy and Tomcat exposed using ClusterIP services only

This mirrors how production Kubernetes environments are commonly designed.

🏗️ Architecture Overview

Key Architecture Decisions

Custom AWS VPC with private subnets across two AZs

Amazon EKS with private API endpoint only

Worker nodes with no public IP addresses

Dedicated Admin EC2 in the same VPC to manage the cluster

AWS SSM used for node access instead of SSH

HAProxy and Tomcat exposed via ClusterIP services

⚙️ Infrastructure Provisioning (Terraform)

Terraform was used to provision all AWS resources in a reproducible way.

Resources Created

VPC, private subnets, route tables

NAT Gateway for outbound internet access

Private Amazon EKS cluster

Managed node group

IAM roles for:

EKS control plane

Worker nodes

SSM access

📂 Terraform Folder Structure


✅ Terraform Apply Output


🔒 Worker Nodes with Private IPs Only


☸️ Kubernetes Application Deployment
Backend – Tomcat

Deployed using a Kubernetes Deployment

2 replicas for high availability

Exposed internally via a ClusterIP service

Frontend – HAProxy

Deployed as an internal load balancer

Routes traffic to the Tomcat service

Configuration managed via ConfigMap

Exposed internally via a ClusterIP service

🔁 Application Access Flow (Internal)

Because the application is internal-only, access is validated from within the cluster.

Client Pod
   ↓
HAProxy Service
   ↓
HAProxy Pod
   ↓
Tomcat Service
   ↓
Tomcat Pods (round-robin)


This confirms correct internal routing without public exposure.

🔐 Secure Access & Operations
Admin Access

A dedicated Admin EC2 instance in the same VPC is used to:

Run Terraform

Execute kubectl commands

This allows cluster administration without exposing the EKS API publicly.

Node Access (No SSH)

Worker nodes are accessed using AWS Systems Manager (SSM)

No SSH keys

No port 22

IAM-based and fully audited access




🏁 Conclusion

This project demonstrates:

Secure, private EKS architecture

Practical use of Terraform for cloud infrastructure

Internal Kubernetes service communication

Clear separation of access responsibilities

IAM-driven, audit-friendly operational access

Overall, the solution reflects a production-oriented DevOps mindset, prioritizing security, clarity, and maintainability over convenience.
