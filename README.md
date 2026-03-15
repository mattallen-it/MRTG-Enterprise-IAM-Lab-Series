# Monroe Redstone Technology Group — IAM Lab Series

Purpose

The goal of this project is to build hands-on experience with enterprise Identity and Access Management systems through progressive lab development.

Each lab introduces operational IAM tasks commonly performed by system administrators, identity engineers, and security teams.

![Focus](https://img.shields.io/badge/Career%20Track-Identity%20and%20Access%20Management-darkgreen)
![Security](https://img.shields.io/badge/Certification-Security%2B-red)
![Platform](https://img.shields.io/badge/Environment-Windows%20Enterprise-blue)

# iam-infrastructure-labs
Hands-on Identity and Access Management (IAM) lab series covering Hyper-V infrastructure, Active Directory, authentication, access control, and hybrid identity with Microsoft Entra ID.

Organization

This repository documents a simulated enterprise Identity and Access Management (IAM) environment for Monroe Redstone Technology Group (MRTG).

The lab environment models a small-to-mid enterprise identity infrastructure including:

• Windows Server Active Directory
• Domain-joined workstations
• Identity lifecycle management
• Role-based access control (RBAC)
• Group Policy security baselines
• Privileged access controls
• Hybrid identity integration with Microsoft Entra ID
• Identity monitoring and auditing

Domain

mrtg.local

Systems

DC01      Domain Controller  
CLIENT01  Domain Workstation

Architecture

Hypervisor: Hyper-V (Windows 11 Pro)

Domain Controller
DC01 – Windows Server 2022
Active Directory Domain Services
DNS Server

## Identity Architecture

User → Domain Workstation → Domain Controller → Resource Authorization

CLIENT01 authenticates to DC01 (Active Directory Domain Services) to access controlled resources within the mrtg.local domain.

Client System
CLIENT01 – Windows 11 Enterprise
Domain-joined workstation used for user authentication and policy testing

| Lab | Topic |
|-----|------|
| Lab 01 | Identity Infrastructure Foundation |
| Lab 02 | Active Directory Domain Deployment |
| Lab 03 | Identity Lifecycle and RBAC |
| Lab 04 | Group Policy Security Baselines |
| Lab 05 | Domain Workstation Management |
| Lab 06 | Privileged Access Management |
| Lab 07 | Hybrid Identity (Entra ID) |
| Lab 08 | Identity Monitoring and Auditing |
