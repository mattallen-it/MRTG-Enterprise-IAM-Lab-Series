# Lab-02 — Active Directory Domain Services (AD DS) Deployment

![type: lab](https://img.shields.io/badge/type-lab-blue) ![technology: Active Directory](https://img.shields.io/badge/technology-Active%20Directory-darkblue) ![platform: Windows Server 2022](https://img.shields.io/badge/platform-Windows%20Server%202022-0078D6) ![focus: IAM](https://img.shields.io/badge/focus-IAM-green) ![domain: identity](https://img.shields.io/badge/domain-identity-informational) ![level: foundation](https://img.shields.io/badge/level-foundation-lightgrey)

## Overview
Monroe Redstone Technology Group (MRTG) deployed a centralized identity infrastructure using Active Directory Domain Services (AD DS) to support authentication, access control, and identity management.

This lab establishes a domain controller for the internal domain `mrtg.local`, forming the foundation for enterprise identity and access management.

---

## Objective
Deploy and configure Active Directory Domain Services (AD DS) to establish a centralized identity authority.

This includes:
- Installing AD DS
- Promoting a domain controller
- Configuring DNS
- Validating authentication and name resolution

---

## Scope

### Included
- Windows Server 2022 deployment (Hyper-V)
- Server configuration and initial hardening
- Static IP assignment
- AD DS installation and domain controller promotion
- DNS configuration (AD-integrated zones)
- Authentication and DNS validation
- Hyper-V checkpoint creation

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

The MRTG-DC01 server functions as the primary domain controller within the `mrtg.local` domain, hosting:

- Active Directory Domain Services (AD DS)
- Active Directory-integrated DNS

This system serves as the centralized identity provider, responsible for:

- Authentication (Kerberos)
- Authorization and access control
- Directory object management
- Service discovery via DNS

This architecture establishes the foundation for identity and access management (IAM) within the enterprise environment.

---
## Deployment Phases

### Phase 1 — AD DS Installation
![AD DS Role Installation](./screenshots/01-ad-ds-role-installation.png)

Active Directory Domain Services (AD DS) role installed using Server Manager.

---

### Phase 2 — Prerequisite Validation
![Prerequisites Check](./screenshots/02-ad-ds-prerequisites-check.png)

Prerequisite checks completed successfully prior to domain controller promotion.

---

## Outcome

- Successfully deployed AD DS domain (`mrtg.local`)
- Established MRTG-DC01 as domain controller
- Configured AD-integrated DNS
- Validated authentication and name resolution
- Created a reusable infrastructure baseline
- Established a functional identity infrastructure aligned with enterprise IAM principles

This environment serves as the identity foundation for future IAM labs.

---

## Next Lab

**Lab-03 — Active Directory Identity Management**

Planned focus:
- Organizational Unit (OU) structure
- User and group provisioning
- Access control models
- Introduction to Group Policy (GPO)
