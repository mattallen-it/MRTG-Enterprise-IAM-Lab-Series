# Lab 01 — Virtualization and Identity Infrastructure Foundation

![Platform](https://img.shields.io/badge/Platform-Hyper--V-blue)
![Technology](https://img.shields.io/badge/Technology-Virtualization-blue)
![Focus](https://img.shields.io/badge/Focus-Infrastructure%20Foundation-green)
![Security](https://img.shields.io/badge/Security-Lab%20Isolation-red)
![Validation](https://img.shields.io/badge/Validation-Completed-brightgreen)
![Documentation](https://img.shields.io/badge/Documentation-Audit%20Ready-purple)

---

## Objective

The objective of this lab is to establish the virtualization foundation for the Monroe Redstone Technology Group IAM lab environment.

This lab creates the base infrastructure required to support future Active Directory, identity management, security policy, access control, endpoint, monitoring, and governance labs.

Lab 01 focuses on preparing a controlled Hyper-V environment that can support domain controllers, client systems, and future infrastructure services.

---

## Business Problem

Monroe Redstone Technology Group needs a safe and repeatable lab environment for building and validating identity infrastructure.

Before Active Directory, Group Policy, access control, or IAM governance can be implemented, the environment needs a stable virtualization foundation.

This lab addresses the need to:

- Create a controlled virtual lab environment
- Separate lab systems from production or personal systems
- Prepare virtual machines for domain and endpoint services
- Support future Active Directory and IAM labs
- Establish a consistent infrastructure baseline
- Create a foundation for rollback, testing, and validation

---

## Lab Summary

In this lab, I prepared the virtualization foundation for the MRTG IAM lab series.

The environment was designed to support:

- A primary domain controller
- Additional infrastructure servers
- A domain-joined client workstation
- Future logging and monitoring services
- Isolated lab networking
- Repeatable validation and documentation

This lab serves as the starting point for the full IAM lab series.

---

## Environment

| Component | Details |
|---|---|
| Host Platform | Windows 11 Pro |
| Hypervisor | Hyper-V |
| Lab Organization | Monroe Redstone Technology Group |
| Primary Domain Controller | `MRTG-DC01` |
| Client Workstation | `MRTG-CLIENT-01` |
| Network Type | Internal / isolated lab network |
| Purpose | IAM infrastructure foundation |

---

## Virtualization Foundation

Hyper-V was used as the virtualization platform for the lab environment.

Hyper-V provides a controlled environment for building and testing identity infrastructure without affecting the host system.

This allows future labs to safely configure and validate:

- Active Directory Domain Services
- DNS
- DHCP
- Group Policy
- Domain-joined clients
- Security baselines
- Logging and monitoring
- IAM governance controls

---

## Lab Network Design

The lab network was designed to support identity infrastructure services in an isolated environment.

| Network Component | Purpose |
|---|---|
| Hyper-V Virtual Switch | Provides connectivity between lab systems |
| Domain Controller | Provides directory, DNS, and authentication services in later labs |
| Client Workstation | Used to validate domain join, policy enforcement, and endpoint controls |
| Future Servers | Support logging, monitoring, and additional services |

---

## Planned Lab Systems

| System | Role |
|---|---|
| `MRTG-DC01` | Primary domain controller for future Active Directory deployment |
| `MRTG-CLIENT-01` | Domain-joined workstation for validation and testing |
| `MRTG-DC02` | Future additional domain controller for resilience and replication |
| `MRTG-LOG01` | Future logging and SIEM server |

---

## Infrastructure Design Principles

This lab was built around the following principles:

- Keep the lab isolated from personal or production systems
- Use clear VM naming standards
- Separate server and client roles
- Build infrastructure in phases
- Preserve a clean baseline for future labs
- Document each major configuration step
- Create evidence that supports future troubleshooting and review

---

## Naming Standard

A consistent naming standard was used for lab systems.

| Name | Purpose |
|---|---|
| `MRTG-DC01` | Primary domain controller |
| `MRTG-DC02` | Additional domain controller |
| `MRTG-CLIENT-01` | Domain-joined workstation |
| `MRTG-LOG01` | Logging and SIEM server |

The `MRTG` prefix represents Monroe Redstone Technology Group and keeps the lab systems clearly grouped.

---

## Virtual Machine Planning

The lab infrastructure was planned with separate roles for server and client systems.

| VM | Operating System | Planned Role |
|---|---|---|
| `MRTG-DC01` | Windows Server 2022 | Primary domain controller |
| `MRTG-CLIENT-01` | Windows 11 Enterprise | Client validation system |
| `MRTG-DC02` | Windows Server 2022 | Future additional domain controller |
| `MRTG-LOG01` | Windows Server 2022 | Future logging/SIEM server |

---

## Security Considerations

Even though this is a lab environment, security-focused decisions were still applied.

The lab was designed to support:

- Isolated testing
- Controlled identity infrastructure
- Separate administrative systems
- Repeatable rollback points
- Safe experimentation
- Evidence collection
- Future IAM governance validation

This approach supports realistic enterprise and government-style IT operations.

---

## Risk Addressed

Without a controlled virtualization foundation, identity labs can become inconsistent, difficult to troubleshoot, and difficult to rebuild.

This lab reduces that risk by establishing a structured and repeatable base environment before deploying Active Directory services.

The main risks addressed include:

- Uncontrolled lab sprawl
- Inconsistent system naming
- Lack of infrastructure planning
- No clear separation between server and client roles
- Weak foundation for future Active Directory labs
- Difficulty reproducing or troubleshooting future configurations

---

## Control Mapping

This lab supports the following IAM and infrastructure concepts:

| Control Area | How This Lab Supports It |
|---|---|
| Infrastructure foundation | Establishes the virtual environment for future IAM labs |
| Change control readiness | Creates a stable base for phased configuration |
| Operational consistency | Uses standard names and planned roles |
| Security isolation | Keeps identity testing inside a controlled lab |
| Documentation | Records the infrastructure foundation |
| Future audit readiness | Supports evidence collection in later labs |

---

## Validation

The following validation checks were completed:

| Validation Item | Result |
|---|---|
| Hyper-V available on host system | Passed |
| Virtual lab environment planned | Passed |
| Lab VM naming standard established | Passed |
| Server and client roles identified | Passed |
| Lab network design established | Passed |
| Future IAM infrastructure path defined | Passed |

---

## Evidence Collected

The following evidence should be collected for this lab:

| Evidence | File |
|---|---|
| Hyper-V Manager overview | `images/lab01-hyperv-manager-overview.png` |
| Virtual switch configuration | `images/lab01-virtual-switch-configuration.png` |
| VM inventory / planned systems | `images/lab01-vm-inventory.png` |
| DC01 VM settings | `images/lab01-dc01-vm-settings.png` |
| CLIENT01 VM settings | `images/lab01-client01-vm-settings.png` |
| Lab network summary | `images/lab01-lab-network-summary.png` |

---

## What I Would Improve in Production

In a production environment, I would improve this process by:

- Using formal infrastructure design documentation
- Defining resource requirements before deployment
- Documenting network segmentation and firewall rules
- Using approved naming standards
- Tracking infrastructure assets in a CMDB
- Applying baseline security hardening before production use
- Defining backup and recovery requirements
- Documenting administrative ownership
- Validating licensing and support requirements
- Using formal change management before deploying infrastructure

---

## Lessons Learned

This lab reinforced that identity infrastructure starts before Active Directory is installed.

A stable virtualization foundation makes the rest of the IAM environment easier to build, troubleshoot, document, and validate.

The key lesson is that good IAM labs need structure from the beginning. Naming standards, VM roles, network planning, and documentation all matter because they support every future identity and access control lab.

---

## Outcome

Lab 01 successfully established the virtualization and infrastructure foundation for the MRTG IAM lab series.

The lab prepared the environment for future work involving:

- Active Directory deployment
- Domain controller promotion
- DNS and DHCP services
- Domain-joined client validation
- Group Policy enforcement
- Access control testing
- Logging and monitoring
- IAM governance

This lab created the base environment required for the rest of the IAM lab series.

---

## Next Lab

[Lab 02 — AD DS Deployment](../Lab-02-AD-DS-Deployment)

Lab 02 will focus on installing the Active Directory Domain Services role and preparing the server for domain controller promotion.
