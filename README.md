# Monroe Redstone Technology Group — IAM Lab Series

![IAM](https://img.shields.io/badge/IAM-Enterprise-blue)
![Active Directory](https://img.shields.io/badge/Directory-AD%20DS-green)
![Security](https://img.shields.io/badge/Security-Policy%20%26%20Access-red)
![Platform](https://img.shields.io/badge/Platform-Windows%20Enterprise-lightgrey)
![Focus](https://img.shields.io/badge/Focus-Identity%20Governance-purple)

---

This project simulates a real-world enterprise Identity and Access Management (IAM) environment for Monroe Redstone Technology Group (MRTG).

The lab series demonstrates how identity systems are built, secured, and managed using Active Directory, including:

- Role-based access control (RBAC)
- Organizational Unit (OU) design
- Group Policy enforcement
- Access control via security groups
- Identity lifecycle governance
- Monitoring and auditing

The focus is on practical IAM operations aligned with enterprise and government environments, emphasizing least privilege, access control, and auditability.

---

## Objectives

- Design, implement, and secure an enterprise identity environment
- Deploy Active Directory Domain Services (AD DS)
- Enforce identity-based access control
- Validate policy enforcement across domain systems
- Align identity operations with government-regulated IT practices

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

- Domain Name: `mrtg.local`
- Directory Services: Active Directory Domain Services (AD DS)
- Authentication Model: Kerberos-based domain authentication

---

## Systems

### MRTG-DC01 — Domain Controller
- Active Directory
- DNS
- Group Policy Management

### CLIENT01 — Domain-Joined Workstation
- Policy enforcement validation
- Authentication testing
- Access control validation

---

## Infrastructure Architecture

| Component        | Description                            |
|------------------|----------------------------------------|
| Hypervisor       | Hyper-V (Windows 11 Pro Host)         |
| Domain Controller| MRTG-DC01 — Windows Server 2022       |
| Services         | AD DS, DNS, Group Policy              |
| Client System    | CLIENT01 — Windows 11 Enterprise       |

This architecture reflects enterprise IAM deployment practices in regulated environments.

---

## Identity Architecture

Identity authentication and authorization are centralized through Active Directory.

Access to resources is governed through:

- Organizational Unit (OU) structure for policy scoping
- Group Policy Objects (GPO) for configuration enforcement
- Security groups for role-based access control (RBAC)

This model reflects enterprise IAM principles of:

- Least privilege
- Centralized identity control
- Policy-driven enforcement
- Auditability

---

## Lab Series Progression

| Lab | Topic |
|------|--------|
| Lab-01 — Virtualization and Identity Infrastructure Foundation | Environment Buildout |
| Lab-02 — AD DS Deployment | Identity Platform Deployment |
| Lab-03 — Domain Controller Promotion | Identity Activation |
| Lab-04 — OU Design and GPO Enforcement | Policy & Access Control |
| Lab-05 — Identity Lifecycle Management | Joiner / Mover / Leaver |
| Lab-06 — NTFS and Share Permissions | Resource Access Control |
| Lab-07 — Service Accounts and Delegation | Privileged Identity Management |
| Lab-08 — Identity Monitoring and Auditing | Security & Compliance |

---

## Project Goal

This lab series demonstrates practical IAM implementation within a structured enterprise environment.

Key focus areas:

- Identity provisioning and lifecycle management
- Active Directory deployment and hardening
- Group Policy configuration and enforcement
- Role-based access control (RBAC)
- Hybrid identity integration (future Entra ID)
- Identity monitoring, logging, and auditing

---

## Quick Access

- [Lab-01 — Virtualization and Identity Infrastructure Foundation](./Lab-01-Virtualization-and-Identity-Infrastructure-Foundation/)
- [Lab-02 — AD DS Deployment](./Lab-02-AD-DS-Deployment/)
- [Lab-03 — Domain Controller Promotion](./Lab-03-Domain-Controller-Promotion/)
- [Lab-04 — OU Design and GPO Enforcement](./Lab-04-OU-Design-and-GPO-Enforcement/)
