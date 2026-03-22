# Monroe Redstone Technology Group — IAM Lab Series
Enterprise Identity Simulation Environment

Hands-on Identity and Access Management (IAM) lab series covering Hyper-V infrastructure, Active Directory identity services, authentication workflows, role-based access control (RBAC), and hybrid identity with Microsoft Entra ID.

![Focus](https://img.shields.io/badge/Career%20Track-Identity%20and%20Access%20Management-darkgreen)
![Security](https://img.shields.io/badge/Certification-Security%2B-red)
![Platform](https://img.shields.io/badge/Environment-Windows%20Enterprise-blue)

## Organization

This repository documents a simulated enterprise Identity and Access Management environment for:

**Monroe Redstone Technology Group (MRTG)**

The lab environment models a small-to-mid enterprise identity infrastructure including:

- Windows Server Active Directory
- Domain-joined workstations
- Identity lifecycle management
- Role-based access control (RBAC)
- Group Policy security baselines
- Privileged access administration
- Hybrid identity integration with Microsoft Entra ID
- Identity monitoring and auditing

---

## Domain

mrtg.local (Active Directory domain)

---

## Systems

MRTG-DC01 → Primary Domain Controller (AD DS, DNS services)

CLIENT01 → Domain-Joined Workstation (Windows 11 Enterprise)

---

## Infrastructure Architecture

| Component | Description |
|----------|-------------|
| Hypervisor | Hyper-V (Windows 11 Pro) |
| Domain Controller | **MRTG-DC01** — Windows Server 2022 |
| Services | Active Directory Domain Services (AD DS), DNS |
| Client System | **CLIENT01** — Windows 11 Enterprise |

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
| [Lab-01](./Lab-01-Virtualization-and-Identity-Infrastructure-Foundation) | Virtualization and Identity Infrastructure Foundation |
| [Lab-02](./Lab-02-Active-Directory-Domain-Services-AD-DS-Deployment) | Active Directory Domain Services (AD DS) Deployment |
| [Lab-03](./Lab-03-Active-Directory-Domain-Setup) | Active Directory Domain Setup (Identity Authority Foundation) |
| Lab-04 | Organizational Units (OU) Design and Group Policy |
| Lab-05 | User and Group Lifecycle Management |
| Lab-06 | NTFS Permissions vs Share Permissions |
| Lab-07 | Service Accounts and Delegation |
| Lab-08 | Identity Monitoring and Auditing |

---

## Project Goal

This lab series demonstrates practical Identity and Access Management (IAM) implementation within a simulated enterprise environment, aligned with real-world government and enterprise security practices.

Key focus areas include:
- Identity provisioning and lifecycle management
- Role-based access control (RBAC) implementation
- Directory services deployment and hardening
- Group Policy-based security enforcement
- Hybrid identity integration (Active Directory + Microsoft Entra ID)
- Identity monitoring, logging, and auditing
