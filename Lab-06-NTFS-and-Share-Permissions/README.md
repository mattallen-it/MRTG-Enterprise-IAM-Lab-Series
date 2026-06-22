# Lab 06: NTFS and Share Permissions

![Platform](https://img.shields.io/badge/Platform-Windows%20Server%202022-blue)
![Technology](https://img.shields.io/badge/Technology-Active%20Directory-blue)
![Service](https://img.shields.io/badge/Service-SMB%20File%20Sharing-lightgrey)
![Access](https://img.shields.io/badge/Access-NTFS%20%26%20Share%20Permissions-orange)
![Model](https://img.shields.io/badge/Model-Group%20Based-purple)
![Validation](https://img.shields.io/badge/Validation-Least%20Privilege-brightgreen)

---

## Objective

Configure department-based resource access using NTFS permissions and SMB share permissions in the `mrtg.local` Active Directory domain.

This lab builds on the lifecycle workflows from Lab 05 by connecting department security group membership to access on shared resources.

The goal is to enforce least privilege through centralized, group-based authorization.

---

## Business Scenario

Monroe Redstone Technology Group requires secure and manageable access to departmental file shares.

Assigning permissions directly to individual users creates inconsistent access, makes transfers and offboarding harder, and increases the risk of permission drift.

This lab addresses the need to:

- Create department-specific shared folders
- Assign permissions through Active Directory security groups
- Configure SMB share permissions
- Configure NTFS permissions
- Validate authorized access
- Confirm that unauthorized access is denied
- Apply a consistent permission model across departments
- Connect identity lifecycle changes to resource authorization

---

## Lab Summary

In this lab, I created departmental shared folders for HR, IT, and Finance.

SMB share permissions and NTFS permissions were assigned to department-based Active Directory security groups.

Access was tested with users from different departments. Authorized users could access resources associated with their department, while users without the required group membership were denied.

This demonstrated how Active Directory group membership becomes enforceable resource access.

---

## Environment

| Component | Details |
|---|---|
| Domain | `mrtg.local` |
| Domain Controller and Lab File Server | `MRTG-DC01` |
| Directory Service | Active Directory Domain Services |
| File Sharing Protocol | SMB |
| Local Share Path | `C:\Shares` |
| HR Group | `GG_HR_Users` |
| IT Group | `GG_IT_Users` |
| Finance Group | `GG_Finance_Users` |
| Virtualization Platform | Hyper-V |
| Organization | Monroe Redstone Technology Group |

---

## Prerequisites

- Operational `mrtg.local` Active Directory domain
- Department-based global security groups
- HR-aligned test user Kevin Carter
- IT-aligned test user Sarah Jones
- Administrative access to `MRTG-DC01`
- Network connectivity to the file shares
- Completed identity lifecycle changes from Lab 05

---

## Scope

### Included

- Department folder creation
- SMB share configuration
- Share permission configuration
- NTFS permission configuration
- Group-based access assignment
- HR authorized-access validation
- IT authorized-access validation
- Cross-department access-denial testing
- Least-privilege validation

### Not Included

- Dedicated production file server deployment
- Distributed File System namespaces
- File Server Resource Manager
- File screening
- Access-based enumeration
- Advanced object-access auditing
- Dynamic Access Control
- Data Loss Prevention
- Cloud file services
- Microsoft Entra ID authorization

---

## Resource Architecture

Department folders were created under a central share directory.

```text
C:\Shares
|-- Finance
|-- HR
`-- IT
```

Each resource was mapped to a department security group.

```text
GG_HR_Users      -> HR share
GG_IT_Users      -> IT share
GG_Finance_Users -> Finance share
Administrators   -> Administrative control
SYSTEM           -> System control
```

This design supports:

- Group-based authorization
- Department resource separation
- Least privilege
- Repeatable permission assignment
- Cleaner onboarding and transfer workflows
- Easier access reviews
- Improved auditability

---

## Permission Model

Access to an SMB resource is evaluated through both share and NTFS permissions.

| Permission Layer | Purpose |
|---|---|
| Share Permissions | Control access when the resource is reached over the network |
| NTFS Permissions | Control file and folder access at the file-system level |
| Security Groups | Represent approved department access |

When a user accesses a folder through SMB, the effective result is the most restrictive combination of the applicable share and NTFS permissions.

Permissions were assigned to groups rather than individual user accounts.

---

## Department Access Model

| Department | Security Group | Network Resource |
|---|---|---|
| HR | `GG_HR_Users` | `\\MRTG-DC01\HR` |
| IT | `GG_IT_Users` | `\\MRTG-DC01\IT` |
| Finance | `GG_Finance_Users` | `\\MRTG-DC01\Finance` |

Users receive access by becoming members of the appropriate department group.

Access denial is produced by the absence of an applicable Allow permission. Explicit Deny entries should be used only when there is a specific requirement because they can complicate permission evaluation and troubleshooting.

---

## Implementation and Validation

### 1. Created the Department Folders

A central folder structure was created on `MRTG-DC01`.

Department folders included:

- Finance
- HR
- IT

![Department share folders created](screenshots/lab-06-01-department-share-folders-created.png)

This established the resource structure for department-based access control.

---

### 2. Configured the HR Share Permissions

The HR folder was published as an SMB share.

The following group was assigned at the share layer:

```text
GG_HR_Users
```

Assigned share permissions:

```text
Change
Read
```

![HR share permissions configured](screenshots/lab-06-02-hr-share-permissions-configured.png)

This established the network-access layer for the HR resource.

---

### 3. Configured the HR NTFS Permissions

NTFS permissions were configured on the HR folder.

`GG_HR_Users` received:

```text
Modify
```

Administrative and system-level control remained assigned to the appropriate privileged principals.

![HR NTFS permissions configured](screenshots/lab-06-03-hr-ntfs-permissions-configured.png)

This established the file-system authorization layer for the HR resource.

---

### 4. Applied the Department Permission Model

The same group-to-resource pattern was applied to the IT and Finance folders.

| Folder | Authorized Group |
|---|---|
| HR | `GG_HR_Users` |
| IT | `GG_IT_Users` |
| Finance | `GG_Finance_Users` |

Using the same design across departments improves consistency and makes permissions easier to review.

---

### 5. Validated Authorized HR Access

Kevin Carter's HR-aligned account was used to access:

```text
\\MRTG-DC01\HR
```

![HR user access allowed](screenshots/lab-06-04-hr-user-access-allowed.png)

The share opened successfully, confirming that `GG_HR_Users` membership provided the intended access.

---

### 6. Validated Denial of IT Access for the HR User

Kevin Carter's account was used to access:

```text
\\MRTG-DC01\IT
```

![HR user IT access denied](screenshots/lab-06-05-hr-user-it-access-denied.png)

Access was denied because the user did not have the required IT group membership.

---

### 7. Validated Authorized IT Access

Sarah Jones's IT-aligned account was used to access:

```text
\\MRTG-DC01\IT
```

![IT user access allowed](screenshots/lab-06-06-it-user-access-allowed.png)

The share opened successfully, confirming that `GG_IT_Users` membership provided the intended access.

---

### 8. Validated Denial of HR Access for the IT User

Sarah Jones's account was used to access:

```text
\\MRTG-DC01\HR
```

![IT user HR access denied](screenshots/lab-06-07-it-user-hr-access-denied.png)

Access was denied because the user's previous HR alignment had been removed during the Mover workflow in Lab 05.

This confirmed that the lifecycle change affected the user's effective resource access.

---

## Effective Access Results

| User | Department Alignment | HR Share | IT Share |
|---|---|---|---|
| Kevin Carter | HR | Allowed | Denied |
| Sarah Jones | IT | Denied | Allowed |

The results matched the users' current department group memberships.

Finance permissions were configured using the same model, but user-level Finance access testing was not included in the captured evidence for this lab.

---

## Security and IAM Relevance

This lab connects identity administration to resource authorization.

Organizational Units help organize identities and target policy. Security groups represent approved access. NTFS and share permissions enforce that access against data.

This lab supports:

- Least privilege
- Group-based authorization
- Department resource separation
- Reduced direct user permissions
- Consistent access assignment
- Lifecycle-driven access changes
- Access-denial validation
- Cleaner access reviews
- Evidence-based authorization testing

The access results also demonstrate why Mover workflows must remove obsolete group memberships. Adding new access without removing old access can create privilege accumulation.

---

## Risks Addressed

This lab reduces the risk of:

- Direct user-level permission assignments
- Excessive department access
- Unauthorized file-share access
- Retained access after a department transfer
- Inconsistent permission configuration
- Permission drift
- Weak resource separation
- Poor access-review visibility
- Unvalidated authorization controls

---

## Control Mapping

| Control Area | Lab Contribution |
|---|---|
| Resource Access Control | Uses NTFS and SMB share permissions to protect department folders |
| Group-Based Authorization | Assigns permissions to department security groups |
| Least Privilege | Limits users to resources associated with their current department |
| Resource Separation | Prevents HR and IT users from accessing each other's department shares |
| Lifecycle Continuity | Uses group changes from Lab 05 to determine effective access |
| Operational Consistency | Applies the same permission pattern across departments |
| Audit Readiness | Documents configuration and user-level access results |

---

## Validation Results

| Validation Item | Result |
|---|---|
| Department folders created under `C:\Shares` | Passed |
| HR share permissions configured | Passed |
| HR NTFS permissions configured | Passed |
| IT permission model applied | Passed |
| Finance permission model applied | Passed |
| Kevin Carter accessed the HR share | Passed |
| Kevin Carter was denied access to the IT share | Passed |
| Sarah Jones accessed the IT share | Passed |
| Sarah Jones was denied access to the HR share | Passed |
| Access results matched current group membership | Passed |

---

## Evidence Collected

| Evidence | File |
|---|---|
| Department share folders | `screenshots/lab-06-01-department-share-folders-created.png` |
| HR share permissions | `screenshots/lab-06-02-hr-share-permissions-configured.png` |
| HR NTFS permissions | `screenshots/lab-06-03-hr-ntfs-permissions-configured.png` |
| Authorized HR access | `screenshots/lab-06-04-hr-user-access-allowed.png` |
| HR user denied IT access | `screenshots/lab-06-05-hr-user-it-access-denied.png` |
| Authorized IT access | `screenshots/lab-06-06-it-user-access-allowed.png` |
| IT user denied HR access | `screenshots/lab-06-07-it-user-hr-access-denied.png` |

---

## What I Would Improve in Production

In a production environment, I would:

- Use a dedicated file server instead of hosting shares on a domain controller
- Separate identity infrastructure from general application and file services
- Apply the AGDLP model for scalable permission management
- Place user accounts in global role groups
- Place global role groups in domain local resource groups
- Assign NTFS permissions to domain local resource groups
- Use a formal share and folder naming standard
- Document a business owner and data owner for each share
- Require approved access requests
- Enable access-based enumeration where appropriate
- Audit access to sensitive department data
- Review access groups regularly
- Monitor permission and group-membership changes
- Use change management for ACL modifications
- Document permission inheritance and exception handling
- Test effective access after every permission change
- Avoid explicit Deny entries unless they are required and documented

---

## Lessons Learned

This lab reinforced that group membership becomes meaningful when it controls access to actual resources.

Users and groups form the identity layer. NTFS and share permissions form the enforcement layer.

The main takeaway is that permissions should be assigned to groups rather than directly to users. This makes onboarding, transfers, offboarding, troubleshooting, and recurring access reviews more manageable.

The cross-department tests also showed that successful access testing is not enough. A complete authorization test must confirm both expected access and expected denial.

---

## Outcome

Lab 06 successfully implemented group-based access control for departmental file shares in the MRTG Active Directory environment.

The lab confirmed that:

- Department folders were created under `C:\Shares`
- SMB share permissions were configured
- NTFS permissions were configured
- Department security groups controlled access
- Kevin Carter could access HR resources
- Kevin Carter was denied access to IT resources
- Sarah Jones could access IT resources
- Sarah Jones was denied access to HR resources
- Access results matched current department membership

The MRTG environment now enforces least-privilege access to departmental file resources using Active Directory security groups.

---

## Next Lab

[Lab 07: Service Accounts and Delegation](../Lab-07-Service-Accounts-and-Delegation/)

Lab 07 introduces service accounts and delegated administrative permissions to support controlled non-human identity use and least-privilege administration.
