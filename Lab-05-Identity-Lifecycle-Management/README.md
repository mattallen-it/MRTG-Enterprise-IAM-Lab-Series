# Lab 05: Identity Lifecycle Management

![Platform](https://img.shields.io/badge/Platform-Windows%20Server%202022-blue)
![Technology](https://img.shields.io/badge/Technology-Active%20Directory-blue)
![Tooling](https://img.shields.io/badge/Tooling-ADUC-purple)
![Focus](https://img.shields.io/badge/Focus-Identity%20Lifecycle-orange)
![Access](https://img.shields.io/badge/Access-Group%20Based-brightgreen)
![Model](https://img.shields.io/badge/Model-JML-blue)

---

## Objective

Implement identity lifecycle management workflows in the `mrtg.local` Active Directory domain.

This lab applies Joiner, Mover, and Leaver concepts using department-aligned Organizational Units, consistent account naming, and group-based access assignment.

The goal is to demonstrate how identities are created, corrected, transferred, and prepared for controlled offboarding.

---

## Business Scenario

Monroe Redstone Technology Group requires a repeatable process for managing user identities throughout the employment lifecycle.

Without a controlled lifecycle process, accounts can be created inconsistently, placed in the wrong department, assigned incorrect access, retain access after a transfer, or remain active after employment ends.

This lab addresses the need to:

- Create users with a consistent naming standard
- Place users in the correct department OU
- Assign access through security groups
- Correct inaccurate OU placement and group membership
- Update access when a user changes departments
- Review account actions used during offboarding
- Validate identity state after lifecycle changes
- Preserve account history instead of immediately deleting accounts

---

## Lab Summary

In this lab, I performed identity lifecycle operations using Active Directory Users and Computers.

The workflow included:

- Reviewing the existing MRTG OU structure
- Reviewing department-based security groups
- Validating default user membership
- Assigning department-aligned group membership
- Creating a new user account
- Correcting an account that was initially aligned with the wrong department
- Updating a user's OU placement and access after a department transfer
- Reviewing account actions used during offboarding
- Validating the resulting identity state

This lab reinforced that OU placement and security group membership are related but separate controls.

OU placement supports organization, policy targeting, and delegation. Security group membership represents authorization and access entitlements. Both must be reviewed during identity lifecycle changes.

---

## Environment

| Component | Details |
|---|---|
| Domain | `mrtg.local` |
| Domain Controller | `MRTG-DC01` |
| Directory Service | Active Directory Domain Services |
| Management Tool | Active Directory Users and Computers |
| User Structure | Department-based OUs under `_MRTG\Users` |
| Group Model | Global security groups for department-aligned access |
| Virtualization Platform | Hyper-V |
| Organization | Monroe Redstone Technology Group |

---

## Prerequisites

- Operational `mrtg.local` Active Directory domain
- Established `_MRTG` OU hierarchy
- Department-based user OUs
- Department-based global security groups
- Administrative access to Active Directory Users and Computers
- Defined user naming convention
- Approved department assignments for the test identities

---

## Scope

### Included

- OU structure review
- Department security group review
- User account creation
- Default group membership validation
- Department group assignment
- Incorrect access correction
- Department transfer workflow
- OU placement validation
- Offboarding action review
- Lifecycle validation through Active Directory Users and Computers

### Not Included

- Microsoft Entra ID synchronization
- Hybrid identity
- Multifactor authentication
- Automated provisioning
- HR system integration
- PowerShell lifecycle automation
- Access certification campaigns
- Privileged Identity Management
- NTFS or share permission validation
- Permanent account deletion

---

## Directory Architecture

The MRTG directory uses department-aligned OUs and global security groups.

```text
mrtg.local
`-- _MRTG
    |-- Users
    |   |-- Engineering
    |   |-- Executives
    |   |-- Finance
    |   |-- HR
    |   |-- IT
    |   |-- Operations
    |   `-- Security
    |-- Computers
    |   |-- Servers
    |   `-- Workstations
    |-- Groups
    |-- Admin Accounts
    `-- Service Accounts
```

This structure supports:

- Department-based identity organization
- Group-based access assignment
- Lifecycle state validation
- Future delegation of control
- Future automation
- Access review workflows

---

## Identity Lifecycle Model

This lab uses the Joiner, Mover, and Leaver model.

| Lifecycle Stage | Description |
|---|---|
| Joiner | Create the account, apply the naming standard, place it in the correct OU, and assign baseline group membership |
| Mover | Update OU placement and remove or add group memberships to match the new role |
| Leaver | Disable access, remove unnecessary entitlements, preserve records, and follow the approved retention process |

The lab fully demonstrates Joiner and Mover activities. The Leaver portion reviews the available account actions and prepares for a controlled offboarding workflow rather than performing permanent deletion.

---

## Naming Convention

| Attribute | Format |
|---|---|
| Display Name | `First Last` |
| User Principal Name | `first.last@mrtg.local` |
| Pre-Windows 2000 Logon Name | `first.last` |

Example:

```text
kevin.carter@mrtg.local
```

A consistent naming standard improves searchability, administration, automation, and audit review.

---

## Security Groups

| Group | Intended Alignment |
|---|---|
| `GG_HR_Users` | HR users |
| `GG_IT_Users` | IT users |
| `GG_Finance_Users` | Finance users |
| `GG_Engineering_Users` | Engineering users |
| `GG_Operations_Users` | Operations users |
| `GG_Security_Users` | Security users |
| `GG_Remote_Desktop_Users` | Approved Remote Desktop users |

These groups represent department or access alignment. Resource-level permission enforcement is validated in later access-control labs.

---

## Lifecycle Workflow Summary

| Workflow | User | Starting State | Final State |
|---|---|---|---|
| Joiner and correction | Kevin Carter | New account initially aligned with Finance | Moved to HR and assigned `GG_HR_Users` |
| Mover | Sarah Jones | HR-aligned identity | Moved to IT and assigned `GG_IT_Users` |
| Leaver preparation | Mike Chen | Finance-aligned identity | Account actions reviewed for controlled offboarding |

---

## Implementation and Validation

### 1. Reviewed the MRTG OU Structure

The existing Active Directory structure was reviewed to confirm that department-based OUs were available for lifecycle management.

![MRTG OU structure](screenshots/lab-05-01-mrtg-ou-structure.png)

This confirmed that the environment had the required structure for Joiner, Mover, and Leaver workflows.

---

### 2. Reviewed the Global Security Groups

The existing global security groups were reviewed to confirm that department alignment could be managed through group membership.

![Global security groups](screenshots/lab-05-02-global-security-groups-created.png)

This established the group-based model used throughout the lifecycle workflow.

---

### 3. Reviewed Default User Membership

Sarah Jones's initial group membership was reviewed.

Initial membership:

```text
Domain Users
```

![Default Domain Users membership](screenshots/lab-05-03-user-default-domain-users-membership.png)

This confirmed that the user did not yet have department-aligned group membership.

---

### 4. Assigned HR Group Membership

Sarah Jones was added to:

```text
GG_HR_Users
```

![HR user group membership assigned](screenshots/lab-05-04-hr-user-group-membership-assigned.png)

This established her initial HR-aligned access state before the simulated department transfer.

---

### 5. Validated Finance Group Membership

Mike Chen's group membership was reviewed.

Confirmed department group:

```text
GG_Finance_Users
```

![Finance user group membership assigned](screenshots/lab-05-05-finance-user-group-membership-assigned.png)

This confirmed that his account was aligned with Finance before the offboarding action review.

---

### 6. Created Kevin Carter's Account

A new user account was created for Kevin Carter using the MRTG naming convention.

Initial creation path:

```text
mrtg.local/_MRTG/Users/Finance
```

![New Finance user Kevin Carter](screenshots/lab-05-06-new-finance-user-kevin-carter.png)

The initial Finance placement created a controlled correction scenario because Kevin Carter was intended to be an HR user.

---

### 7. Reviewed the Incorrect Finance Membership

Kevin Carter was initially assigned:

```text
GG_Finance_Users
```

![Kevin Carter Finance group membership](screenshots/lab-05-07-kevin-carter-finance-group-membership.png)

This represented an identity alignment error that required remediation.

---

### 8. Corrected Kevin Carter's Identity Alignment

Kevin Carter's Finance membership was removed and replaced with:

```text
GG_HR_Users
```

His user object was also aligned with the HR OU.

![Kevin Carter membership correction](screenshots/lab-05-08-kevin-carter-membership-correction.png)

This demonstrated that correcting a lifecycle error requires reviewing both access-group membership and directory placement.

---

### 9. Validated the HR OU State

The HR OU was reviewed during the workflow.

Visible users included:

```text
Kevin Carter
Sarah Jones
```

![HR users OU membership](screenshots/lab-05-13-hr-users-ou-membership.png)

This evidence represents the point in the workflow after Kevin Carter's correction and before Sarah Jones completed the move to IT.

An Active Directory user object can exist in only one OU at a time.

---

### 10. Updated Sarah Jones for the IT Transfer

Sarah Jones's HR-aligned access was updated for her transfer to IT.

Final department group:

```text
GG_IT_Users
```

![Sarah Jones IT group membership updated](screenshots/lab-05-09-sarah-jones-it-group-membership-updated.png)

The mover process required removing access that no longer matched her role and assigning the new department group.

---

### 11. Validated Sarah Jones in the IT OU

The IT OU was reviewed after the transfer.

Visible users included:

```text
John Smith
Sarah Jones
```

![IT users OU membership](screenshots/lab-05-10-it-users-ou-membership.png)

This confirmed that Sarah Jones's final OU placement matched her new IT assignment.

---

### 12. Validated Mike Chen in the Finance OU

The Finance OU was reviewed to confirm Mike Chen's directory placement.

```text
Mike Chen
```

![Finance users OU membership](screenshots/lab-05-11-finance-users-ou-membership.png)

This confirmed his Finance-aligned state before the account action review.

---

### 13. Reviewed Lifecycle Account Actions

The user account action menu was reviewed for Mike Chen.

Available actions included:

- Reset password
- Enable or disable the account, depending on its current state
- Move
- Delete
- Properties

![User account action menu](screenshots/lab-05-12-user-password-reset-action.png)

This identified the administrative controls used during lifecycle events.

Reviewing the menu is not the same as completing a full Leaver workflow. A production offboarding process would require approved disablement, entitlement removal, session revocation where supported, data ownership handling, and retention documentation.

---

## Correction Record

Kevin Carter was initially created in the Finance OU and assigned `GG_Finance_Users`, even though his intended department was HR.

The correction required:

- Removing the incorrect Finance group membership
- Adding `GG_HR_Users`
- Aligning the account with the HR OU
- Validating the corrected identity state
- Documenting the change

Identity errors are security issues until the incorrect access and placement are fully remediated.

---

## Security and IAM Relevance

Identity lifecycle management ensures that access reflects a user's current business role.

This lab supports:

- Consistent user provisioning
- Department-based identity organization
- Group-based access assignment
- Least-privilege remediation
- Controlled department transfers
- Removal of obsolete access
- Offboarding preparation
- Reduced orphaned-account risk
- Evidence-based identity validation
- Separation of OU placement and authorization

A user should not retain access from a previous or incorrect department. Lifecycle actions must be approved, documented, and validated.

---

## Risks Addressed

This lab reduces the risk of:

- Inconsistent account creation
- Incorrect OU placement
- Missing department membership
- Incorrect department access
- Retained access after a transfer
- Direct user-level permission assignments
- Excess privilege accumulation
- Unreviewed lifecycle actions
- Weak auditability
- Premature account deletion

---

## Control Mapping

| Control Area | Lab Contribution |
|---|---|
| Joiner Process | Creates a new user using the approved naming and placement model |
| Mover Process | Updates department membership and OU placement |
| Leaver Preparation | Reviews administrative account actions used during offboarding |
| Group-Based Access | Uses department security groups to represent access alignment |
| Least Privilege | Removes incorrect or obsolete department access |
| Access Correction | Documents and validates remediation of an alignment error |
| Operational Consistency | Uses repeatable naming, OU, and group patterns |
| Audit Readiness | Captures identity state before and after lifecycle changes |

---

## Validation Results

| Validation Item | Result |
|---|---|
| MRTG OU structure reviewed | Passed |
| Department security groups reviewed | Passed |
| Sarah Jones's default membership reviewed | Passed |
| Sarah Jones assigned to `GG_HR_Users` | Passed |
| Mike Chen's Finance alignment validated | Passed |
| Kevin Carter's account created | Passed |
| Kevin Carter's incorrect Finance alignment identified | Passed |
| Kevin Carter assigned to `GG_HR_Users` | Passed |
| Kevin Carter aligned with the HR OU | Passed |
| HR OU state reviewed before Sarah Jones's transfer | Passed |
| Sarah Jones assigned to `GG_IT_Users` | Passed |
| Sarah Jones aligned with the IT OU | Passed |
| Mike Chen visible in the Finance OU | Passed |
| Lifecycle account action menu reviewed | Passed |

---

## Evidence Collected

| Evidence | File |
|---|---|
| MRTG OU structure | `screenshots/lab-05-01-mrtg-ou-structure.png` |
| Global security groups | `screenshots/lab-05-02-global-security-groups-created.png` |
| Default Domain Users membership | `screenshots/lab-05-03-user-default-domain-users-membership.png` |
| HR group membership | `screenshots/lab-05-04-hr-user-group-membership-assigned.png` |
| Finance group membership | `screenshots/lab-05-05-finance-user-group-membership-assigned.png` |
| Kevin Carter account creation | `screenshots/lab-05-06-new-finance-user-kevin-carter.png` |
| Kevin Carter Finance membership | `screenshots/lab-05-07-kevin-carter-finance-group-membership.png` |
| Kevin Carter membership correction | `screenshots/lab-05-08-kevin-carter-membership-correction.png` |
| Sarah Jones IT membership | `screenshots/lab-05-09-sarah-jones-it-group-membership-updated.png` |
| IT OU membership | `screenshots/lab-05-10-it-users-ou-membership.png` |
| Finance OU membership | `screenshots/lab-05-11-finance-users-ou-membership.png` |
| User account action menu | `screenshots/lab-05-12-user-password-reset-action.png` |
| HR OU workflow state | `screenshots/lab-05-13-hr-users-ou-membership.png` |

---

## What I Would Improve in Production

In a production environment, I would:

- Use a formal Joiner, Mover, and Leaver request process
- Require manager and data-owner approval for access changes
- Define baseline access packages for each department and role
- Integrate lifecycle events with the authoritative HR system
- Automate provisioning with PowerShell or an IAM platform
- Require ticket or request identifiers for every change
- Use peer review for account creation and sensitive group assignments
- Verify that obsolete access is removed during transfers
- Perform post-transfer access reviews
- Disable departing-user accounts promptly
- Revoke active sessions where supported
- Move disabled accounts to a dedicated Disabled Users OU
- Remove group memberships according to the offboarding standard
- Define account and data retention periods before deletion
- Transfer ownership of files, mailboxes, and business records
- Monitor lifecycle events through centralized logging
- Conduct recurring access certification reviews

---

## Lessons Learned

This lab reinforced that identity lifecycle management includes more than creating accounts.

A complete lifecycle process covers onboarding, role changes, access correction, offboarding, retention, and evidence collection.

OU placement and security group membership must both be maintained. A user can be in the correct OU while retaining incorrect access through group membership, or have the correct group membership while remaining in the wrong policy scope.

The Kevin Carter correction demonstrated that identity errors require complete remediation and validation. The Sarah Jones transfer demonstrated that new access should be added only after obsolete access is removed or reviewed.

Lifecycle management is where IAM becomes an operational discipline.

---

## Outcome

Lab 05 successfully implemented foundational identity lifecycle workflows in the MRTG Active Directory environment.

The lab confirmed that:

- The MRTG OU structure supported lifecycle operations
- Department security groups supported group-based alignment
- Sarah Jones received HR alignment before the simulated transfer
- Mike Chen's Finance alignment was validated
- Kevin Carter was created using the established naming standard
- Kevin Carter's incorrect Finance alignment was identified
- Kevin Carter was corrected to the HR OU and `GG_HR_Users`
- Sarah Jones was transferred to the IT OU and `GG_IT_Users`
- Account actions used during lifecycle events were reviewed
- Access alignment was managed through group membership rather than direct assignment

The environment now supports controlled onboarding, transfer, correction, and offboarding preparation.

---

## Next Lab

[Lab 06: NTFS and Share Permissions](../Lab-06-NTFS-and-Share-Permissions/)

Lab 06 applies NTFS and share permissions to department resources using Active Directory users and security groups.
