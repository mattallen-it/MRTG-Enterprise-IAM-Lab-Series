# Lab-02 — AD DS Deployment

![Type](https://img.shields.io/badge/type-lab-blue)
![Track](https://img.shields.io/badge/track-IAM-green)
![Technology](https://img.shields.io/badge/technology-Active%20Directory-blue)
![Platform](https://img.shields.io/badge/platform-Windows%20Server%202022-lightgrey)
![Focus](https://img.shields.io/badge/focus-Identity%20Deployment-orange)
![Domain](https://img.shields.io/badge/domain-mrtg.local-purple)
![Auth](https://img.shields.io/badge/auth-Kerberos-yellow)
![Level](https://img.shields.io/badge/level-Foundation-darkblue)

---

## Overview

This lab focuses on deploying Active Directory Domain Services (AD DS) within the MRTG environment by promoting a Windows Server to a Domain Controller.

This establishes a centralized identity authority, enabling authentication, authorization, and directory-based access control across the environment.

---

## Why This Matters

Active Directory serves as the core identity provider in most enterprise and government environments.

Proper deployment of AD DS enables:

- Centralized authentication using Kerberos
- Structured identity management through directory services
- Secure access control across systems and resources
- Foundation for policy enforcement (GPO), role-based access control (RBAC), and identity auditing

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
The Active Directory Domain Services role was installed to establish the identity infrastructure required for centralized authentication and directory-based access control.

![AD DS Role Installation](./images/01-ad-ds-role-installation.png)

---

### 2. Prerequisites Check
All prerequisite checks were validated to ensure the system meets requirements for domain controller promotion and secure identity services deployment.

![AD DS Prerequisites Check](./images/02-ad-ds-prerequisites-check.png)

---

### 3. Created New Forest
A new Active Directory forest was created to establish a dedicated identity boundary for the MRTG environment, serving as the root of trust for authentication and authorization.

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
A Hyper-V checkpoint was created to preserve the post-deployment state, enabling rollback and controlled testing of identity configurations.

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
