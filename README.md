# Monroe Redstone Technology Group — IAM Lab Series

![IAM](https://img.shields.io/badge/IAM-Enterprise-blue)
![Active Directory](https://img.shields.io/badge/Directory-Active_Directory-2A628C)
![Security](https://img.shields.io/badge/Security-Policy_&_Access_Control-red)
![Platform](https://img.shields.io/badge/Platform-Windows_Enterprise-lightgrey)
![Focus](https://img.shields.io/badge/Focus-Identity_Governance-purple)

---

This project simulates a structured enterprise Identity and Access Management (IAM) environment for Monroe Redstone Technology Group (MRTG).

The lab series demonstrates how identity infrastructure is deployed, governed, and secured using Active Directory, with emphasis on policy enforcement, role-based access control (RBAC), and auditability within regulated environments.

Core implementation areas:

- Role-Based Access Control (RBAC)
- Organizational Unit (OU) design
- Group Policy enforcement
- Access control via security groups
- Identity lifecycle governance
- Monitoring and auditing

The focus is on practical IAM operations aligned with enterprise and government environments, emphasizing least privilege, centralized access control, and auditability.

---

## Objectives

- Design, implement, and secure an enterprise identity environment  
- Deploy Active Directory Domain Services (AD DS)  
- Enforce identity-based access control  
- Validate policy enforcement across domain systems  
- Align identity operations with governance-oriented IT practices  

---

## Organization

This repository represents a structured IAM implementation for:

**Monroe Redstone Technology Group (MRTG)**

Core identity components include:

- Active Directory Domain Services (AD DS)  
- Domain-joined endpoints  
- Organizational Unit (OU) hierarchy  
- Role-Based Access Control (RBAC)  
- Group Policy-based security enforcement  
- Hybrid identity preparation (future Entra ID integration)  
- Identity monitoring and validation  

---

## Domain

- **Domain Name:** mrtg.local  
- **Directory Services:** Active Directory Domain Services (AD DS)  
- **Authentication Model:** Kerberos-based domain authentication  

---

## Systems

### MRTG-DC01 — Primary Domain Controller

- Active Directory Domain Services
- DNS
- Group Policy Management
- Global Catalog
- Replication partner for MRTG-DC02

### MRTG-DC02 — Additional Domain Controller

- Active Directory Domain Services
- DNS
- Global Catalog
- AD replication validation
- Directory resilience support

### MRTG-CLIENT-01 — Domain-Joined Workstation

- Policy enforcement validation
- Authentication testing
- Access control validation

---

## Infrastructure Architecture

| Component | Description |
|---|---|
| Hypervisor | Hyper-V (Windows 11 Pro Host) |
| Primary Domain Controller | MRTG-DC01 — Windows Server 2022 |
| Additional Domain Controller | MRTG-DC02 — Windows Server 2022 |
| Services | AD DS, DNS, Group Policy, Global Catalog, AD Replication |
| Client System | MRTG-CLIENT-01 — Windows 11 Enterprise |
---

## Identity Architecture

Authentication and authorization are centralized through Active Directory Domain Services (AD DS).

Access control is enforced through:

- Organizational Unit (OU) structure for policy scoping  
- Group Policy Objects (GPO) for configuration enforcement  
- Security groups for Role-Based Access Control (RBAC)  

This architecture supports:

- Least privilege  
- Centralized identity governance  
- Policy-driven enforcement  
- Auditability  

---

## Lab Series Progression

| Lab | Topic |
|---|---|
| Lab-01 — Virtualization and Identity Infrastructure Foundation | Environment Buildout |
| Lab-02 — AD DS Deployment | Identity Platform Deployment |
| Lab-03 — Domain Controller Promotion | Identity Activation |
| Lab-04 — OU Design and GPO Enforcement | Policy & Access Control |
| Lab-05 — Identity Lifecycle Management | Joiner / Mover / Leaver |
| Lab-06 — NTFS and Share Permissions | Resource Access Control |
| Lab-07 — Service Accounts and Delegation | Privileged Identity Management |
| Lab-08 — Identity Monitoring and Auditing | Security & Compliance |
| Lab-09 — Password Policy and Account Lockout Hardening | Authentication Hardening |
| Lab-10 — Fine-Grained Password Policies for Tiered Identity Control | Tiered Authentication Control |
| Lab-11 — DHCP Services for Enterprise Identity Infrastructure | Identity-Supporting Network Services |
| Lab-12 — Additional Domain Controller and AD Replication | Directory Resilience |
| Lab-13 — Centralized Logging and Event Forwarding for Identity Events | Visibility & Audit Collection |
| Lab-14 — Active Directory Sites and Services for Replication Topology | Replication Topology |
| Lab-15 — Group Policy Security Baselines for Workstations and Servers | Endpoint Security Control |
| Lab-16 — Delegation of Control and Tiered Administrative Boundaries | Least Privilege Administration |
| Lab-17 — Windows LAPS and Local Administrator Control | Privileged Endpoint Protection |
| Lab-18 — Group-Based Access Control for File and Department Resources | Authorization Design |
| Lab-19 — Active Directory Certificate Services | Enterprise Trust Services |
| Lab-20 — Identity Lifecycle Automation with PowerShell | Identity Automation |
| Lab-21 — Directory Recovery, Backup, and Operational Resilience | Identity Recovery & Continuity |

---

## Enterprise IAM Objectives

This lab series demonstrates structured IAM implementation within an enterprise environment.

Core focus areas:

- Active Directory deployment and hardening  
- Identity provisioning and lifecycle management  
- Role-based access control (RBAC)  
- Group Policy configuration and enforcement  
- Hybrid identity preparation (future Entra ID integration)  
- Identity monitoring, logging, and auditing  

---

## Quick Access

- [Lab-01 — Virtualization and Identity Infrastructure Foundation](./Lab-01-Virtualization-and-Identity-Infrastructure-Foundation/)
- [Lab-02 — AD DS Deployment](./Lab-02-AD-DS-Deployment/)
- [Lab-03 — Domain Controller Promotion](./Lab-03-Domain-Controller-Promotion/)
- [Lab-04 — OU Design and GPO Enforcement](./Lab-04-OU-Design-and-GPO-Enforcement/)
- [Lab-05 — Identity Lifecycle Management](./Lab-05-Identity-Lifecycle-Management/)
- [Lab-06 — NTFS and Share Permissions](./Lab-06-NTFS-and-Share-Permissions/)
- [Lab-07 — Service Accounts and Delegation](./Lab-07-Service-Accounts-and-Delegation/)
- [Lab-08 — Identity Monitoring and Auditing](./Lab-08-Identity-Monitoring-and-Auditing/)
- [Lab-09 — Password Policy and Account Lockout Hardening](./Lab-09-Password-Policy-and-Account-Lockout-Hardening/)
- [Lab-10 — Fine-Grained Password Policies for Tiered Identity Control](./Lab-10-Fine-Grained-Password-Policies-for-Tiered-Identity-Control/)
- [Lab-11 — DHCP Services for Enterprise Identity Infrastructure](./Lab-11-DHCP-Services-for-Enterprise-Identity-Infrastructure/)
- [Lab-12 — Additional Domain Controller and AD Replication](Lab-12-Additional-Domain-Controller-and-AD-Replication)
- [Lab-13 — Centralized Logging and Event Forwarding for Identity Events](Lab-13-Centralized-Logging-and-Event-Forwarding-for-Identity-Events)
- [Lab-14 — Active Directory Sites and Services for Replication Topology](Lab-14-Active-Directory-Sites-and-Services-for-Replication-Topology)
- [Lab-15 — Group Policy Security Baselines for Workstations and Servers](./Lab-15-Group-Policy-Security-Baselines-for-Workstations-and-Servers)
