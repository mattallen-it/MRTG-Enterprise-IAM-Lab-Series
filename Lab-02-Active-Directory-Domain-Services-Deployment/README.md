# Lab-02 — Active Directory Domain Services (AD DS) Deployment

![type: lab](https://img.shields.io/badge/type-lab-blue) ![technology: Active Directory](https://img.shields.io/badge/technology-Active%20Directory-darkblue) ![platform: Windows Server 2022](https://img.shields.io/badge/platform-Windows%20Server%202022-0078D6) ![focus: IAM](https://img.shields.io/badge/focus-IAM-green) ![domain: identity](https://img.shields.io/badge/domain-identity-informational) ![level: foundation](https://img.shields.io/badge/level-foundation-lightgrey)

## Overview
Monroe Redstone Technology Group (MRTG) deployed a centralized identity infrastructure using Active Directory Domain Services (AD DS) to support authentication, access control, and identity management.

This lab establishes a domain controller for the internal domain `mrtg.local`, forming the foundation for enterprise identity and access management.

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
| Role | Domain Controller |
| Domain | mrtg.local |

---
## Architecture

The MRTG-DC01 server is prepared to function as a domain controller within the future `mrtg.local` domain, hosting:

- Active Directory Domain Services (AD DS)

At this stage, the server is configured with the necessary role and management tools required to support identity services.

Domain creation, authentication, and DNS integration are completed in Lab 03.

---
## Deployment Phases

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

This lab establishes the infrastructure foundation required for centralized identity deployment in Lab 03.

This environment serves as the identity foundation for future IAM labs.

---

## Why This Matters

Separating AD DS installation from domain controller promotion reflects real-world enterprise practices, where infrastructure preparation and identity activation are handled as distinct operational phases.

This reduces risk and ensures controlled deployment of critical identity systems.

## Next Lab

**Lab-03 — Active Directory Identity Management**

Planned focus:
- Organizational Unit (OU) structure
- User and group provisioning
- Access control models
- Introduction to Group Policy (GPO)
