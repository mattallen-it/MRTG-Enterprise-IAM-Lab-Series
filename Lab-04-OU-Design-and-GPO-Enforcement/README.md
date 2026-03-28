Lab-04 — OU Design and GPO Enforcement (Access Control)
---

![IAM](https://img.shields.io/badge/IAM-Active%20Directory-blue)
![GPO](https://img.shields.io/badge/Policy-Group%20Policy%20Objects-green)
![Access Control](https://img.shields.io/badge/Security-Access%20Control-red)
![Windows Server](https://img.shields.io/badge/Platform-Windows%20Server%202022-lightgrey)
![RBAC](https://img.shields.io/badge/Model-RBAC-purple)

## Overview

This lab focuses on implementing policy-based identity control within the MRTG Active Directory environment using Organizational Units (OUs) and Group Policy Objects (GPOs).

This phase introduces centralized policy enforcement, enabling standardized configuration, security controls, and scalable identity management across the domain.

This lab demonstrates how identity transitions from static objects to controlled and governed entities.

---

## Why This Matters

In enterprise and government environments, identity must be controlled through policy.

Group Policy enables:

- Centralized configuration management
- Enforcement of security baselines
- Identity-based targeting through OU structure
- Scalable and repeatable access control

Without policy enforcement, identity systems become inconsistent and insecure.

---

## Environment

| Component           | Value              |
|--------------------|-------------------|
| Domain Name        | mrtg.local        |
| Domain Controller  | MRTG-DC01         |
| Tools Used         | Group Policy Management Console (GPMC) |
| Platform           | Windows Server 2022 |

---

## Architecture

### Identity Infrastructure

* **Domain:** mrtg.local
* **Domain Controller:** MRTG-DC01
* **Directory Services:** Active Directory Domain Services (AD DS)
* **Authentication Protocol:** Kerberos

Active Directory serves as the centralized identity provider, handling authentication and authorization across all domain-joined systems.

---

### Organizational Unit (OU) Design

The OU structure is aligned with business functions to enable targeted policy enforcement and administrative delegation.

```
mrtg.local
└── _MRTG
    ├── Users
    │   ├── IT
    │   ├── Security
    │   ├── HR
    │   ├── Finance
    │   ├── Operations
    │   ├── Engineering
    │   └── Executives
    ├── Computers
    │   ├── Workstations
    │   └── Servers
    ├── Groups
    ├── Admin Accounts
    └── Service Accounts
```

This structure enables:

* Policy targeting by role and device type
* Separation of administrative responsibilities
* Scalable identity management

---

### Group Policy Architecture

Group Policy Objects (GPOs) are used to enforce security configurations at scale.

* **Workstation Baseline GPO**

  * Enforces password policy
  * Enforces account lockout policy
  * Applies user session lock controls

* **Linking Strategy**

  * GPO linked to **Workstations OU**
  * Applies to domain-joined endpoints

* **Scope & Filtering**

  * Security filtering via **Authenticated Users**
  * Ensures correct policy targeting

---

### Access Control Model

Access is governed through **security group membership**, implementing Role-Based Access Control (RBAC).

Example:

* **GG_Remote_Desktop_Users**

  * Grants RDP access to authorized users
  * Assigned via group membership, not direct user permissions

This ensures:

* Centralized access management
* Reduced administrative overhead
* Consistent enforcement of least privilege

---

### Policy Enforcement Flow

1. User logs into domain-joined system
2. Authentication handled by Domain Controller (Kerberos)
3. Applicable GPOs are retrieved and applied
4. Access decisions enforced via:

   * OU placement
   * Group membership
   * GPO configuration

This model reflects real-world enterprise IAM environments where identity, policy, and access control are tightly integrated.

---

## Lab Steps and Evidence

### 1. Designed Organizational Unit (OU) Structure

A structured OU hierarchy was created to reflect enterprise business functions and enable policy targeting.

![OU Structure](images/step01_ou_structure.png)

---

### 2. Implemented Computer OU Segmentation

Dedicated OUs were created for Servers and Workstations to allow device-specific policy enforcement.

![Computer OU Structure](images/step02_computer_ou_structure.png)

---

### 3. Joined Client Machine to Domain and Placed in Workstations OU

CLIENT01 was domain-joined and moved into the Workstations OU for policy application.

![Workstation OU Membership](images/step03_workstation_ou_membership.png)

---

### 4. Configured Password Policy

A baseline password policy was implemented to enforce credential security:

- Password history enforced  
- Minimum length configured  
- Complexity requirements enabled  

![Password Policy](images/step04_password_policy.png)

---

### 5. Configured Account Lockout Policy

Account lockout settings were applied to protect against brute-force attacks:

- Lockout threshold defined  
- Lockout duration configured  
- Reset counter configured  

![Account Lockout Policy](images/step05_account_lockout_policy.png)

---

### 6. Configured User Session Lock Policy

A user-based GPO was created to enforce automatic session locking:

- Screen saver enabled  
- Password protection enforced  
- Idle timeout configured  

![User Session Lock](images/step06_user_session_lock.png)

---

### 7. Linked GPO to Workstations OU

The workstation baseline GPO was linked to the Workstations OU to enforce policy at the device level.

![GPO Linked](images/step07_gpo_linked_to_ou.png)

---

### 8. Configured GPO Scope and Security Filtering

Security filtering was configured to ensure policies applied only to intended users and systems.

![GPO Scope](images/step08_gpo_scope_filtering.png)

---

### 9. Validated Computer Policy Application

Group Policy results confirmed that workstation-level policies were successfully applied.

![Computer Policy Applied](images/step09_computer_policy_applied.png)

---

### 10. Validated User Policy Application

User-level policies were verified using gpresult to confirm correct GPO enforcement.

![User Policy Applied](images/step10_user_policy_applied.png)

---

### 11. Simulated Access Control Issue (RDP Denied)

An RDP login attempt failed due to missing group-based access permissions.

![RDP Denied](images/step11_rdp_access_denied.png)

---

### 12. Implemented Group-Based Access Control

User was added to the Remote Desktop Users group to grant appropriate access.

![Group Membership](images/step12_rdp_group_membership.png)

---

### 13. Validated Access Remediation

After group assignment and policy update, RDP access was successfully granted.

![RDP Success](images/step13_rdp_access_granted.png)
---

## Outcome

Policy-based identity control was successfully implemented.

- OUs structured for targeted policy application
- Security baseline policies enforced via GPO
- Policy application validated at both computer and user levels
- Access control enforced through group-based permissions

This environment now supports scalable, policy-driven identity and access management.

---

## IAM / Security Perspective

This lab demonstrates how identity is governed through policy enforcement.

Key concepts implemented:

- OU-based policy targeting
- Role-based access control (RBAC)
- Security baseline enforcement
- Validation through system tools (`gpresult`)
- Access control through group membership

This reflects real-world enterprise IAM practices, where identity, policy, and access are tightly integrated.

---

## Next Lab

[Lab-05 — Identity Lifecycle Management](../Lab-05-Identity-Lifecycle-Management)

The next lab will cover:

- User provisioning (Joiner process)
- Role changes (Mover process)
- Account deactivation (Leaver process)
- Identity lifecycle governance

---
