# Lab-02 — Active Directory Domain Services (AD DS) Deployment

![Lab](https://img.shields.io/badge/type-lab-blue)
![Active Directory](https://img.shields.io/badge/technology-Active%20Directory-purple)
![Windows Server](https://img.shields.io/badge/platform-Windows%20Server%202022-blue)
![IAM](https://img.shields.io/badge/focus-IAM-green)
![Level](https://img.shields.io/badge/level-foundation-lightgrey)

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
| Disk | 60 GB |
| Network | Hyper-V Virtual Switch |
| IP Address | 192.168.10.10 |
| Role | Domain Controller |
| Domain | mrtg.local |

---

## Architecture

The MRTG-DC01 server functions as the primary domain controller, hosting:
- Active Directory Domain Services (AD DS)
- DNS (Active Directory-integrated)

This system acts as the centralized identity provider, responsible for:
- Authentication (Kerberos)
- Directory object management
- Access control enforcement

---

## Deployment Steps

### 1. Install AD DS Role
![AD DS Role Installation](./01-ad-ds-role-installation.png)

Active Directory Domain Services role installed using Server Manager.

---

### 2. Validate Prerequisites
![Prerequisites Check](./02-ad-ds-prerequisites-check.png)

Prerequisite validation completed successfully prior to promotion.

---

### 3. Create New Forest
![New Forest Creation](./03-new-forest-mrtg-local.png)

New Active Directory forest created with root domain `mrtg.local`.

---

### 4. Verify DNS Zones
![DNS Zones](./04-dns-zones-mrtg-local.png)

Forward lookup zones created and integrated with Active Directory.

---

### 5. Verify _msdcs Records
![MSDCS Records](./05-dns-msdcs-service-records.png)

Critical service records (`_msdcs`) confirm domain controller service discovery.

---

### 6. Verify DNS Records
![DNS Records](./06-dns-host-and-service-records.png)

Host and service records registered correctly in DNS.

---

### 7. Validate Network Configuration
![IP Configuration](./07-ipconfig-domain-controller.png)

Static IP configuration verified, with DNS pointing to the domain controller.

---

### 8. Validate Authentication & Name Resolution
![Authentication Validation](./08-domain-authentication-validation.png)

Successful domain authentication and DNS resolution:
- `whoami` confirms domain context
- `ping mrtg.local` resolves correctly

---

### 9. Create Post-Promotion Checkpoint
![Checkpoint](./09-post-dc-promotion-checkpoint.png)

Hyper-V checkpoint created to preserve a stable domain controller baseline.

---

## Outcome

- Successfully deployed AD DS domain (`mrtg.local`)
- Established MRTG-DC01 as domain controller
- Configured AD-integrated DNS
- Validated authentication and name resolution
- Created a reusable infrastructure baseline

This environment serves as the identity foundation for future IAM labs.

---

## Next Lab

**Lab-03 — Active Directory Identity Management**

Planned focus:
- Organizational Unit (OU) structure
- User and group provisioning
- Access control models
- Introduction to Group Policy (GPO)
