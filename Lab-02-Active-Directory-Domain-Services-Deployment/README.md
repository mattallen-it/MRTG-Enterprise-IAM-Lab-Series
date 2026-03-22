# Lab-02 — Active Directory Domain Services (AD DS) Deployment

!![Type](https://img.shields.io/badge/type-lab-1f2937?style=flat-square)
![Technology](https://img.shields.io/badge/technology-Active%20Directory-1f2937?style=flat-square)
![Platform](https://img.shields.io/badge/platform-Windows%20Server%202022-1f2937?style=flat-square)
![Focus](https://img.shields.io/badge/focus-IAM-065f46?style=flat-square)
![Domain](https://img.shields.io/badge/domain-Identity-4c1d95?style=flat-square)
![Level](https://img.shields.io/badge/level-Foundation-6b7280?style=flat-square)

## Overview
Monroe Redstone Technology Group (MRTG) deployed a centralized identity infrastructure using Active Directory Domain Services (AD DS) to support authentication, access control, and identity management.

This lab establishes a domain controller for the internal domain `mrtg.local`, forming the foundation for enterprise identity and access management.

This lab focuses on infrastructure preparation, with domain creation and identity activation completed in the next phase.

This staged approach aligns with enterprise identity deployment practices, where infrastructure preparation and identity activation are handled as separate operational phases.

---

## Objective

Deploy Active Directory Domain Services (AD DS) to prepare Windows Server for domain controller promotion and centralized identity management.

---

## Scope

### Included
- Windows Server 2022 deployment (Hyper-V)
- Server configuration and initial hardening
- Static IP assignment
- AD DS role installation
- Installation of AD DS management tools

### Not Included
- Organizational Unit (OU) design
- User and group provisioning
- Group Policy Object (GPO) configuration
- Domain-joined workstation setup

---

## Environment

| Component | Value |
|----------|------|
| Host OS | Windows 11 Pro |
| Hypervisor | Hyper-V |
| VM Name | MRTG-DC01 |
| OS | Windows Server 2022 Standard (Desktop Experience) |
| Generation | Gen 2 |
| Memory | 8 GB |
| Disk | 80 GB |
| Network | Hyper-V Virtual Switch |
| IP Address | 192.168.10.10 |
| Role | Member Server (Pre-Domain Controller) |
| Domain | N/A |

---
## Architecture

The MRTG-DC01 server is prepared to function as a domain controller within the future `mrtg.local` domain, hosting:

- Active Directory Domain Services (AD DS)

At this stage, the server is configured with the necessary role and management tools required to support identity services.

Domain creation, authentication, and DNS integration are completed in Lab 03.

This phase represents infrastructure readiness, with identity services activated during domain promotion in Lab 03.

### Phase 1 — AD DS Installation

Active Directory Domain Services (AD DS) role was installed using Server Manager, including all required management tools.

The server is now prepared for domain controller promotion, which will be completed in Lab 03.

### Phase 1 — AD DS Installation
![AD DS Role Installation](./screenshots/01-ad-ds-role-installation.png)

Active Directory Domain Services (AD DS) role installed using Server Manager.

---
## Outcome

- Successfully installed Active Directory Domain Services (AD DS)
- Prepared Windows Server for domain controller promotion
- Installed required management tools for AD administration

This system is now staged for domain controller promotion, which is completed in Lab 03.
---

## Why This Matters

Separating AD DS installation from domain controller promotion reflects real-world enterprise practices, where infrastructure preparation and identity activation are handled as distinct operational phases.

This reduces risk and ensures controlled deployment of critical identity systems.

## Next Lab

### Lab-03 — Active Directory Domain Setup (Identity Authority Foundation)

In the next lab, the server will be promoted to a Domain Controller, establishing the `mrtg.local` Active Directory domain as the centralized identity authority.

### Planned focus:
- Domain controller promotion (new forest)
- DNS configuration (AD-integrated)
- Authentication and name resolution validation
- Domain controller health checks

---

## Navigation

- [← Previous: Lab-01 — Virtualization and Identity Infrastructure Foundation](../Lab-01-Virtualization-and-Identity-Infrastructure-Foundation)
- [Next → Lab-03 — Active Directory Domain Setup](../Lab-03-Active-Directory-Domain-Setup)
