# Lab-02 — Active Directory Domain Services (AD DS) Deployment

![Type](https://img.shields.io/badge/type-lab-blue)
![Track](https://img.shields.io/badge/track-IAM-green)
![Technology](https://img.shields.io/badge/technology-Active_Directory-2A628C)
![Platform](https://img.shields.io/badge/platform-Windows_Server_2022-lightgrey)
![Focus](https://img.shields.io/badge/focus-Identity_Deployment-orange)
![Domain](https://img.shields.io/badge/domain-mrtg.local-purple)
![Auth](https://img.shields.io/badge/auth-Kerberos-blue)
![Level](https://img.shields.io/badge/level-Foundation-darkblue)

---

## Overview

This lab establishes Active Directory Domain Services (AD DS) within the MRTG environment by promoting Windows Server 2022 to a Domain Controller.

This deployment creates the centralized identity authority responsible for authentication, authorization, and directory-based access control.

---

## Why This Matters

Active Directory functions as the core identity provider in most enterprise and government environments.

Proper AD DS deployment enables:

- Centralized Kerberos-based authentication  
- Structured identity management through domain hierarchy  
- Secure access control across systems and resources  
- Policy enforcement via Group Policy Objects (GPO)  
- Audit-ready identity infrastructure  

This lab establishes the identity authority that all future access control and governance mechanisms depend on.

---

## Environment

| Component        | Value |
|-----------------|--------|
| Domain Name      | mrtg.local |
| Domain Controller| MRTG-DC01 |
| OS               | Windows Server 2022 |
| Role Installed   | Active Directory Domain Services |
| Virtualization   | Hyper-V |

---

## Architecture

### Domain Controller — MRTG-DC01
- Active Directory Domain Services (AD DS)  
- DNS Server  

### Directory Structure
- New forest: **mrtg.local**

### Authentication Model
- Kerberos-based domain authentication  

This deployment establishes the centralized identity authority for the MRTG environment.

---

## Security Considerations

- Domain Controller deployed within isolated internal virtual network  
- AD DS installed using least privilege administrative access  
- DNS integrated with Active Directory for secure name resolution  
- Environment prepared for policy enforcement and auditing  

---

## Implementation & Validation

### 1. AD DS Role Installation
![AD DS Role Installation](images/01-ad-ds-role-installation.png)

---

### 2. Prerequisites Validation
![AD DS Prerequisites Check](images/02-ad-ds-prerequisites-check.png)

---

### 3. New Forest Creation — mrtg.local
![New Forest](images/03-new-forest-mrtg-local.png)

---

### 4. DNS Zone Validation
![DNS Zone](images/04-dns-zones-mrtg-local.png)

---

### 5. SRV Record Validation
![SRV Records](images/05-dns-msdcs-service-records.png)

---

### 6. Host and Service Record Validation
![DNS Host Records](images/06-dns-host-and-service-record.png)

---

### 7. Network Configuration Validation
- Static IP configured  
- DNS pointed to self for authoritative resolution  

![IP Configuration](images/07-ipconfig-domain-controller.png)


---

### 8. Post-Deployment Checkpoint Creation
A Hyper-V checkpoint was created to preserve the post-promotion state for controlled rollback and testing.

![Post Promotion Checkpoint](images/08-post-dc-promotion-checkpoint.png)

---

## Outcome

A fully functional Active Directory Domain Services environment was successfully deployed.

- MRTG-DC01 configured as Domain Controller  
- DNS integrated with Active Directory  
- Kerberos-based authentication operational  
- Centralized identity infrastructure established  

The environment now functions as the authoritative identity boundary for domain-joined systems.

---

## Next Lab

[Lab-03 — Domain Controller Promotion & Identity Structure](../Lab-03-Domain-Controller-Promotion/)

The next lab will cover:

- Organizational Unit (OU) structure design  
- Initial Group Policy configuration  
- Security group implementation (RBAC foundation)  
- Identity organization aligned to business roles  
