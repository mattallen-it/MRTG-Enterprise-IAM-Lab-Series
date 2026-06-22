# Monroe Redstone Technology Group IAM Lab Series

![IAM](https://img.shields.io/badge/IAM-Enterprise-blue)
![Active Directory](https://img.shields.io/badge/Directory-Active_Directory-2A628C)
![Security](https://img.shields.io/badge/Security-Policy_%26_Access_Control-red)
![Platform](https://img.shields.io/badge/Platform-Windows_Enterprise-lightgrey)
![Focus](https://img.shields.io/badge/Focus-Identity_Governance-purple)
![Status](https://img.shields.io/badge/Status-Complete-brightgreen)

---

## Series Overview

This repository documents a complete 30-lab Identity and Access Management environment created for the fictional **Monroe Redstone Technology Group (MRTG)**.

The series begins with the deployment of an on-premises Active Directory environment and progresses through identity lifecycle management, access control, administrative delegation, automation, recovery, security monitoring, and operational governance.

The project emphasizes practical IAM skills relevant to enterprise and government-regulated IT environments, including least privilege, centralized access control, auditability, recovery readiness, and repeatable operations.

> This project was completed in a controlled lab environment and represents hands-on technical training rather than production administration experience.

---

## Series Status

| Item | Status |
|---|---|
| Foundation Series | Labs 1-24 complete |
| Governance Expansion | Labs 25-30 complete |
| Total Labs | 30 |
| Overall Status | Complete |

---

## Key Outcomes

Across the complete series, I:

- Built and validated an Active Directory Domain Services environment
- Designed an Organizational Unit structure for users, computers, groups, and administrative scope
- Implemented group-based access control for departmental resources
- Applied Group Policy security controls to workstations and servers
- Configured password, account lockout, and fine-grained password policies
- Practiced Joiner, Mover, and Leaver identity lifecycle processes
- Delegated administrative responsibilities using least privilege
- Deployed an additional domain controller and validated replication
- Implemented Windows LAPS for local administrator password protection
- Deployed Active Directory Certificate Services
- Automated identity lifecycle tasks with PowerShell
- Validated backup, recovery, and operational resilience procedures
- Created IAM audit evidence, SOPs, runbooks, and handoff documentation
- Reviewed service accounts as non-human identities
- Configured a scheduled task using a least-privilege service account
- Enabled BitLocker and documented recovery procedures
- Reviewed and remediated local administrator access
- Collected and searched Windows identity events with Splunk Enterprise
- Completed final IAM operations, monitoring, and governance validation

---

## Lab Environment

### Domain

| Item | Value |
|---|---|
| Organization | Monroe Redstone Technology Group |
| Domain | `mrtg.local` |
| Directory Service | Active Directory Domain Services |
| Primary Authentication | Kerberos |
| Hypervisor | Hyper-V on Windows 11 Pro |

### Systems

| System | Operating System | Purpose |
|---|---|---|
| `MRTG-DC01` | Windows Server 2022 | Primary domain controller, DNS, Group Policy, Global Catalog, and FSMO role holder |
| `MRTG-DC02` | Windows Server 2022 | Additional domain controller, DNS, Global Catalog, replication, and directory resilience |
| `MRTG-LOG01` | Windows Server 2022 | Centralized logging and security monitoring with Splunk Enterprise |
| `MRTG-CLIENT-01` | Windows 11 Enterprise | Domain-joined workstation for authentication, policy, access, BitLocker, and local administrator validation |

### Core Technologies

- Active Directory Domain Services
- DNS and DHCP
- Group Policy
- Kerberos authentication
- NTFS and share permissions
- Windows LAPS
- Active Directory Certificate Services
- Windows Event Forwarding
- PowerShell
- BitLocker
- Splunk Enterprise
- Hyper-V

---

## Identity Architecture

Authentication and authorization are centralized through Active Directory Domain Services.

Access and administrative scope are controlled through:

- Organizational Units for policy application and delegation
- Security groups for role-based and department-based access
- Group Policy Objects for centralized configuration enforcement
- Delegation of Control for least-privilege administration
- Service account governance for non-human identities
- Windows LAPS and local administrator remediation for endpoint privilege reduction
- Windows event collection and Splunk searches for identity monitoring

The architecture supports centralized governance, policy-driven enforcement, auditability, lifecycle automation, recovery readiness, and operational review.

---

## Lab Series

| Lab | Topic | Primary Focus |
|---|---|---|
| Lab 01 | Virtualization and Identity Infrastructure Foundation | Environment buildout |
| Lab 02 | AD DS Deployment Preparation | Identity platform preparation |
| Lab 03 | Domain Controller Promotion | Identity activation |
| Lab 04 | OU Design and GPO Enforcement | Policy and access control |
| Lab 05 | Identity Lifecycle Management | Joiner, Mover, and Leaver processes |
| Lab 06 | NTFS and Share Permissions | Resource access control |
| Lab 07 | Service Accounts and Delegation | Privileged identity management |
| Lab 08 | Identity Monitoring and Auditing | Security and compliance |
| Lab 09 | Password Policy and Account Lockout Hardening | Authentication hardening |
| Lab 10 | Fine-Grained Password Policies for Tiered Identity Control | Tiered authentication control |
| Lab 11 | DHCP Services for Enterprise Identity Infrastructure | Identity-supporting network services |
| Lab 12 | Additional Domain Controller and AD Replication | Directory resilience |
| Lab 13 | Centralized Logging and Event Forwarding for Identity Events | Visibility and audit collection |
| Lab 14 | Active Directory Sites and Services for Replication Topology | Replication topology |
| Lab 15 | Group Policy Security Baselines for Workstations and Servers | Endpoint security control |
| Lab 16 | Delegation of Control and Tiered Administrative Boundaries | Least-privilege administration |
| Lab 17 | Windows LAPS and Local Administrator Control | Privileged endpoint protection |
| Lab 18 | Group-Based Access Control for File and Department Resources | Authorization design |
| Lab 19 | Active Directory Certificate Services | Enterprise trust services |
| Lab 20 | Identity Lifecycle Automation with PowerShell | Identity automation |
| Lab 21 | Directory Recovery, Backup, and Operational Resilience | Identity recovery and continuity |
| Lab 22 | IAM Security Review and Access Control Audit | Identity risk and access review |
| Lab 23 | IAM Runbooks, SOPs, and Operational Handoff | Operational documentation |
| Lab 24 | Enterprise IAM Capstone Validation | End-to-end IAM validation |
| Lab 25 | Service Account Governance Foundation | Non-human identity governance |
| Lab 26 | Scheduled Task with Least-Privilege Service Account | Least-privilege automation |
| Lab 27 | BitLocker and Endpoint Encryption Recovery | Endpoint encryption and recovery |
| Lab 28 | Local Administrator Access Review and Remediation | Privileged access cleanup |
| Lab 29 | SIEM Identity Monitoring with Splunk | Identity event monitoring |
| Lab 30 | IAM Operations, Monitoring, and Governance Capstone | Operational IAM governance |

---

## Quick Access

- [Lab 01: Virtualization and Identity Infrastructure Foundation](./Lab-01-Virtualization-and-Identity-Infrastructure-Foundation/)
- [Lab 02: AD DS Deployment Preparation](./Lab-02-AD-DS-Deployment-Preparation/)
- [Lab 03: Domain Controller Promotion](./Lab-03-Domain-Controller-Promotion/)
- [Lab 04: OU Design and GPO Enforcement](./Lab-04-OU-Design-and-GPO-Enforcement/)
- [Lab 05: Identity Lifecycle Management](./Lab-05-Identity-Lifecycle-Management/)
- [Lab 06: NTFS and Share Permissions](./Lab-06-NTFS-and-Share-Permissions/)
- [Lab 07: Service Accounts and Delegation](./Lab-07-Service-Accounts-and-Delegation/)
- [Lab 08: Identity Monitoring and Auditing](./Lab-08-Identity-Monitoring-and-Auditing/)
- [Lab 09: Password Policy and Account Lockout Hardening](./Lab-09-Password-Policy-and-Account-Lockout-Hardening/)
- [Lab 10: Fine-Grained Password Policies for Tiered Identity Control](./Lab-10-Fine-Grained-Password-Policies-for-Tiered-Identity-Control/)
- [Lab 11: DHCP Services for Enterprise Identity Infrastructure](./Lab-11-DHCP-Services-for-Enterprise-Identity-Infrastructure/)
- [Lab 12: Additional Domain Controller and AD Replication](./Lab-12-Additional-Domain-Controller-and-AD-Replication/)
- [Lab 13: Centralized Logging and Event Forwarding for Identity Events](./Lab-13-Centralized-Logging-and-Event-Forwarding-for-Identity-Events/)
- [Lab 14: Active Directory Sites and Services for Replication Topology](./Lab-14-Active-Directory-Sites-and-Services-for-Replication-Topology/)
- [Lab 15: Group Policy Security Baselines for Workstations and Servers](./Lab-15-Group-Policy-Security-Baselines-for-Workstations-and-Servers/)
- [Lab 16: Delegation of Control and Tiered Administrative Boundaries](./Lab-16-Delegation-of-Control-and-Tiered-Administrative-Boundaries/)
- [Lab 17: Windows LAPS and Local Administrator Control](./Lab-17-Windows-LAPS-and-Local-Administrator-Control/)
- [Lab 18: Group-Based Access Control for File and Department Resources](./Lab-18-Group-Based-Access-Control-for-File-and-Department-Resources/)
- [Lab 19: Active Directory Certificate Services](./Lab-19-Active-Directory-Certificate-Services/)
- [Lab 20: Identity Lifecycle Automation with PowerShell](./Lab-20-Identity-Lifecycle-Automation-with-PowerShell/)
- [Lab 21: Directory Recovery, Backup, and Operational Resilience](./Lab-21-Directory-Recovery-Backup-and-Operational-Resilience/)
- [Lab 22: IAM Security Review and Access Control Audit](./Lab-22-IAM-Security-Review-and-Access-Control-Audit/)
- [Lab 23: IAM Runbooks, SOPs, and Operational Handoff](./Lab-23-IAM-Runbooks-SOPs-Operational-Handoff/)
- [Lab 24: Enterprise IAM Capstone Validation](./Lab-24-Enterprise-IAM-Capstone-Validation/)
- [Lab 25: Service Account Governance Foundation](./Lab-25-Service-Account-Governance-Foundation/)
- [Lab 26: Scheduled Task with Least-Privilege Service Account](./Lab-26-Scheduled-Task-with-Least-Privilege-Service-Account/)
- [Lab 27: BitLocker and Endpoint Encryption Recovery](./Lab-27-BitLocker-and-Endpoint-Encryption-Recovery/)
- [Lab 28: Local Administrator Access Review and Remediation](./Lab-28-Local-Administrator-Access-Review-and-Remediation/)
- [Lab 29: SIEM Identity Monitoring with Splunk](./Lab-29-SIEM-Identity-Monitoring-with-Splunk/)
- [Lab 30: IAM Operations, Monitoring, and Governance Capstone](./Lab-30-IAM-Operations-Monitoring-and-Governance-Capstone/)

---

## Security and IAM Themes

| Theme | Application |
|---|---|
| Least Privilege | Group-based access, delegated administration, service account controls, and local administrator remediation |
| Identity Governance | User, group, service account, and privileged access review |
| Lifecycle Management | Joiner, Mover, and Leaver processes supported by repeatable PowerShell workflows |
| Authentication Security | Password policy, account lockout controls, fine-grained password policies, Kerberos, and certificates |
| Endpoint Security | Group Policy baselines, Windows LAPS, BitLocker, and local administrator review |
| Monitoring | Windows event collection and identity-focused searches in Splunk Enterprise |
| Operational Resilience | Domain controller replication, System State backup, and documented recovery procedures |
| Audit Readiness | Screenshots, command output, validation results, exports, and written documentation |
| Operational Handoff | SOPs, runbooks, governance records, and capstone validation |

---

## Skills Demonstrated

- Hyper-V lab design and virtual machine management
- Windows Server and Windows 11 administration
- Active Directory Domain Services deployment and administration
- DNS, DHCP, Global Catalog, and AD replication validation
- Organizational Unit and Group Policy design
- Role-based access control using security groups
- NTFS and share permission administration
- Delegation of Control and administrative boundary design
- Windows LAPS implementation
- Active Directory Certificate Services deployment
- PowerShell identity lifecycle automation
- Active Directory backup and recovery validation
- IAM security review and audit evidence collection
- Service account governance and least-privilege task execution
- BitLocker encryption and recovery validation
- Local administrator access review and remediation
- Windows Security Event Log analysis
- Splunk Enterprise installation, ingestion, and searching
- SOP, runbook, governance, and handoff documentation

---

## Final Outcome

This project demonstrates the progression of an IAM environment from initial infrastructure deployment through operational governance.

The completed environment includes centralized identity management, group-based authorization, policy enforcement, delegated administration, lifecycle automation, recovery procedures, privileged access controls, security monitoring, audit evidence, and operational documentation.

The repository serves as a practical portfolio of hands-on IAM work and documents both the technical implementation and the operational reasoning behind each control.

---

## Next Phase

This completed on-premises IAM environment provides the foundation for the next stage of the MRTG learning environment:

1. Complete the AZ-900 study plan and **The Bridge** Azure fundamentals lab series
2. Take the Microsoft Azure Fundamentals certification exam
3. Continue into SC-900 security, compliance, and identity fundamentals
4. Extend MRTG into Microsoft Entra ID and hybrid identity
5. Prepare for SC-300 identity and access administration
6. Add deeper Azure administration skills when required by career goals
