# Monroe Redstone Technology Group — IAM Lab Series

![Career Track](https://img.shields.io/badge/career%20track-IAM-065f46?style=flat-square)
![Certification](https://img.shields.io/badge/certification-Security%2B-b91c1c?style=flat-square)
![Environment](https://img.shields.io/badge/environment-Windows%20Enterprise-1f2937?style=flat-square)

This environment represents a simulated enterprise identity infrastructure for Monroe Redstone Technology Group (MRTG), designed to demonstrate real-world Identity and Access Management (IAM) practices within a Windows-based environment.

The lab series focuses on building and managing Active Directory as an identity authority, implementing role-based access control (RBAC), enforcing Group Policy, and integrating hybrid identity with Microsoft Entra ID. Emphasis is placed on least privilege, identity lifecycle management, and auditing to align with enterprise and government security expectations.

---

## Objective

To design, implement, and secure an enterprise identity environment that reflects real-world IAM responsibilities in government and regulated IT environments.

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
  - Role: Endpoint used for policy enforcement and access validation  
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

Access to resources is governed through:
- Organizational Unit (OU) structure for policy scoping
- Group Policy Objects (GPO) for configuration enforcement
- Security groups for role-based access assignments

This model aligns with enterprise IAM principles of least privilege, centralized identity control, and auditability.

---

## Lab Series Progression

| Lab | Topic |
|-----|------|
| Lab-01 — Virtualization Foundation | Environment Buildout |
| Lab-02 — AD DS Installation | Identity Platform Deployment |
| Lab-03 — Domain Controller Promotion | Identity Activation |
| Lab-04 — OU and GPO Implementation | Policy & Access Control |
| Lab-05 — Identity Lifecycle Management | User & Group Management |
| Lab-06 — NTFS and Share Permissions | Resource Access Control |
| Lab-07 — Service Accounts and Delegation | Privileged Identity Management |
| Lab-08 — Identity Monitoring and Auditing | Security & Compliance |

---

## Project Goal

This lab series demonstrates practical Identity and Access Management (IAM) implementation within a simulated enterprise environment, aligned with real-world government and enterprise security practices.

Key focus areas include:

- Identity provisioning and lifecycle management  
- Role-Based Access Control (RBAC) implementation  
- Active Directory deployment and hardening  
- Group Policy-based configuration enforcement  
- Hybrid identity integration (Active Directory + Microsoft Entra ID)  
- Identity monitoring, logging, and auditing

---

## Quick Access

- [Start with Lab-01](./Lab-01-Virtualization/README.md)
- [Jump to Lab-04 (OU Design & GPO)](./Lab-04-OU-and-GPO/README.md)
