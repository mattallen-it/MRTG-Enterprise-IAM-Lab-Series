# Monroe Redstone Technology Group — IAM Lab Series

![Career Track](https://img.shields.io/badge/career%20track-IAM-065f46?style=flat-square)
![Certification](https://img.shields.io/badge/certification-Security%2B-b91c1c?style=flat-square)
![Environment](https://img.shields.io/badge/environment-Windows%20Enterprise-1f2937?style=flat-square)

This project simulates a real-world enterprise Identity and Access Management (IAM) environment for Monroe Redstone Technology Group (MRTG).

The lab series demonstrates how identity systems are built, secured, and managed using Active Directory, including centralized authentication, role-based access control (RBAC), and policy enforcement.

The focus is on practical IAM operations aligned with government and enterprise environments, emphasizing least privilege, access control, and auditability.


---

## Objective

To design, implement, and secure an enterprise identity environment that reflects real-world IAM responsibilities in government and regulated IT environments.

This environment demonstrates the ability to design, implement, and validate identity systems that enforce secure access control in enterprise environments.

---

## Organization

This repository represents a simulated enterprise Identity and Access Management (IAM) environment for:

**Monroe Redstone Technology Group (MRTG)**

The environment is structured to reflect real-world enterprise identity architecture, including:

- Centralized Active Directory (AD DS)
- Domain-joined endpoints
- Organizational Unit (OU) hierarchy aligned to business functions
- Role-Based Access Control (RBAC) using security groups
- Group Policy-based configuration enforcement
- Privileged access separation and administrative tiering
- Hybrid identity integration (future state)
- Identity monitoring, auditing, and policy validation

---

## Domain

- **Domain Name:** mrtg.local  
- **Directory Service:** Active Directory Domain Services (AD DS)  
- **Authentication Model:** Kerberos-based domain authentication  

---

## Systems

- **MRTG-DC01** — Domain Controller  
  - Roles: Active Directory, DNS, Group Policy Management  

- **CLIENT01** — Domain-Joined Workstation  
  - Role: Used to validate authentication, Group Policy enforcement, and access control scenarios across the domain
---

## Infrastructure Architecture

| Component | Description |
|----------|-------------|
| Hypervisor | Hyper-V (Windows 11 Pro) |
| Domain Controller | **MRTG-DC01** — Windows Server 2022 |
| Services | Active Directory Domain Services (AD DS), DNS |
| Client System | **CLIENT01** — Windows 11 Enterprise |

This architecture reflects enterprise IAM design principles used in government and regulated environments, emphasizing centralized identity control, policy enforcement, and auditability.

---

## Identity Architecture

Identity authentication and authorization are enforced through a centralized Active Directory Domain Services (AD DS) model, utilizing domain-joined systems, Kerberos-based authentication, and role-based access control (RBAC) via security groups.

Authentication requests from domain-joined systems are processed by the domain controller using Kerberos, establishing trust and enabling secure access to domain resources.

Access to resources is governed through centralized identity-based policy enforcement, including:
- Organizational Unit (OU) structure for policy scoping
- Group Policy Objects (GPO) for configuration enforcement
- Security groups for role-based access assignments

This model aligns with enterprise IAM principles of least privilege, centralized identity control, and auditability.

This design reflects real-world enterprise IAM environments where identity is the primary security boundary.

---

## Lab Series Progression

| Lab | Topic |
|-----|------|
| Lab-01 — Virtualization and Identity Infrastructure Foundation | Environment Buildout (Identity Infrastructure Foundation) |
| Lab-02 — AD DS Deployment | Identity Platform Deployment (Active Directory) |
| Lab-03 — Domain Controller Promotion | Identity Activation (Domain Services) |
| Lab-04 — OU and GPO Implementation | Policy & Access Control (GPO Enforcement) |
| Lab-05 — Identity Lifecycle Management | User & Group Management |
| Lab-06 — NTFS and Share Permissions | Resource Access Control |
| Lab-07 — Service Accounts and Delegation | Privileged Identity Management |
| Lab-08 — Identity Monitoring and Auditing | Security & Compliance |

---

## Project Goal

This lab series demonstrates practical Identity and Access Management (IAM) implementation within a simulated enterprise environment, aligned with real-world government and enterprise security practices.

This lab series demonstrates the ability to troubleshoot, validate, and enforce identity-based access control in a controlled enterprise environment.

Key focus areas include:

- Identity provisioning and lifecycle management  
- Role-Based Access Control (RBAC) implementation  
- Active Directory deployment and hardening  
- Group Policy-based configuration enforcement  
- Hybrid identity integration (Active Directory + Microsoft Entra ID)  
- Identity monitoring, logging, and auditing

---

## Quick Access

- 🔹 [Lab-01 — Virtualization and Identity Infrastructure Foundation](./Lab-01-Virtualization-Foundation/README.md)
- 🔹 [Lab-02 — AD DS Deployment](./Lab-02-AD-DS-Installation/README.md)
- 🔹 [Lab-03 — Domain Controller Promotion](./Lab-03-Domain-Controller-Promotion/README.md)
- 🔹 [Lab-04 — OU and GPO Implementation](./Lab-04-OU-and-GPO-Implementation/README.md)
