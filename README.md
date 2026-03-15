# Monroe Redstone Technology Group — IAM Lab Series

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

Client System
CLIENT01 – Windows 11 Enterprise
Domain-joined workstation used for user authentication and policy testing

| Lab | Topic |
|-----|------|
| Lab 01 | Identity Infrastructure Foundation |
| Lab 02 | Active Directory Domain Deployment |
| Lab 03 | Identity Lifecycle and RBAC |
| Lab 04 | Group Policy Security Baselines |
| Lab 05 | Domain Workstation Administration |
| Lab 06 | Privileged Access Management |
| Lab 07 | Hybrid Identity (Entra ID) |
| Lab 08 | Identity Monitoring and Auditing |
