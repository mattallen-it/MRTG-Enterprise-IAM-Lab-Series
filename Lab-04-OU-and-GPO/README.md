# Lab 04 — OU Design and Group Policy Implementation

---

### Overview

In this lab, I implemented Group Policy Objects (GPOs) within the Monroe Redstone Technology Group (MRTG) environment to enforce centralized security configurations across domain-joined systems.

This lab demonstrates how identity-driven policy enforcement is used to control user and device behavior, a critical component of Identity and Access Management (IAM) in enterprise and government environments.

This approach aligns with enterprise security frameworks by ensuring consistent, auditable control over user sessions and endpoint configurations.

This implementation ensures centralized, identity-driven enforcement of security policies across users and endpoints.

---

## Objective

- Deploy and configure Group Policy Objects (GPOs)
- Enforce security controls on domain-joined endpoints
- Validate policy application using gpresult
- Understand how Group Policy supports IAM governance and compliance

---
##  Environment

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
- DC01 – Domain Controller (Active Directory, DNS, Group Policy Management)
- CLIENT01 – Domain-joined workstation

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

##  Group Policy Configuration

A baseline workstation security policy was created and linked to the Workstations Organizational Unit to enforce standardized configurations across domain-joined devices.

### GPO Linked to Workstations OU

![GPO Link](images/gpo_linked_to_workstations_ou.png)

### Password Policy Configuration

![Password Policy](images/gpo_password_policy_configured.png)

### Account Lockout Policy Configuration

![Lockout Policy](images/gpo_account_lockout_policy_configured.png)

### GPO Scope and Security Filtering

![Scope](images/gpo_scope_and_security_filtering.png)

##  Policy Enforcement & Validation

To confirm that policies were successfully applied, Group Policy updates were forced and validated using command-line tools.

### Computer Policy Validation

![GPResult Computer](images/gpresult_gpo_applied.png)

### User Policy Validation

![GPResult User](images/gpresult_user_policy_applied.png)

## Access Control Scenario

An access issue was simulated and resolved to demonstrate identity-based access control and troubleshooting.

### Initial Access Denied (RDP)

![Access Denied](images/rdp_access_denied_user_not_authorized.png)

### Group-Based Access Assignment

![Group Membership](images/remote_desktop_users_group_membership.png)

### Policy Update and Remediation

![GPUpdate](images/rdp_group_assignment_and_gpupdate.png)

### Final Validation

![Final Validation](images/gpresult_final_validation.png)

## IAM & Security Analysis

This lab demonstrates how Group Policy functions as a centralized enforcement mechanism within an identity-driven environment.

### Key Concepts

- Centralized Access Control  
- Least Privilege Enforcement  
- Policy-Based Governance  
- Audit and Validation  

### Real-World Relevance
Group Policy is used in enterprise and government environments to enforce security baselines, manage endpoint configurations, and control user behavior in alignment with Zero Trust principles.

##  Outcome

- Successfully deployed and linked Group Policy Objects  
- Enforced security configurations on domain-joined systems  
- Implemented identity-based access control using security groups  
- Validated policy enforcement using gpresult  
- Demonstrated troubleshooting and remediation of access issues

## Next Steps

The next phase will extend identity management into the cloud by introducing Microsoft Entra ID and hybrid identity concepts.

Group Policy is used in enterprise and government environments to enforce security baselines, manage endpoint configurations, and control user behavior in alignment with Zero Trust principles.
