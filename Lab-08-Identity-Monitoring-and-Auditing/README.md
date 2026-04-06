# Lab-08: Identity Monitoring and Auditing

![Platform](https://img.shields.io/badge/Platform-Windows%20Server%202022-blue)
![Directory](https://img.shields.io/badge/Directory-Active%20Directory-darkgreen)
![Focus](https://img.shields.io/badge/Focus-Identity%20Monitoring%20%26%20Auditing-purple)
![Status](https://img.shields.io/badge/Status-Completed-brightgreen)

## Overview

In this lab, I implemented identity-focused auditing in the **Monroe Redstone Technology Group (MRTG)** Active Directory environment. The objective was to improve visibility into administrative identity activity by configuring domain controller audit policy, applying object-level auditing to the `_MRTG/Users` OU, and validating that delegated administrative actions generated the expected security events.

This lab builds directly on the delegated administration model established in **Lab 07**. Instead of relying on broad administrative access for user management tasks, I used the delegated admin account **`john.smith.admin`** to perform controlled identity actions and then verified those actions through the domain controller’s **Security** log.

---

## Objectives

- Create a dedicated auditing GPO for domain controllers
- Configure advanced audit policy for:
  - Logon events
  - User account management
  - Directory service changes
  - Security group management
- Verify policy application with `gpupdate` and `auditpol`
- Apply object-level auditing to the `_MRTG/Users` OU
- Generate auditable delegated identity actions
- Validate resulting events in **Event Viewer**

---

## Lab Scope

### Included
- Domain controller advanced audit policy configuration
- Object-level auditing on the `_MRTG/Users` OU
- Delegated password reset activity
- Delegated user attribute modification
- Security log validation in Event Viewer

### Not Included
- SIEM integration
- Windows Event Forwarding
- Microsoft Defender for Identity
- Centralized alerting workflows
- Long-term retention architecture

This lab stays focused on **native Windows and Active Directory auditing**.

---

## Environment / Lab Setup

### Systems
- **Domain Controller:** `MRTG-DC01`
- **Domain:** `mrtg.local`

### Active Directory Structure
- **Parent OU:** `_MRTG`
- **User OU:** `_MRTG/Users`
- **Admin Accounts OU:** `_MRTG/Admin Accounts`

### Key Accounts and Groups
- **Delegated admin account:** `john.smith.admin`
- **Delegated admin group:** `GG_IT_HelpDesk_Admins`
- **Validation target user:** `kevin.carter`

### GPOs Used
- **Auditing GPO:** `MRTG-DC-Identity-Auditing`
- **Temporary sign-in prep GPO:** `MRTG-DC-Logon-Validation`

### Assumptions
- The delegated administration model from **Lab 07** was already in place
- `john.smith.admin` had validated delegated access to manage users in `_MRTG/Users`
- The environment was running in Hyper-V with checkpoint support

---

## Architecture

This lab used a single-domain Active Directory environment with:

- audit policy configured at the **Domain Controllers** OU level
- object-level auditing applied to the `_MRTG/Users` OU
- delegated user-management activity performed by **`john.smith.admin`**
- event validation performed on **MRTG-DC01**

### High-Level Flow

1. Create and link a dedicated domain controller auditing GPO
2. Configure identity-related advanced audit policy settings
3. Apply object-level auditing to `_MRTG/Users`
4. Perform delegated identity actions
5. Validate resulting security events in Event Viewer

---

## Implementation Steps

### 1. Created a Pre-Lab Checkpoint

Before making changes, I created a Hyper-V checkpoint to preserve the clean post-Lab 07 environment.

**Screenshot:**  
`Lab-08-01-Pre-Auditing-Checkpoint.png`

---

### 2. Confirmed Delegated Administration Baseline

I verified that **`john.smith.admin`** remained a member of **`GG_IT_HelpDesk_Admins`** and confirmed the `_MRTG/Users` OU structure was present. This ensured the lab started from the intended delegated-access baseline rather than broad administrative access.

**Screenshot:**  
`Lab-08-02-Delegated-Admin-Baseline.png`

---

### 3. Created and Linked the Auditing GPO

I created a dedicated GPO named **`MRTG-DC-Identity-Auditing`** and linked it to the **Domain Controllers** OU. I used a dedicated GPO rather than modifying the default policy so the change would remain easier to manage, explain, and validate.

**Screenshot:**  
`Lab-08-03-Auditing-GPO-Created.png`

---

### 4. Configured Audit Logon

I enabled **Audit Logon** for both **Success** and **Failure** events. This provides visibility into successful and failed authentication activity on the domain controller.

**Screenshot:**  
`Lab-08-04-Audit-Logon-Configured.png`

---

### 5. Configured Audit User Account Management

I enabled **Audit User Account Management** for both **Success** and **Failure** events. This supports monitoring of actions such as password resets and account changes.

**Screenshot:**  
`Lab-08-05-Audit-User-Account-Management-Configured.png`

---

### 6. Configured Audit Directory Service Changes

I enabled **Audit Directory Service Changes** for **Success** events. This supports tracking of Active Directory object modifications when paired with the appropriate object-level auditing entry.

**Screenshot:**  
`Lab-08-06-Audit-Directory-Service-Changes-Configured.png`

---

### 7. Configured Audit Security Group Management

I enabled **Audit Security Group Management** for both **Success** and **Failure** events to improve visibility into changes affecting access-control groups.

**Screenshot:**  
`Lab-08-07-Audit-Security-Group-Management-Configured.png`

---

### 8. Applied and Verified Audit Policy

I applied the updated policy and verified the resulting configuration with:

```cmd
gpupdate /force
auditpol /get /category:*
