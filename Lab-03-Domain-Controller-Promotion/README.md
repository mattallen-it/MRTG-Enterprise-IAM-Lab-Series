# Lab-03 — Domain Controller Promotion (Identity Activation)

![Type](https://img.shields.io/badge/type-lab-blue)
![Track](https://img.shields.io/badge/track-IAM-green)
![Technology](https://img.shields.io/badge/technology-Active_Directory-2A628C)
![Platform](https://img.shields.io/badge/platform-Windows_Server_2022-lightgrey)
![Auth](https://img.shields.io/badge/auth-Kerberos-blue)
![Focus](https://img.shields.io/badge/focus-Identity_Activation-yellow)
![Level](https://img.shields.io/badge/level-Foundation-darkblue)
![Role](https://img.shields.io/badge/role-Domain_Controller-critical)

---

## Overview

This lab activates the MRTG identity infrastructure by finalizing Domain Controller promotion and validating centralized authentication and directory services functionality.

This phase transitions the environment from infrastructure deployment to operational identity enforcement.

---

## Objective

Promote Windows Server 2022 to Domain Controller and establish **mrtg.local** as the authoritative identity domain for MRTG.

---

## Scope

### Included
- Domain controller promotion (mrtg.local)
- AD-integrated DNS validation
- Kerberos authentication validation
- Service discovery (SRV record) validation
- Domain health checks
- Hyper-V baseline checkpoint creation

### Not Included
- Organizational Unit (OU) structure design
- Security group provisioning
- Group Policy configuration
- Domain-joined workstation configuration

---

## Environment

| Component | Value |
|-----------|--------|
| VM Name | MRTG-DC01 |
| OS | Windows Server 2022 |
| Role | Domain Controller |
| Domain | mrtg.local |
| DNS | AD-Integrated |

---

## Architecture

MRTG-DC01 operates as the first domain controller within the **mrtg.local** forest, providing:

- Active Directory Domain Services (AD DS)
- AD-integrated DNS
- Kerberos-based authentication
- Service discovery via SRV records

This system functions as the authoritative Identity Provider (IdP) for MRTG.

---

# Identity Activation Phases

## Phase 1 — Domain Controller Promotion
![Forest Creation](images/03-new-forest-mrtg-local.png)

The server was promoted to Domain Controller, establishing **mrtg.local** as the forest root.

---

## Phase 2 — DNS Validation
![DNS Zones](images/04-dns-zones-mrtg-local.png)

Validated AD-integrated DNS zones required for authentication and service discovery.

---

## Phase 3 — Service Record Validation
![DNS Records](images/06-dns-host-and-service-record.png)

Confirmed SRV and host records for proper domain controller registration.

---

## Phase 4 — Network Configuration Validation
![IP Config](images/07-ipconfig-domain-controller.png)

Validated:

- Static IP configuration  
- DNS self-reference (authoritative resolution)  

---

## Phase 5 — Authentication & Resolution Validation
![Authentication Validation](images/08-domain-authentication-validation.png)

Validated:

- Successful domain logon  
- Domain context confirmation  
- Name resolution within mrtg.local  
- Kerberos-based authentication  

Operational verification performed using:

dcdiag
nslookup mrtg.local
set l
klist

Domain health confirmed with no critical errors.

---

## Phase 6 — Infrastructure Baseline Checkpoint
![Checkpoint](images/09-post-dc-promotion-checkpoint.png)

A Hyper-V checkpoint was created to preserve a stable post-promotion baseline for controlled rollback and future configuration testing.

---

# Identity Outcome

- Domain Controller fully operational  
- AD-integrated DNS functional  
- Kerberos authentication validated  
- Service discovery confirmed  
- Domain health verified  

The environment now operates as a centralized identity control plane for MRTG.

Operational Readiness State:

> Identity boundary established.  
> Authentication authority active.  
> Environment prepared for policy-layer enforcement.

---

# Security Perspective

The Domain Controller represents a **Tier 0 identity asset** and must be treated as privileged infrastructure.

Security posture considerations:

- Administrative access restricted to dedicated privileged accounts  
- Standard user logons prohibited on domain controller  
- Continuous monitoring and auditing required  
- Hardened configuration baseline maintained  

Compromise of a domain controller equates to compromise of the entire identity domain.

---

## Next Lab

[Lab-04 — OU Design and GPO Enforcement (Access Control)](../Lab-04-OU-and-GPO-Enforcement/)

Next phase will introduce:

- Organizational Unit (OU) hierarchy design
- Initial security group implementation (RBAC foundation)
- Group Policy configuration and enforcement
- Identity structure aligned to business roles

