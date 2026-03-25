# Monroe Redstone Technology Group — IAM Lab Series

![Career Track](https://img.shields.io/badge/career%20track-IAM-065f46?style=flat-square)
![Certification](https://img.shields.io/badge/certification-Security%2B-b91c1c?style=flat-square)
![Environment](https://img.shields.io/badge/environment-Windows%20Enterprise-1f2937?style=flat-square)

This environment represents a simulated enterprise identity infrastructure for Monroe Redstone Technology Group (MRTG), designed to demonstrate real-world Identity and Access Management (IAM) practices within a Windows-based environment.

The lab series focuses on building and managing Active Directory as an identity authority, implementing role-based access control (RBAC), enforcing Group Policy, and integrating hybrid identity with Microsoft Entra ID. Emphasis is placed on least privilege, identity lifecycle management, and auditing to align with enterprise and government security expectations.

## Objective

To design, implement, and secure an enterprise identity environment that reflects real-world IAM responsibilities in government and regulated IT environments.

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
[Lab-01 — Virtualization and Identity Infrastructure Foundation](./Lab-01-Virtualization/README.md)
[Lab-02 — Active Directory Domain Services (AD DS) Deployment](./Lab-02-AD-DS-Deployment/README.md)
[Lab-03 — Active Directory Domain Setup (Identity Authority Foundation)](./Lab-03-AD-Setup/README.md)
[Lab-04 — Organizational Unit (OU) Design and Group Policy](./Lab-04-OU-and-GPO/README.md)
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

## Quick Access

- [Start with Lab 01](./Lab-01-Virtualization-and-Identity-Infrastructure-Foundation)
