# Lab 18 — Group-Based Access Control for File and Department Resources

![Platform](https://img.shields.io/badge/Platform-Windows%20Server-blue)
![Technology](https://img.shields.io/badge/Technology-Active%20Directory-blue)
![Tooling](https://img.shields.io/badge/Tooling-NTFS%20%26%20SMB-purple)
![Focus](https://img.shields.io/badge/Focus-Group--Based%20Access%20Control-orange)
![Security](https://img.shields.io/badge/Security-Least%20Privilege-red)
![Validation](https://img.shields.io/badge/Validation-Access%20Allowed%20%26%20Denied-brightgreen)

---

## Overview

In this lab, I implemented group-based access control for department file resources in the Monroe Redstone Technology Group Active Directory environment.

The goal was to control access to shared department folders using Active Directory security groups instead of assigning permissions directly to individual users.

This lab used an AGDLP-style access model:

```text
Accounts → Global Groups → Domain Local Groups → Permissions
```

This design supports least privilege, cleaner access reviews, stronger audit evidence, and better identity lifecycle management.

---

## Business Problem

MRTG needed a scalable way to control access to department file resources without assigning permissions directly to individual users.

Direct user permissions become difficult to manage as users join, move between departments, or leave the organization. They also make access reviews harder because permissions are scattered across individual accounts instead of being tied to business roles and resource groups.

This lab solves that problem by using Active Directory security groups to control access to department folders. Users receive access through department membership, and folder permissions are assigned to resource access groups.

---

## Lab Summary

In this lab, I created a department file share structure and applied group-based access control using Active Directory security groups, SMB share permissions, and NTFS permissions.

The design used department Global Groups for user membership and Domain Local Groups for resource permissions. Department Global Groups were nested into matching Domain Local resource groups, and those resource groups were assigned NTFS Modify permissions on the appropriate department folders.

The lab validated both authorized access and denied access:

- HR users could access the HR folder.
- HR users could not access the Finance folder.
- Finance users could access the Finance folder.
- Finance users could not access the HR folder.

This confirmed that access was controlled through group membership and that department boundaries were enforced through NTFS permissions.

---

## Objectives

- Create a structured department file share
- Create Domain Local resource access groups
- Use existing Global Groups for department membership
- Nest Global Groups into Domain Local Groups
- Configure SMB share permissions at the root share level
- Configure NTFS permissions at the department folder level
- Validate authorized access for department users
- Validate unauthorized access denial across departments
- Confirm access through user context and group membership
- Create a post-lab checkpoint after successful validation

---

## Scope

### Included

- Department folder structure creation
- Active Directory group-based access control
- Global Group and Domain Local Group usage
- SMB share permissions
- NTFS folder permissions
- User access validation
- Access denied testing
- Evidence collection through screenshots
- Hyper-V checkpoint creation after validation

### Not Included

- DFS Namespace configuration
- File Server Resource Manager quotas
- Access-based enumeration
- Advanced file access auditing
- Classification labels
- Data Loss Prevention controls
- Microsoft Entra ID group-based access
- Production file server hardening

---

## Lab Environment

| Component | Details |
|---|---|
| Organization | Monroe Redstone Technology Group |
| Domain | `mrtg.local` |
| Domain Controller | `MRTG-DC01` |
| Client Workstation | `MRTG-CLIENT-01` |
| File Share Host | `MRTG-DC01` |
| Local Folder Path | `C:\MRTG-Shared-Resources` |
| Network Share | `\\MRTG-DC01\MRTG-Shared` |
| HR Test User | `mrtg\kevin.carter` |
| Finance Test User | `mrtg\mike.chen` |
| Hypervisor | Hyper-V |

---

## Scenario

Monroe Redstone Technology Group needed a clean and repeatable way to control access to department file resources.

Instead of granting folder permissions directly to users, permissions were assigned through security groups. This allows access to be managed by changing group membership instead of modifying folder permissions every time a user joins, moves, or leaves a department.

The access design used in this lab was:

```text
User Account → Department Global Group → Resource Domain Local Group → Folder Permission
```

Example:

```text
kevin.carter → GG_HR_Users → DL_HR_Share_RW → HR Folder
```

---

## Access Control Design

### Department Global Groups

| Department | Global Group |
|---|---|
| HR | `GG_HR_Users` |
| Finance | `GG_Finance_Users` |
| IT | `GG_IT_Users` |
| Operations | `GG_Operations_Users` |

### Resource Access Groups

| Department Folder | Domain Local Group | Permission |
|---|---|---|
| HR | `DL_HR_Share_RW` | Modify |
| Finance | `DL_Finance_Share_RW` | Modify |
| IT | `DL_IT_Share_RW` | Modify |
| Operations | `DL_Operations_Share_RW` | Modify |

### Group Nesting Model

| Domain Local Group | Member Group |
|---|---|
| `DL_HR_Share_RW` | `GG_HR_Users` |
| `DL_Finance_Share_RW` | `GG_Finance_Users` |
| `DL_IT_Share_RW` | `GG_IT_Users` |
| `DL_Operations_Share_RW` | `GG_Operations_Users` |

---

## Security Design

The access model separates user identity from resource permissions.

| Layer | Purpose |
|---|---|
| User Account | Represents the individual user |
| Global Group | Represents department or business role membership |
| Domain Local Group | Represents access to a specific resource |
| NTFS Permission | Enforces access on the folder |
| Share Permission | Allows authenticated users to reach the share path |

Instead of asking, "Which users are directly assigned to this folder?", the better question becomes, "Which group grants access to this folder, and who is a member of that group?"

---

## Implementation Steps

### Step 1 — Created Pre-Lab Checkpoint

Created a Hyper-V checkpoint before making Lab 18 changes.

This provided a rollback point before configuring shared folders, groups, and permissions.

![Pre-Lab Checkpoint](screenshots/lab-18-01-pre-lab-checkpoint.png)

---

### Step 2 — Created Department Folder Structure

Created the department folder structure on `MRTG-DC01`.

Root folder:

```text
C:\MRTG-Shared-Resources
```

Department folders:

```text
Finance
HR
IT
Operations
```

![Department Folder Structure](screenshots/lab-18-02-department-folder-structure.png)

---

### Step 3 — Created and Verified Security Groups

Created Domain Local resource access groups for department folder permissions.

The environment already contained department Global Groups used for user membership.

![Security Groups Created](screenshots/lab-18-03-security-groups-created.png)

---

### Step 4 — Configured Group Membership

Nested department Global Groups into their matching Domain Local resource groups.

Example:

```text
GG_HR_Users → DL_HR_Share_RW
```

This allows HR users to receive access through group membership instead of direct folder assignment.

![Group Membership Configured](screenshots/lab-18-04-group-membership-configured.png)

---

### Step 5 — Configured Share Permissions

Shared the root department resource folder as:

```text
\\MRTG-DC01\MRTG-Shared
```

Share permissions were configured to allow authenticated users to reach the share.

Department-level access control was handled through NTFS permissions on each folder.

![Share Permissions Configured](screenshots/lab-18-05-share-permissions-configured.png)

---

### Step 6 — Configured HR NTFS Permissions

Configured the HR folder with the matching Domain Local resource group.

```text
DL_HR_Share_RW → Modify
```

Broad inherited access was removed so access would be controlled by the assigned resource group.

![HR NTFS Permissions Configured](screenshots/lab-18-06-hr-ntfs-permissions-configured.png)

---

### Step 7 — Configured Finance NTFS Permissions

Configured the Finance folder with the matching Domain Local resource group.

```text
DL_Finance_Share_RW → Modify
```

![Finance NTFS Permissions Configured](screenshots/lab-18-07-finance-ntfs-permissions-configured.png)

---

### Step 8 — Configured IT NTFS Permissions

Configured the IT folder with the matching Domain Local resource group.

```text
DL_IT_Share_RW → Modify
```

![IT NTFS Permissions Configured](screenshots/lab-18-08-it-ntfs-permissions-configured.png)

---

### Step 9 — Configured Operations NTFS Permissions

Configured the Operations folder with the matching Domain Local resource group.

```text
DL_Operations_Share_RW → Modify
```

![Operations NTFS Permissions Configured](screenshots/lab-18-09-operations-ntfs-permissions-configured.png)

---

## Validation

### Step 10 — Accessed the Shared Folder from the Client

Accessed the shared folder from `MRTG-CLIENT-01` using the UNC path:

```text
\\MRTG-DC01\MRTG-Shared
```

The department folders were visible from the client workstation.

![Shared Folder Accessed From Client](screenshots/lab-18-10-shared-folder-accessed-from-client.png)

---

### Step 11 — Verified HR User Context

Confirmed the signed-in user context on the client workstation.

Command used:

```cmd
whoami
```

Expected user:

```text
mrtg\kevin.carter
```

![HR User Context Verified](screenshots/lab-18-11-hr-user-context-verified.png)

---

### Step 12 — Validated HR Group Membership

Validated the HR user's group membership.

Command used:

```cmd
whoami /groups
```

The output confirmed that `kevin.carter` had the expected HR access chain:

```text
GG_HR_Users
DL_HR_Share_RW
```

![HR Group Membership Validated](screenshots/lab-18-12-hr-group-membership-validated.png)

---

### Step 13 — Validated HR Authorized Access

Confirmed that the HR user could access the HR folder.

Path tested:

```text
\\MRTG-DC01\MRTG-Shared\HR
```

Test file created:

```text
HR-access-test.txt
```

![HR Authorized Access Validated](screenshots/lab-18-13-hr-authorized-access-validated.png)

---

### Step 14 — Validated HR Unauthorized Access Denial

Confirmed that the HR user could not access the Finance folder.

Path tested:

```text
\\MRTG-DC01\MRTG-Shared\Finance
```

The access attempt was denied as expected.

![HR User Finance Access Denied](screenshots/lab-18-14-hr-user-finance-access-denied.png)

---

### Step 15 — Confirmed Finance User Remote Desktop Access

Confirmed that the Finance test user was part of the Remote Desktop access group so the account could sign in to the client workstation for validation.

![Finance User RDP Group Membership](screenshots/lab-18-15-finance-user-rdp-group-membership.png)

---

### Step 16 — Validated Finance User Group Membership

Confirmed the signed-in Finance user context and validated group membership.

Expected user:

```text
mrtg\mike.chen
```

Expected group path:

```text
GG_Finance_Users
DL_Finance_Share_RW
```

![Finance Group Membership Validated](screenshots/lab-18-16-finance-group-membership-validated.png)

---

### Step 17 — Validated Finance Authorized Access

Confirmed that the Finance user could access the Finance folder.

Path tested:

```text
\\MRTG-DC01\MRTG-Shared\Finance
```

Test file created:

```text
Finance-access-test.txt
```

![Finance Authorized Access Validated](screenshots/lab-18-17-finance-authorized-access-validated.png)

---

### Step 18 — Validated Finance Unauthorized Access Denial

Confirmed that the Finance user could not access the HR folder.

Path tested:

```text
\\MRTG-DC01\MRTG-Shared\HR
```

The access attempt was denied as expected.

![Finance User HR Access Denied](screenshots/lab-18-18-finance-user-hr-access-denied.png)

---

### Step 19 — Created Post-Lab Checkpoint

Created a final checkpoint after validating group-based access control.

Checkpoint name:

```text
MRTG-DC01_Post-Lab-18-Group-Based-Access-Control-Validated
```

![Post-Lab Checkpoint](screenshots/lab-18-19-post-lab-checkpoint.png)

---

## Validation Summary

| Test | Expected Result | Actual Result | Status |
|---|---|---|---|
| HR user accesses HR folder | Access allowed | Access allowed | Passed |
| HR user accesses Finance folder | Access denied | Access denied | Passed |
| Finance user accesses Finance folder | Access allowed | Access allowed | Passed |
| Finance user accesses HR folder | Access denied | Access denied | Passed |
| Permissions assigned directly to users | No direct user assignment | No direct user assignment | Passed |
| Permissions assigned through groups | Group-based assignment | Group-based assignment | Passed |
| Department folders reachable through root share | Share reachable | Share reachable | Passed |
| NTFS permissions enforce department boundaries | Department-specific enforcement | Department-specific enforcement | Passed |

---

## Evidence Collected

| Evidence | Screenshot |
|---|---|
| Pre-lab checkpoint created | `screenshots/lab-18-01-pre-lab-checkpoint.png` |
| Department folder structure created | `screenshots/lab-18-02-department-folder-structure.png` |
| Security groups created | `screenshots/lab-18-03-security-groups-created.png` |
| Group membership configured | `screenshots/lab-18-04-group-membership-configured.png` |
| Share permissions configured | `screenshots/lab-18-05-share-permissions-configured.png` |
| HR NTFS permissions configured | `screenshots/lab-18-06-hr-ntfs-permissions-configured.png` |
| Finance NTFS permissions configured | `screenshots/lab-18-07-finance-ntfs-permissions-configured.png` |
| IT NTFS permissions configured | `screenshots/lab-18-08-it-ntfs-permissions-configured.png` |
| Operations NTFS permissions configured | `screenshots/lab-18-09-operations-ntfs-permissions-configured.png` |
| Shared folder accessed from client | `screenshots/lab-18-10-shared-folder-accessed-from-client.png` |
| HR user context verified | `screenshots/lab-18-11-hr-user-context-verified.png` |
| HR group membership validated | `screenshots/lab-18-12-hr-group-membership-validated.png` |
| HR authorized access validated | `screenshots/lab-18-13-hr-authorized-access-validated.png` |
| HR unauthorized Finance access denied | `screenshots/lab-18-14-hr-user-finance-access-denied.png` |
| Finance user RDP group membership confirmed | `screenshots/lab-18-15-finance-user-rdp-group-membership.png` |
| Finance group membership validated | `screenshots/lab-18-16-finance-group-membership-validated.png` |
| Finance authorized access validated | `screenshots/lab-18-17-finance-authorized-access-validated.png` |
| Finance unauthorized HR access denied | `screenshots/lab-18-18-finance-user-hr-access-denied.png` |
| Post-lab checkpoint created | `screenshots/lab-18-19-post-lab-checkpoint.png` |

---

## Skills Demonstrated

- Active Directory group management
- Global Group and Domain Local Group design
- AGDLP-style permission modeling
- SMB share configuration
- NTFS permission configuration
- Least privilege access control
- Department-based authorization
- User context validation with `whoami`
- Group membership validation with `whoami /groups`
- Authorized access testing
- Unauthorized access denial testing
- IAM documentation and evidence capture

---

## Real-World Relevance

Group-based access control is a core IAM practice used to manage access to shared resources at scale.

In enterprise, government, and defense contractor environments, access should not be granted directly to individual users unless there is a specific exception and approval process.

A cleaner model is to assign users to business-role groups and assign permissions to resource groups.

This improves:

- Access review accuracy
- Permission auditing
- Joiner, mover, and leaver workflows
- Department resource protection
- Least privilege enforcement
- Operational consistency
- Security boundary validation

---

## Lessons Learned

- Direct user permissions are harder to manage and audit than group-based access
- Global Groups are useful for organizing users by department or role
- Domain Local Groups are useful for assigning permissions to resources
- NTFS permissions should enforce the actual access boundary
- Share permissions should allow users to reach the share, but NTFS should control folder-level access
- Successful access testing is not enough
- Denied-access testing proves that least privilege is actually working
- A clean access model supports better IAM operations and future audits

---

## What I Would Improve in Production

In a production environment, I would improve this implementation by:

- Enabling access-based enumeration so users only see folders they can access
- Using a dedicated file server instead of a domain controller for file shares
- Adding file access auditing for sensitive department folders
- Creating a formal access request and approval workflow
- Reviewing group membership on a scheduled basis
- Using naming standards for resource groups and department groups
- Separating read-only and modify access groups
- Documenting data owners for each department folder
- Monitoring permission changes through centralized logging or SIEM
- Testing access using dedicated test accounts before production rollout

---

## Outcome

Lab 18 successfully implemented and validated group-based access control for department file resources.

The final model used Active Directory security groups, SMB share permissions, and NTFS permissions to enforce least privilege.

The HR user was able to access the HR folder and was denied access to Finance. The Finance user was able to access the Finance folder and was denied access to HR.

This confirmed that department access was controlled through group membership rather than direct user permissions.

---

## Next Lab

[Lab 19 — Active Directory Certificate Services](../Lab-19-Active-Directory-Certificate-Services/)

Lab 19 will build on the identity and infrastructure foundation by introducing Active Directory Certificate Services, focusing on enterprise trust, certificate authority configuration, certificate enrollment, and internal PKI validation in the MRTG domain.
