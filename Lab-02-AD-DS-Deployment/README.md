# Lab-02 — AD DS Deployment

---

## Overview

This lab focuses on deploying Active Directory Domain Services (AD DS) within the MRTG environment by promoting a Windows Server to a Domain Controller.

This establishes centralized identity management, enabling authentication, authorization, and directory-based access control across the environment.

---

## Why This Matters

Active Directory serves as the core identity provider in most enterprise and government environments.

Proper deployment of AD DS enables:

- Centralized authentication using Kerberos
- Structured identity management through directory services
- Secure access control across systems and resources
- Foundation for Group Policy, RBAC, and auditing

This lab establishes the identity authority that all future access control and policy enforcement will depend on.

---

## Environment

| Component           | Value                              |
|--------------------|-----------------------------------|
| Domain Name        | mrtg.local                         |
| Domain Controller  | MRTG-DC01                          |
| OS                 | Windows Server 2022                |
| Role               | Active Directory Domain Services   |
| Virtualization     | Hyper-V                            |

---

## Architecture

- **Domain Controller: MRTG-DC01**
  - Active Directory Domain Services (AD DS)
  - DNS Server

- **Directory Structure**
  - New forest: `mrtg.local`

- **Authentication Model**
  - Kerberos-based authentication

This deployment establishes the centralized identity authority for the MRTG environment.

---

## Security Considerations

- Domain Controller deployed in isolated virtual network
- AD DS installed using least privilege administrative access
- DNS integrated with Active Directory for secure name resolution
- Environment prepared for future policy enforcement and auditing

---

## Lab Steps and Evidence

### 1. Installed AD DS Role
The Active Directory Domain Services role was installed on MRTG-DC01.

![AD DS Role Installation](./images/01-ad-ds-role-installation.png)

---

### 2. Prerequisites Check
All prerequisite checks passed before promoting the server to a Domain Controller.

![AD DS Prerequisites Check](./images/02-ad-ds-prerequisites-check.png)

---

### 3. Created New Forest
A new Active Directory forest was created using the root domain `mrtg.local`.

![New Forest mrtg.local](./images/03-new-forest-mrtg-local.png)

---

### 4. Verified DNS Zones
DNS zones were validated to ensure proper domain name resolution required for Kerberos authentication and domain service discovery.

![DNS Zones mrtg.local](./images/04-dns-zones-mrtg-local.png)

---

### 5. Verified AD-Integrated DNS Records
Service (SRV) records were verified to confirm that domain-joined systems can locate domain controllers for authentication.

![DNS msdcs Service Records](./images/05-dns-msdcs-service-records.png)

---

### 6. Verified Host and Service Records
Host and service records were validated to ensure reliable name resolution between systems, supporting authentication and access to domain resources.

![DNS Host and Service Record](./images/06-dns-host-and-service-record.png)

---

### 7. Verified Network Configuration
The domain controller was configured with a static IP and pointed to itself for DNS resolution to ensure consistent identity services and authentication reliability.

DNS configuration was verified using ipconfig /all to confirm the domain controller is using itself for name resolution, ensuring proper authentication flow.

![IPConfig Domain Controller](./images/07-ipconfig-domain-controller.png)

---

### 8. Created Post-Deployment Checkpoint
A Hyper-V checkpoint was created after successful domain controller promotion.

![Post DC Promotion Checkpoint](./images/09-post-dc-promotion-checkpoint.png)

---

## Outcome

A fully functional Active Directory Domain Services environment was successfully deployed.

- Domain `mrtg.local` created
- MRTG-DC01 configured as Domain Controller
- DNS integrated with Active Directory
- Core identity infrastructure established

This environment now functions as the centralized identity authority, enabling Kerberos-based authentication, directory-backed authorization, and policy enforcement across domain-joined systems.

---

## Next Lab

[Lab-03 — AD Identity Structure](../Lab-03-AD-Identity-Structure)

The next lab will cover:

- Organizational Unit (OU) structure design  
- User and group provisioning  
- Security group implementation (RBAC foundation)  
- Identity organization aligned to business roles  

---
