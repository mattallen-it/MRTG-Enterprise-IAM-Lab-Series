# Lab-07 — Service Accounts and Delegation

![Windows Server](https://img.shields.io/badge/Windows%20Server-Privileged%20Administration-blue)
![Hyper-V](https://img.shields.io/badge/Hyper--V-Lab%20Environment-5C2D91)
![AD DS](https://img.shields.io/badge/AD%20DS-Delegated%20Administration-0A66C2)
![IAM](https://img.shields.io/badge/IAM-Privileged%20Identity%20Management-darkgreen)
![Least Privilege](https://img.shields.io/badge/Security-Least%20Privilege-darkred)

## Overview
In this lab, I implemented foundational privileged identity management controls within the Monroe Redstone Technology Group (MRTG) Active Directory environment by separating human administrative identities from service accounts and delegating limited user-management rights through a dedicated support group. Building on the identity lifecycle and access control work from earlier labs, this lab demonstrates how routine administrative tasks can be scoped to delegated identities without granting broad domain-wide administrative privilege.

## Objectives
- Separate named administrative identities from standard user accounts
- Maintain dedicated service accounts in a separate organizational unit
- Create a delegated administration group for limited support tasks
- Assign a named administrative identity to the delegated support group
- Delegate password reset rights over the `_MRTG/Users` OU
- Validate that delegated scope is narrower than full Domain Admin privilege
- Reinforce least privilege through role-based privileged identity design

## Scope
This lab focuses on privileged identity separation and delegated administration in an on-premises Active Directory environment. It does not include Group Managed Service Accounts (gMSAs), Privileged Access Management platforms, tiered admin workstations, or deep Kerberos/SPN implementation.

## Lab Environment

| Component | Details |
|---|---|
| Organization | Monroe Redstone Technology Group (MRTG) |
| Domain | mrtg.local |
| Domain Controller | MRTG-DC01 |
| Platform | Hyper-V |
| Core Services | Active Directory Domain Services, DNS, Group Policy |
| Privileged OUs | `_MRTG/Admin Accounts`, `_MRTG/Service Accounts` |
| Delegation Target | `_MRTG/Users` |

## Architecture / Design
The MRTG environment separates privileged identities by function. Human administrative accounts are stored in a dedicated **Admin Accounts** OU, while non-human operational identities are stored in a dedicated **Service Accounts** OU. A separate delegated administration group, `GG_IT_HelpDesk_Admins`, is used to assign limited support rights over the `_MRTG/Users` OU. This design reduces reliance on broad administrative groups and reinforces least privilege by scoping administrative actions to only the tasks required.

## Technologies Used
- Active Directory Users and Computers
- Active Directory Domain Services (AD DS)
- Windows Server
- Group Delegation in ADUC
- Hyper-V

## Privileged Identity Model
This lab uses three distinct privileged identity layers:

- **Named human admin accounts** for identifiable administrative actions
- **Dedicated service accounts** for non-human operational functions
- **Delegated admin groups** for scoped support responsibilities

### Administrative Accounts
- `alex.rivera.admin`
- `john.smith.admin`

### Service Accounts
- `Service App Deploy`
- `Service Backup`

### Delegated Support Group
- `GG_IT_HelpDesk_Admins`

## Implementation Steps

### Step 1 - Review Named Administrative Accounts
Reviewed the dedicated **Admin Accounts** OU and confirmed that named privileged identities were separated from standard user accounts. This supports accountability by tying administrative actions to specific named identities rather than shared or generic admin accounts.

![Step 1](images/lab07-01.png)

### Step 2 - Review Dedicated Service Accounts
Reviewed the dedicated **Service Accounts** OU and confirmed that non-human identities were stored separately from both standard users and human administrative accounts. This supports cleaner privileged identity management by separating operational service identities from interactive administrative identities.

![Step 2](images/lab07-02.png)

### Step 3 - Create the Delegated Administration Group
Created the `GG_IT_HelpDesk_Admins` security group in the **Groups** OU to represent a scoped support role for delegated administrative tasks.

![Step 3](images/lab07-03.png)

### Step 4 - Assign a Named Administrative Identity to the Delegated Group
Added `john.smith.admin` to `GG_IT_HelpDesk_Admins` to bind the delegated support role to a specific named administrative identity. This maintained accountability while avoiding the use of a broader domain-wide administrative role.

![Step 4](images/lab07-04.png)

### Step 5 - Delegate Password Reset Rights Over the Users OU
Used the Delegation of Control Wizard on `_MRTG/Users` to grant `GG_IT_HelpDesk_Admins` the ability to **reset user passwords and force password change at next logon**. This provided a realistic Help Desk-style delegated task without assigning unrestricted administrative control.

![Step 5](images/lab07-05.png)

### Step 6 - Validate Scoped Privileged Group Membership
Reviewed `john.smith.admin` group membership and confirmed that the account was a member of `GG_IT_HelpDesk_Admins` while not being a member of `Domain Admins`. This validated that the delegated account remained scoped to a limited administrative role rather than inheriting broad domain-wide privilege.

![Step 6](images/lab07-06.png)

## Validation / Verification
- Confirmed named administrative accounts were separated into the `_MRTG/Admin Accounts` OU
- Confirmed service accounts were separated into the `_MRTG/Service Accounts` OU
- Verified `GG_IT_HelpDesk_Admins` existed as a delegated support group
- Verified `john.smith.admin` was assigned to `GG_IT_HelpDesk_Admins`
- Verified password reset rights were delegated over `_MRTG/Users`
- Confirmed `john.smith.admin` was not a member of `Domain Admins`
- Confirmed delegated scope was narrower than broad domain-wide administrative privilege

## Outcome
This lab established a foundational privileged identity model inside the MRTG Active Directory environment by separating human administrative accounts, service accounts, and delegated support roles. It reinforced the principle that routine administrative tasks should be assigned through scoped delegation rather than broad domain-wide privilege. This lab also highlighted that delegated Active Directory rights and interactive logon rights are separate control layers, reinforcing the need to manage privileged access boundaries carefully in enterprise environments.

## Next Lab
➡️ [Lab-08 - Identity Monitoring and Auditing](../Lab-08-Identity-Monitoring-and-Auditing/README.md)
