# Monroe Redstone Technology Group — IAM Lab Series

![IAM](https://img.shields.io/badge/IAM-Enterprise-blue)
![Active Directory](https://img.shields.io/badge/Directory-Active_Directory-2A628C)
![Security](https://img.shields.io/badge/Security-Policy_&_Access_Control-red)
![Platform](https://img.shields.io/badge/Platform-Windows_Enterprise-lightgrey)
![Focus](https://img.shields.io/badge/Focus-Identity_Governance-purple)
![Status](https://img.shields.io/badge/Status-Complete-brightgreen)

---

## Series Status

**Status:** Complete  
**Original Foundation Labs:** 24  
**Expansion Labs:** 25–30 complete  
**Total Labs:** 30  
**Focus:** Active Directory, IAM operations, access control, automation, recovery, audit, monitoring, governance, and operational handoff

This repository documents a complete enterprise-style Identity and Access Management lab environment for **Monroe Redstone Technology Group (MRTG)**.

The lab series demonstrates how identity infrastructure is deployed, secured, governed, monitored, documented, and validated using Active Directory and supporting Windows security technologies.

The project begins with foundational Active Directory implementation and expands into operational IAM maturity through service account governance, least-privilege automation, endpoint encryption, local administrator remediation, SIEM identity monitoring, and a final governance capstone.

---

## Project Overview

This project simulates a structured enterprise IAM environment for Monroe Redstone Technology Group.

The environment is built around Active Directory Domain Services and focuses on real-world identity operations, including:

- Directory services deployment
- Organizational Unit design
- Group Policy enforcement
- Role-Based Access Control
- Department-based access control
- Delegation of control
- Password and account lockout hardening
- Fine-grained password policies
- DHCP services
- Additional domain controller deployment
- Active Directory replication validation
- Centralized logging
- Windows LAPS
- Active Directory Certificate Services
- Identity lifecycle automation
- Directory backup and recovery
- IAM security review
- SOPs and operational handoff
- Service account governance
- Endpoint encryption
- Local administrator remediation
- SIEM identity monitoring
- IAM operations governance

The focus is practical IAM operations aligned with enterprise and government-regulated IT environments, emphasizing least privilege, centralized access control, auditability, and operational readiness.

---

## Objectives

- Design, implement, and secure an enterprise identity environment
- Deploy and validate Active Directory Domain Services
- Enforce identity-based access control using groups and Group Policy
- Validate policy enforcement across domain-joined systems
- Implement delegated administration and least privilege controls
- Automate identity lifecycle workflows with PowerShell
- Validate backup, recovery, audit, and operational handoff readiness
- Review service account ownership, scope, and privilege exposure
- Validate endpoint encryption and recovery readiness
- Review and remediate local administrator exposure
- Install and validate SIEM identity monitoring with Splunk
- Perform a final IAM operations and governance capstone
- Align identity operations with governance-oriented IT practices

---

## Organization

This repository represents a structured IAM implementation for:

**Monroe Redstone Technology Group (MRTG)**

Core identity components include:

- Active Directory Domain Services
- Domain-joined endpoints
- Organizational Unit hierarchy
- Role-Based Access Control
- Group Policy-based security enforcement
- Department-based security groups
- Delegated administration
- Windows LAPS local administrator protection
- Active Directory Certificate Services
- Identity lifecycle automation with PowerShell
- Backup, recovery, and operational resilience
- IAM security review and audit evidence
- Runbooks, SOPs, and operational handoff documentation
- Service account governance
- Endpoint encryption
- Local administrator review and remediation
- SIEM identity monitoring with Splunk
- IAM operations governance review

---

## Domain

| Item | Value |
|---|---|
| Domain Name | `mrtg.local` |
| Directory Services | Active Directory Domain Services |
| Authentication Model | Kerberos-based domain authentication |
| Primary Identity Platform | Active Directory |
| Lab Organization | Monroe Redstone Technology Group |

---

## Systems

### MRTG-DC01 — Primary Domain Controller

- Active Directory Domain Services
- DNS
- Group Policy Management
- Global Catalog
- Replication partner for MRTG-DC02
- FSMO role holder
- IAM governance review system

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
- BitLocker endpoint encryption
- Local administrator access review
- Endpoint security validation

### MRTG-LOG01 — Logging and SIEM Server

- Splunk Enterprise
- Windows Security Event Log ingestion
- Identity event monitoring
- Successful and failed logon searches
- Security group membership change review
- SIEM search validation

---

## Infrastructure Architecture

| Component | Description |
|---|---|
| Hypervisor | Hyper-V on Windows 11 Pro Host |
| Primary Domain Controller | MRTG-DC01 — Windows Server 2022 |
| Additional Domain Controller | MRTG-DC02 — Windows Server 2022 |
| Logging / SIEM Server | MRTG-LOG01 — Windows Server 2022 with Splunk Enterprise |
| Client System | MRTG-CLIENT-01 — Windows 11 Enterprise |
| Core Services | AD DS, DNS, Group Policy, Global Catalog, AD Replication |
| Security Services | Windows Security Event Logging, BitLocker, Windows LAPS, AD CS |
| Monitoring Services | Splunk Enterprise SIEM monitoring |

---

## Identity Architecture

Authentication and authorization are centralized through Active Directory Domain Services.

Access control is enforced through:

- Organizational Unit structure for policy scoping
- Group Policy Objects for configuration enforcement
- Security groups for Role-Based Access Control
- Delegated administration for least-privilege operations
- Service account governance for non-human identities
- Local administrator controls for endpoint privilege reduction
- SIEM monitoring for identity-related event review

This architecture supports:

- Least privilege
- Centralized identity governance
- Policy-driven enforcement
- Auditability
- Delegated administration
- Lifecycle automation
- Recovery readiness
- Monitoring and operational review

---

## Lab Series Progression

| Lab | Topic | Focus |
|---|---|---|
| Lab-01 | Virtualization and Identity Infrastructure Foundation | Environment Buildout |
| Lab-02 | AD DS Deployment Preparation | Identity Platform Preparation |
| Lab-03 | Domain Controller Promotion | Identity Activation |
| Lab-04 | OU Design and GPO Enforcement | Policy and Access Control |
| Lab-05 | Identity Lifecycle Management | Joiner / Mover / Leaver |
| Lab-06 | NTFS and Share Permissions | Resource Access Control |
| Lab-07 | Service Accounts and Delegation | Privileged Identity Management |
| Lab-08 | Identity Monitoring and Auditing | Security and Compliance |
| Lab-09 | Password Policy and Account Lockout Hardening | Authentication Hardening |
| Lab-10 | Fine-Grained Password Policies for Tiered Identity Control | Tiered Authentication Control |
| Lab-11 | DHCP Services for Enterprise Identity Infrastructure | Identity-Supporting Network Services |
| Lab-12 | Additional Domain Controller and AD Replication | Directory Resilience |
| Lab-13 | Centralized Logging and Event Forwarding for Identity Events | Visibility and Audit Collection |
| Lab-14 | Active Directory Sites and Services for Replication Topology | Replication Topology |
| Lab-15 | Group Policy Security Baselines for Workstations and Servers | Endpoint Security Control |
| Lab-16 | Delegation of Control and Tiered Administrative Boundaries | Least Privilege Administration |
| Lab-17 | Windows LAPS and Local Administrator Control | Privileged Endpoint Protection |
| Lab-18 | Group-Based Access Control for File and Department Resources | Authorization Design |
| Lab-19 | Active Directory Certificate Services | Enterprise Trust Services |
| Lab-20 | Identity Lifecycle Automation with PowerShell | Identity Automation |
| Lab-21 | Directory Recovery, Backup, and Operational Resilience | Identity Recovery and Continuity |
| Lab-22 | IAM Security Review and Access Control Audit | Identity Risk and Access Review |
| Lab-23 | IAM Runbooks, SOPs, and Operational Handoff | Operational Documentation |
| Lab-24 | Enterprise IAM Capstone Validation | End-to-End IAM Validation |
| Lab-25 | Service Account Governance Foundation | Non-Human Identity Governance |
| Lab-26 | Scheduled Task with Least-Privilege Service Account | Least-Privilege Automation |
| Lab-27 | BitLocker and Endpoint Encryption Recovery | Endpoint Encryption and Recovery |
| Lab-28 | Local Administrator Access Review and Remediation | Privileged Access Cleanup |
| Lab-29 | SIEM Identity Monitoring with Splunk | Identity Event Monitoring |
| Lab-30 | IAM Operations, Monitoring, and Governance Capstone | Operational IAM Governance |

---

## Series Expansion: Labs 25–30

After completing the original 24-lab IAM foundation series, this expansion focused on identity governance, service account control, endpoint recovery, local administrator review, SIEM identity monitoring, and operational IAM maturity.

| Lab | Title | Focus |
|---|---|---|
| 25 | Service Account Governance Foundation | Non-human identity inventory, ownership, and risk review |
| 26 | Scheduled Task with Least-Privilege Service Account | Service account usage, least privilege, and task execution |
| 27 | BitLocker and Endpoint Encryption Recovery | Endpoint encryption, recovery key handling, and layered security |
| 28 | Local Administrator Access Review and Remediation | Privileged access review and local administrator cleanup |
| 29 | SIEM Identity Monitoring with Splunk | Identity event collection, detection, and monitoring |
| 30 | IAM Operations, Monitoring, and Governance Capstone | IAM governance, monitoring, evidence review, and operational maturity |

This expansion completes the transition from foundational Active Directory administration into operational IAM governance.

The final six labs focus on non-human identity governance, least-privilege automation, endpoint encryption, local administrator remediation, SIEM identity monitoring, and a final governance capstone.

---

## Enterprise IAM Objectives

The complete 30-lab series demonstrates structured IAM implementation within an enterprise-style Active Directory environment.

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
- Review and document service accounts
- Validate least-privilege scheduled task execution
- Enable endpoint encryption with BitLocker
- Review and remediate local administrator access
- Install and validate Splunk SIEM identity monitoring
- Complete an operational IAM governance capstone

---

## Quick Access

- [Lab-01 — Virtualization and Identity Infrastructure Foundation](./Lab-01-Virtualization-and-Identity-Infrastructure-Foundation/)
- [Lab-02 — AD DS Deployment Preparation](./Lab-02-AD-DS-Deployment-Preparation/)
- [Lab-03 — Domain Controller Promotion](./Lab-03-Domain-Controller-Promotion/)
- [Lab-04 — OU Design and GPO Enforcement](./Lab-04-OU-Design-and-GPO-Enforcement/)
- [Lab-05 — Identity Lifecycle Management](./Lab-05-Identity-Lifecycle-Management/)
- [Lab-06 — NTFS and Share Permissions](./Lab-06-NTFS-and-Share-Permissions/)
- [Lab-07 — Service Accounts and Delegation](./Lab-07-Service-Accounts-and-Delegation/)
- [Lab-08 — Identity Monitoring and Auditing](./Lab-08-Identity-Monitoring-and-Auditing/)
- [Lab-09 — Password Policy and Account Lockout Hardening](./Lab-09-Password-Policy-and-Account-Lockout-Hardening/)
- [Lab-10 — Fine-Grained Password Policies for Tiered Identity Control](./Lab-10-Fine-Grained-Password-Policies-for-Tiered-Identity-Control/)
- [Lab-11 — DHCP Services for Enterprise Identity Infrastructure](./Lab-11-DHCP-Services-for-Enterprise-Identity-Infrastructure/)
- [Lab-12 — Additional Domain Controller and AD Replication](./Lab-12-Additional-Domain-Controller-and-AD-Replication/)
- [Lab-13 — Centralized Logging and Event Forwarding for Identity Events](./Lab-13-Centralized-Logging-and-Event-Forwarding-for-Identity-Events/)
- [Lab-14 — Active Directory Sites and Services for Replication Topology](./Lab-14-Active-Directory-Sites-and-Services-for-Replication-Topology/)
- [Lab-15 — Group Policy Security Baselines for Workstations and Servers](./Lab-15-Group-Policy-Security-Baselines-for-Workstations-and-Servers/)
- [Lab-16 — Delegation of Control and Tiered Administrative Boundaries](./Lab-16-Delegation-of-Control-and-Tiered-Administrative-Boundaries/)
- [Lab-17 — Windows LAPS and Local Administrator Control](./Lab-17-Windows-LAPS-and-Local-Administrator-Control/)
- [Lab-18 — Group-Based Access Control for File and Department Resources](./Lab-18-Group-Based-Access-Control-for-File-and-Department-Resources/)
- [Lab-19 — Active Directory Certificate Services](./Lab-19-Active-Directory-Certificate-Services/)
- [Lab-20 — Identity Lifecycle Automation with PowerShell](./Lab-20-Identity-Lifecycle-Automation-with-PowerShell/)
- [Lab-21 — Directory Recovery, Backup, and Operational Resilience](./Lab-21-Directory-Recovery-Backup-and-Operational-Resilience/)
- [Lab-22 — IAM Security Review and Access Control Audit](./Lab-22-IAM-Security-Review-and-Access-Control-Audit/)
- [Lab-23 — IAM Runbooks, SOPs, and Operational Handoff](./Lab-23-IAM-Runbooks-SOPs-Operational-Handoff/)
- [Lab-24 — Enterprise IAM Capstone Validation](./Lab-24-Enterprise-IAM-Capstone-Validation/)
- [Lab-25 — Service Account Governance Foundation](./Lab-25-Service-Account-Governance-Foundation/)
- [Lab-26 — Scheduled Task with Least-Privilege Service Account](./Lab-26-Scheduled-Task-with-Least-Privilege-Service-Account/)
- [Lab-27 — BitLocker and Endpoint Encryption Recovery](./Lab-27-BitLocker-and-Endpoint-Encryption-Recovery/)
- [Lab-28 — Local Administrator Access Review and Remediation](./Lab-28-Local-Administrator-Access-Review-and-Remediation/)
- [Lab-29 — SIEM Identity Monitoring with Splunk](./Lab-29-SIEM-Identity-Monitoring-with-Splunk/)
- [Lab-30 — IAM Operations, Monitoring, and Governance Capstone](./Lab-30-IAM-Operations-Monitoring-and-Governance-Capstone/)

---

## Original Foundation Series Completion

The original 24-lab MRTG Enterprise IAM foundation series is complete.

The final foundation environment includes:

- Primary and additional domain controllers
- Validated replication and domain health
- Structured OU hierarchy
- Department-based access groups
- Group Policy security baselines
- Password and account lockout controls
- Fine-grained password policies
- Delegated administration
- Windows LAPS-managed local administrator controls
- Active Directory Certificate Services
- Identity lifecycle automation
- System State backup and recovery artifacts
- IAM audit exports and security review summary
- SOP, runbook, and operational handoff documentation
- Final enterprise IAM capstone validation

The completed foundation series shows a practical enterprise-style IAM environment built, secured, automated, backed up, audited, documented, and validated.

---

## Expansion Series Completion

Labs 25–30 extended the environment beyond foundational Active Directory administration into operational IAM governance.

The expansion series validated:

- Service account inventory and documentation
- Service account ownership and review frequency
- Least-privilege scheduled task execution
- Endpoint encryption with BitLocker
- Recovery workflow awareness
- Local administrator exposure review
- Local administrator remediation
- Splunk Enterprise installation
- Windows Security Event Log ingestion
- Successful and failed logon monitoring
- Local group membership change review
- Final IAM operations and governance review

The expansion series shows how identity controls are reviewed, monitored, and maintained after implementation.

---

## Security and IAM Themes

| Theme | Description |
|---|---|
| Least Privilege | Access is scoped through groups, delegation, service account review, and local admin remediation |
| Identity Governance | Users, groups, service accounts, and privileged access are reviewed and documented |
| Endpoint Security | Workstation protections include policy enforcement, BitLocker, and local admin review |
| Operational Resilience | Backup, recovery, replication, and checkpoints support rollback and continuity |
| Monitoring | Windows logs, event forwarding, and Splunk searches support identity visibility |
| Audit Readiness | Evidence is collected through screenshots, exports, validation steps, and README documentation |
| Automation | PowerShell and scheduled tasks support repeatable identity operations |
| Handoff Readiness | SOPs, runbooks, and capstones document how the environment is operated and reviewed |

---

## Skills Demonstrated

- Hyper-V lab design
- Windows Server administration
- Active Directory Domain Services deployment
- Domain controller promotion
- DNS and DHCP support services
- OU design
- Group Policy management
- Security group-based access control
- NTFS and share permission management
- Delegation of control
- Windows LAPS
- Active Directory Certificate Services
- PowerShell identity automation
- Backup and recovery validation
- IAM audit review
- Service account governance
- Scheduled task configuration
- BitLocker endpoint encryption
- Local administrator review and remediation
- Splunk Enterprise installation
- SIEM event search and validation
- Windows Security Event ID review
- Operational documentation
- Capstone validation

---

## Final Outcome

This project demonstrates a full IAM lab journey from foundational identity infrastructure to operational governance.

The environment was built, secured, validated, expanded, monitored, and documented across 30 labs.

The final series outcome demonstrates:

- Active Directory infrastructure deployment
- Centralized identity management
- Role-based access control
- Policy-based security enforcement
- Delegated administration
- Identity lifecycle automation
- Backup and recovery readiness
- IAM audit evidence
- Service account governance
- Endpoint encryption validation
- Privileged access remediation
- SIEM identity monitoring
- Governance review and operational handoff

This repository represents a practical IAM portfolio project focused on real-world skill transfer, audit-ready documentation, and government-aligned identity operations.

---

## Next Phase

The next phase builds from this on-premises IAM foundation into cloud identity and Azure fundamentals.

Planned direction:

- AZ-900 study and lab series
- Azure identity and access fundamentals
- Entra ID concepts
- Hybrid identity bridge from Active Directory to cloud identity
- Cloud governance and security foundations
