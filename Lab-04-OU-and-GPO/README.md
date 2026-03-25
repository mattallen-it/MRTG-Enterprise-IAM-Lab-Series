# Lab 04 — Organizational Unit (OU) Design and Group Policy

---

## Overview

In this lab, I implemented Group Policy Objects (GPOs) within the Monroe Redstone Technology Group (MRTG) environment to enforce centralized security configurations across domain-joined systems.

This lab demonstrates how identity-driven policy enforcement is used to control user and device behavior, a critical component of Identity and Access Management (IAM) in enterprise and government environments.

The approach aligns with enterprise security frameworks by ensuring consistent, auditable control over user sessions and endpoint configurations.

---

## Objective

- Deploy and configure Group Policy Objects (GPOs)
- Enforce security controls on domain-joined endpoints
- Validate policy application using `gpresult`
- Understand how Group Policy supports IAM governance and compliance

---

## Environment

| Component         | Details                              |
|------------------|--------------------------------------|
| Organization     | Monroe Redstone Technology Group     |
| Domain           | mrtg.local                           |
| Domain Controller| Windows Server 2019 (DC01)           |
| Client Machine   | Windows 11 (CLIENT01)                |
| Virtualization   | Hyper-V                              |

---

## Architecture

### Core Systems
- **DC01** — Domain Controller (AD DS, DNS, Group Policy)
- **CLIENT01** — Domain-joined workstation

### Organizational Unit Structure
- Users
- Groups
- Computers
- Admin Accounts
- Service Accounts

### Departmental Segmentation
- IT
- Security
- HR
- Finance
- Operations
- Engineering
- Executives

---

## Lab Steps and Evidence

### 1. Created Top-Level OU Structure
A structured OU hierarchy was created to separate users, groups, computers, and privileged accounts, aligning with enterprise identity management practices.

![Top Level OU](images/mrtg_ou_top_level_structure.png)

---

### 2. Implemented Departmental OU Segmentation
Department-based OUs were created to logically group users and enable targeted policy application.

![Departmental OU](images/departmental_ou_structure.png)

---

### 3. Configured Computer OU Segmentation
Workstations and servers were separated into dedicated OUs to support device-based policy enforcement.

![Computers OU](images/computers_ou_segmentation.png)

---

### 4. Verified Client Placement in Workstations OU
The domain-joined client (CLIENT01) was placed into the Workstations OU to ensure correct GPO targeting.

![Client OU Placement](images/client01_in_workstations_ou.png)

---

### 5. Created Department-Based Security Groups
Security groups were created for each department to support role-based access control (RBAC).

![Security Groups](images/security_groups_departmental.png)

---

### 6. Implemented Admin Account Separation
Dedicated administrative accounts were created to enforce privilege separation and reduce risk.

![Admin Accounts](images/admin_accounts_separation.png)

---

### 7. Configured Service Accounts
Service accounts were created for application and operational use, following enterprise identity practices.

![Service Accounts](images/service_accounts_configured.png)

---

### 8. Created and Linked Workstation GPO
A baseline GPO was created and linked to the Workstations OU to enforce standardized security configurations.

![GPO Link](images/gpo_linked_to_workstations_ou.png)

---

### 9. Configured Password Policy
Password complexity, length, and history settings were configured to align with security standards.

![Password Policy](images/gpo_password_policy_configured.png)

---

### 10. Configured Account Lockout Policy
Account lockout thresholds were implemented to protect against brute-force attacks.

![Lockout Policy](images/gpo_account_lockout_policy_configured.png)

---

### 11. Verified GPO Scope and Security Filtering
GPO scope and filtering were validated to ensure correct targeting of users and devices.

![GPO Scope](images/gpo_scope_and_security_filtering.png)

---

### 12. Forced Group Policy Update and Verified Computer Policy
Group Policy was updated and validated using gpresult to confirm computer-level policy application.

![GPResult Computer](images/gpresult_gpo_applied.png)

---

### 13. Verified User Policy Application
User-level policies were validated to confirm identity-based enforcement.

![GPResult User](images/gpresult_user_policy_applied.png)

---

### 14. Simulated Access Denied Scenario (RDP)
A failed remote login attempt demonstrated lack of proper group membership.

![Access Denied](images/rdp_access_denied_user_not_authorized.png)

---

### 15. Implemented Group-Based Access Control
User access was granted by adding the user to the Remote Desktop Users security group.

![Group Membership](images/remote_desktop_users_group_membership.png)

---

### 16. Applied Policy Update and Remediation
Group membership changes were applied and policies refreshed to enforce access.

![Remediation](images/rdp_group_assignment_and_gpupdate.png)

---

### 17. Final Policy Validation
Final validation confirmed that both computer and user policies were successfully applied.

![Final Validation](images/gpresult_final_validation.png)
---

## Outcome

- Successfully deployed and linked Group Policy Objects
- Enforced security configurations on domain-joined systems
- Implemented identity-based access control using security groups
- Validated policy enforcement using `gpresult`
- Demonstrated troubleshooting and remediation of access issues

---

## Next Steps

The next phase will extend identity management into hybrid environments by integrating Microsoft Entra ID and cloud-based identity controls.
