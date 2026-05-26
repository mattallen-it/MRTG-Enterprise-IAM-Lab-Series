# Monroe Redstone Technology Group — IAM Lab Series

![IAM](https://img.shields.io/badge/IAM-Enterprise-blue)
![Active Directory](https://img.shields.io/badge/Directory-Active_Directory-2A628C)
![Security](https://img.shields.io/badge/Security-Policy_&_Access_Control-red)
![Platform](https://img.shields.io/badge/Platform-Windows_Enterprise-lightgrey)
![Focus](https://img.shields.io/badge/Focus-Identity_Governance-purple)

---
## Original Foundation Series Status

**Status:** Complete  
**Original Foundation Labs:** 24  
**Expansion Labs:** 25–30 planned  
**Focus:** Active Directory, IAM operations, access control, automation, recovery, audit, and operational handoff

This lab series demonstrates a complete enterprise-style IAM environment built from the ground up and validated through a final capstone lab.

The series covers identity infrastructure, access control, Group Policy, delegation, logging, Windows LAPS, Active Directory Certificate Services, identity lifecycle automation, backup and recovery, IAM audit evidence, and operational handoff documentation.

This project simulates a structured enterprise Identity and Access Management environment for Monroe Redstone Technology Group (MRTG).

The foundation series demonstrates how identity infrastructure is deployed, governed, and secured using Active Directory, with emphasis on policy enforcement, role-based access control (RBAC), and auditability within regulated environments.

---

Core implementation areas:

- Active Directory Domain Services (AD DS)
- Organizational Unit (OU) design
- Group Policy Object (GPO) enforcement
- Role-Based Access Control (RBAC)
- Security group-based access control
- Password and account lockout policy
- Fine-grained password policy
- DHCP services for identity infrastructure
- Additional domain controller deployment
- Active Directory replication validation
- Centralized logging and event forwarding
- Group Policy security baselines
- Delegation of control
- Windows LAPS and local administrator protection
- Active Directory Certificate Services (AD CS)
- Identity lifecycle automation with PowerShell
- Directory backup and recovery readiness
- IAM security review and access control auditing
- SOPs, runbooks, and operational handoff documentation
- Enterprise IAM capstone validation

The focus is on practical IAM operations aligned with enterprise and government environments, emphasizing least privilege, centralized access control, and auditability.

---

## Objectives

- Design, implement, and secure an enterprise identity environment
- Deploy and validate Active Directory Domain Services (AD DS)
- Enforce identity-based access control using groups and GPOs
- Validate policy enforcement across domain-joined systems
- Implement delegated administration and least privilege controls
- Automate identity lifecycle workflows with PowerShell
- Validate backup, recovery, audit, and operational handoff readiness
- Align identity operations with governance-oriented IT practices

---

## Organization

This repository represents a structured IAM implementation for:

**Monroe Redstone Technology Group (MRTG)**

Core identity components include:

- Active Directory Domain Services (AD DS)
- Domain-joined endpoints
- Organizational Unit (OU) hierarchy
- Role-Based Access Control (RBAC)
- Group Policy-based security enforcement
- Department-based security groups
- Delegated administration
- Windows LAPS local administrator protection
- Active Directory Certificate Services (AD CS)
- Identity lifecycle automation with PowerShell
- Backup, recovery, and operational resilience
- IAM security review and audit evidence
- Runbooks, SOPs, and operational handoff documentation

---

## Domain

- **Domain Name:** mrtg.local  
- **Directory Services:** Active Directory Domain Services (AD DS)  
- **Authentication Model:** Kerberos-based domain authentication  

---

## Systems

### MRTG-DC01 — Primary Domain Controller

- Active Directory Domain Services
- DNS
- Group Policy Management
- Global Catalog
- Replication partner for MRTG-DC02
- FSMO role holder

### MRTG-DC02 — Additional Domain Controller

- Active Directory Domain Services
- DNS
- Global Catalog
- AD replication validation
- Directory resilience support

### MRTG-CLIENT-01 — Domain-Joined Workstation

- Policy enforcement validation
- Authentication testing
- Access control validation

---

## Infrastructure Architecture

| Component | Description |
|---|---|
| Hypervisor | Hyper-V (Windows 11 Pro Host) |
| Primary Domain Controller | MRTG-DC01 — Windows Server 2022 |
| Additional Domain Controller | MRTG-DC02 — Windows Server 2022 |
| Services | AD DS, DNS, Group Policy, Global Catalog, AD Replication |
| Client System | MRTG-CLIENT-01 — Windows 11 Enterprise |
---

## Identity Architecture

Authentication and authorization are centralized through Active Directory Domain Services (AD DS).

Access control is enforced through:

- Organizational Unit (OU) structure for policy scoping  
- Group Policy Objects (GPO) for configuration enforcement  
- Security groups for Role-Based Access Control (RBAC)  

This architecture supports:

- Least privilege  
- Centralized identity governance  
- Policy-driven enforcement  
- Auditability
- Delegated administration
- Lifecycle automation
- Recovery and audit readiness

---

## Lab Series Progression

| Lab | Topic |
|---|---|
| Lab-01 — Virtualization and Identity Infrastructure Foundation | Environment Buildout |
| Lab-02 — AD DS Deployment | Identity Platform Deployment |
| Lab-03 — Domain Controller Promotion | Identity Activation |
| Lab-04 — OU Design and GPO Enforcement | Policy & Access Control |
| Lab-05 — Identity Lifecycle Management | Joiner / Mover / Leaver |
| Lab-06 — NTFS and Share Permissions | Resource Access Control |
| Lab-07 — Service Accounts and Delegation | Privileged Identity Management |
| Lab-08 — Identity Monitoring and Auditing | Security & Compliance |
| Lab-09 — Password Policy and Account Lockout Hardening | Authentication Hardening |
| Lab-10 — Fine-Grained Password Policies for Tiered Identity Control | Tiered Authentication Control |
| Lab-11 — DHCP Services for Enterprise Identity Infrastructure | Identity-Supporting Network Services |
| Lab-12 — Additional Domain Controller and AD Replication | Directory Resilience |
| Lab-13 — Centralized Logging and Event Forwarding for Identity Events | Visibility & Audit Collection |
| Lab-14 — Active Directory Sites and Services for Replication Topology | Replication Topology |
| Lab-15 — Group Policy Security Baselines for Workstations and Servers | Endpoint Security Control |
| Lab-16 — Delegation of Control and Tiered Administrative Boundaries | Least Privilege Administration |
| Lab-17 — Windows LAPS and Local Administrator Control | Privileged Endpoint Protection |
| Lab-18 — Group-Based Access Control for File and Department Resources | Authorization Design |
| Lab-19 — Active Directory Certificate Services | Enterprise Trust Services |
| Lab-20 — Identity Lifecycle Automation with PowerShell | Identity Automation |
| Lab-21 — Directory Recovery, Backup, and Operational Resilience | Identity Recovery & Continuity |
| Lab-22 — IAM Security Review and Access Control Audit | Identity Risk & Access Review |
| Lab-23 — IAM Runbooks, SOPs, and Operational Handoff | Operational Documentation |
| Lab-24 — Enterprise IAM Capstone Validation | End-to-End IAM Validation |
---

## Series Expansion: Labs 25–30

After completing the original 24-lab IAM foundation series, this expansion focuses on identity governance, service account control, endpoint recovery, local administrator review, SIEM identity monitoring, and operational IAM maturity.

| Lab | Title | Focus |
|---|---|---|
| 25 | Service Account Governance Foundation | Non-human identity inventory, ownership, and risk review |
| 26 | Scheduled Task with Least-Privilege Service Account | Service account usage, least privilege, and task execution |
| 27 | BitLocker & Endpoint Encryption Recovery | Endpoint encryption, recovery key handling, and layered security |
| 28 | Local Administrator Access Review & Remediation | Privileged access review and local administrator cleanup |
| 29 | SIEM Identity Monitoring with Splunk | Identity event collection, detection, and alerting |
| 30 | IAM Operations, Monitoring, and Governance Capstone | IAM governance, monitoring, evidence review, and operational maturity |

---

## Enterprise IAM Objectives

The original 24-lab foundation series demonstrates structured IAM implementation within an enterprise-style Active Directory environment. Labs 25–30 expand the project into IAM governance, privileged access review, endpoint recovery, SIEM monitoring, and operational maturity.

Core focus areas:

- Build and validate Active Directory Domain Services
- Design a scalable OU and group structure
- Enforce access control through security groups
- Implement Group Policy for identity and endpoint security
- Apply password, account lockout, and fine-grained password policies
- Validate domain controller replication and directory resilience
- Implement centralized logging for identity events
- Configure delegation of control and tiered administrative boundaries
- Implement Windows LAPS for local administrator protection
- Deploy Active Directory Certificate Services
- Automate Joiner, Mover, and Leaver identity workflows
- Validate directory backup and recovery readiness
- Perform IAM security review and access control auditing
- Create operational SOPs, runbooks, and handoff documentation
- Validate the full environment through an enterprise IAM capstone

---

## Quick Access

- [Lab-01 — Virtualization and Identity Infrastructure Foundation](./Lab-01-Virtualization-and-Identity-Infrastructure-Foundation/)
- [Lab-02 — AD DS Deployment](./Lab-02-AD-DS-Deployment/)
- [Lab-03 — Domain Controller Promotion](./Lab-03-Domain-Controller-Promotion/)
- [Lab-04 — OU Design and GPO Enforcement](./Lab-04-OU-Design-and-GPO-Enforcement/)
- [Lab-05 — Identity Lifecycle Management](./Lab-05-Identity-Lifecycle-Management/)
- [Lab-06 — NTFS and Share Permissions](./Lab-06-NTFS-and-Share-Permissions/)
- [Lab-07 — Service Accounts and Delegation](./Lab-07-Service-Accounts-and-Delegation/)
- [Lab-08 — Identity Monitoring and Auditing](./Lab-08-Identity-Monitoring-and-Auditing/)
- [Lab-09 — Password Policy and Account Lockout Hardening](./Lab-09-Password-Policy-and-Account-Lockout-Hardening/)
- [Lab-10 — Fine-Grained Password Policies for Tiered Identity Control](./Lab-10-Fine-Grained-Password-Policies-for-Tiered-Identity-Control/)
- [Lab-11 — DHCP Services for Enterprise Identity Infrastructure](./Lab-11-DHCP-Services-for-Enterprise-Identity-Infrastructure/)
- [Lab-12 — Additional Domain Controller and AD Replication](Lab-12-Additional-Domain-Controller-and-AD-Replication)
- [Lab-13 — Centralized Logging and Event Forwarding for Identity Events](Lab-13-Centralized-Logging-and-Event-Forwarding-for-Identity-Events)
- [Lab-14 — Active Directory Sites and Services for Replication Topology](Lab-14-Active-Directory-Sites-and-Services-for-Replication-Topology)
- [Lab-15 — Group Policy Security Baselines for Workstations and Servers](./Lab-15-Group-Policy-Security-Baselines-for-Workstations-and-Servers)
- [Lab-16 — Delegation of Control and Tiered Administrative Boundaries](./Lab-16-Delegation-of-Control-and-Tiered-Administrative-Boundaries)
- [Lab-17 — Windows LAPS and Local Administrator Control](./Lab-17-Windows-LAPS-and-Local-Administrator-Control)
- [Lab-18 — Group-Based Access Control for File and Department Resources](Lab-18-Group-Based-Access-Control-for-File-and-Department-Resources)
- [Lab-19 — Active Directory Certificate Services](Lab-19-Active-Directory-Certificate-Services/)
- [Lab-20 — Identity Lifecycle Automation with PowerShell](Lab-20-Identity-Lifecycle-Automation-with-PowerShell/)
- [Lab-21 — Directory Recovery, Backup, and Operational Resilience](Lab-21-Directory-Recovery-Backup-and-Operational-Resilience/)
- [Lab-22 — IAM Security Review and Access Control Audit](Lab-22-IAM-Security-Review-and-Access-Control-Audit/)
- [Lab-23 — IAM Runbooks, SOPs, and Operational Handoff](Lab-23-IAM-Runbooks-SOPs-Operational-Handoff/)
- [Lab-24 — Enterprise IAM Capstone Validation](Lab-24-Enterprise-IAM-Capstone-Validation/)

## Original Foundation Series Completion

The original 24-lab MRTG Enterprise IAM foundation series is complete.

The final environment includes:

- Primary and additional domain controllers
- Validated replication and domain health
- Structured OU hierarchy
- Department-based access groups
- Group Policy security baselines
- Password and account lockout controls
- Fine-grained password policies
- Delegated administration
- Windows LAPS-managed domain controller
- Active Directory Certificate Services
- Identity lifecycle automation
- System State backup and recovery artifacts
- IAM audit exports and security review summary
- SOP, runbook, and operational handoff documentation
- Final enterprise IAM capstone validation

The completed foundation series shows a practical, enterprise-style IAM environment built, secured, automated, backed up, audited, documented, and validated.
