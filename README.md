# Monroe Redstone Technology Group — IAM Lab Series
Enterprise Identity Simulation Environment

Hands-on Identity and Access Management (IAM) lab series covering Hyper-V infrastructure, Active Directory identity services, authentication workflows, role-based access control (RBAC), and hybrid identity with Microsoft Entra ID.

![Focus](https://img.shields.io/badge/Career%20Track-Identity%20and%20Access%20Management-darkgreen)
![Security](https://img.shields.io/badge/Certification-Security%2B-red)
![Platform](https://img.shields.io/badge/Environment-Windows%20Enterprise-blue)

## Organization

This repository documents a simulated enterprise Identity and Access Management environment for:

**Monroe Redstone Technology Group (MRTG)**

The lab environment models a small-to-mid enterprise identity infrastructure including:

- Windows Server Active Directory
- Domain-joined workstations
- Identity lifecycle management
- Role-based access control (RBAC)
- Group Policy security baselines
- Privileged access administration
- Hybrid identity integration with Microsoft Entra ID
- Identity monitoring and auditing

---

## Domain

mrtg.local

---

## Systems

| System | Role |
|------|------|
| DC01 | Domain Controller |
| CLIENT01 | Domain Workstation |

---

## Infrastructure Architecture

| Component | Description |
|----------|-------------|
| Hypervisor | Hyper-V (Windows 11 Pro) |
| Domain Controller | **DC01** — Windows Server 2022 |
| Services | Active Directory Domain Services, DNS |
| Client System | **CLIENT01** — Windows 11 Enterprise |

---

## Identity Architecture

Identity authentication and authorization follow a standard enterprise Active Directory model.


User → Domain Workstation → Domain Controller → Resource Authorization


CLIENT01 authenticates to **DC01 (Active Directory Domain Services)** to access controlled resources within the **mrtg.local** domain.

---

## Lab Series Progression

| Lab | Topic |
|-----|------|
| Lab 01 | Identity Infrastructure Foundation |
| Lab 02 | Active Directory Domain Deployment |
| Lab 03 | Identity Lifecycle and RBAC |
| Lab 04 | Group Policy Security Baselines |
| Lab 05 | Domain Workstation Management |
| Lab 06 | Privileged Access Management |
| Lab 07 | Hybrid Identity (Microsoft Entra ID) |
| Lab 08 | Identity Monitoring and Auditing |

---

## Project Goal

This lab series demonstrates practical Identity and Access Management skills used in enterprise and government-regulated environments, including:

- Identity provisioning and lifecycle management
- Role-based access control implementation
- Directory administration
- Security policy enforcement
- Hybrid identity architecture
