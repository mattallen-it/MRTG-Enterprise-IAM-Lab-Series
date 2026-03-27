# Lab 03 — Domain Controller Promotion (Identity Activation)

![Type](https://img.shields.io/badge/type-lab-blue)
![Track](https://img.shields.io/badge/track-IAM-green)
![Technology](https://img.shields.io/badge/technology-Active%20Directory-blue)
![Platform](https://img.shields.io/badge/platform-Windows%20Server%202022-lightgrey)
![Domain](https://img.shields.io/badge/domain-mrtg.local-purple)
![Auth](https://img.shields.io/badge/auth-Kerberos-orange)
![Focus](https://img.shields.io/badge/focus-Identity%20Activation-yellow)
![Level](https://img.shields.io/badge/level-Foundation-darkblue)
![Role](https://img.shields.io/badge/role-Domain%20Controller-critical)

## Overview

Monroe Redstone Technology Group (MRTG) established its centralized identity system by promoting Windows Server to a Domain Controller and creating the internal domain `mrtg.local`.

This lab builds directly on Lab 02 by activating identity services, enabling authentication, authorization, and service discovery within the environment.

This marks the transition from infrastructure preparation to active identity enforcement within the environment.

---

## Objective

Promote Windows Server to a Domain Controller and establish the MRTG Active Directory domain (`mrtg.local`) as the centralized identity authority.

---

## Scope

### Included
- Domain controller promotion (new forest: mrtg.local)
- DNS configuration (AD-integrated)
- Domain authentication validation
- Name resolution testing
- Domain controller health checks
- Hyper-V checkpoint creation

### Not Included
- Organizational Unit (OU) design
- User and group provisioning (beyond default accounts)
- Group Policy Object (GPO) configuration
- Domain-joined workstation setup

---

## Environment

| Component | Value |
|----------|------|
| VM Name | MRTG-DC01 |
| OS | Windows Server 2022 |
| Role | Domain Controller |
| Domain | mrtg.local |
| DNS | AD-integrated |

---

## Architecture

The MRTG-DC01 server functions as the first domain controller within the `mrtg.local` forest, providing:

- Active Directory Domain Services (AD DS)
- AD-integrated DNS

This system functions as the authoritative identity provider (IdP) for MRTG, enforcing authentication and authorization decisions across the domain.

- Authentication (Kerberos)
- Authorization (security principals)
- Service discovery (DNS)

This establishes the identity control plane for MRTG.

---

## Identity Deployment Phases

### Phase 1 — Domain Controller Promotion

![New Forest](images/03-new-forest-mrtg-local.png)

The server was promoted to a domain controller by creating a new Active Directory forest (`mrtg.local`).

This establishes the root of trust for authentication and defines the security boundary for all identity operations within the environment.

This defines the Active Directory forest as the primary security boundary and identity namespace for MRTG.

---

### Phase 2 — Prerequisite Validation

![AD DS Prerequisites](images/02-ad-ds-prerequisites-check.png)

Prerequisite checks were validated to ensure the system meets requirements for secure domain controller promotion and identity service deployment.

---

### Phase 3 — DNS Configuration

![DNS Zones](images/04-dns-zones-mrtg-local.png)

AD-integrated DNS zones were automatically created during promotion, including the primary domain zone and `_msdcs` zone.

These DNS zones enable domain controller discovery and are required for Kerberos authentication and directory services functionality.

---

### Phase 4 — DNS Service Records Validation

![DNS Records](images/06-dns-host-and-service-record.png)

DNS service records confirm proper domain controller registration and enable service discovery within the domain.

---

### Phase 5 — Network Configuration Validation

![IP Config](images/07-ipconfig-domain-controller.png)

Validated static IP configuration and confirmed the domain controller is configured to use itself for DNS resolution.

---

### Phase 6 — Authentication and Name Resolution Validation

![Authentication Validation](images/08-domain-authentication-validation.png)

These validations confirm that authentication, authorization context, and DNS-based service discovery are functioning correctly across the domain.

- Successful domain login using `MRTG\Administrator`
- Verified domain context (`whoami`, `%USERDOMAIN%`)
- Confirmed name resolution using `ping mrtg.local`
- Validated Kerberos-based authentication within domain context

---

### Phase 7 — Infrastructure Baseline Checkpoint

![Checkpoint](images/09-post-dc-promotion-checkpoint.png)

A Hyper-V checkpoint was created to preserve a stable domain controller baseline for future labs.

---

## Identity Outcome

- Successfully promoted server to Domain Controller for `mrtg.local`
- Established centralized identity authority using Active Directory
- Configured AD-integrated DNS for service discovery
- Validated authentication and domain connectivity
- Verified domain controller configuration and network settings
- Established a functional Kerberos-based authentication environment with centralized identity control

This environment now operates as a centralized identity control plane, supporting Kerberos-based authentication, directory-backed authorization, and reliable service discovery across domain-joined systems.

---

## Security Perspective

The domain controller is a Tier 0 asset and represents the core of enterprise identity infrastructure.

Active Directory authentication is dependent on DNS for service discovery (SRV records), making DNS integrity critical to identity security.

Compromise of a domain controller results in full domain compromise, emphasizing the need for strict access control, monitoring, and hardening in production environments.

Domain controllers must be treated as highly privileged systems (Tier 0) with restricted administrative access and continuous monitoring.

All administrative access to domain controllers should be restricted, monitored, and separated from standard user operations to reduce risk of privilege escalation and domain compromise.

Domain controllers should be isolated, hardened, and monitored as part of a privileged access management strategy.

Administrative access to domain controllers should be restricted to dedicated privileged accounts and never performed from standard user contexts.

---

## Next Lab

[Lab-04 — OU Design and GPO Enforcement (Access Control)](../Lab-04-OU-and-GPO-Implementation/README.md)

Planned focus:

* Designing Organizational Unit (OU) structure aligned to business roles
* Implementing identity segmentation using OU hierarchy
* Enforcing security policies using Group Policy Objects (GPOs)
* Applying access control through security group membership
