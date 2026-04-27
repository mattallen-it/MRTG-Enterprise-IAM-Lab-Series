# Lab-05 — Identity Lifecycle Management

![Windows Server](https://img.shields.io/badge/Windows%20Server-Active%20Directory-blue)
![Hyper-V](https://img.shields.io/badge/Hyper--V-Lab%20Environment-5C2D91)
![AD DS](https://img.shields.io/badge/AD%20DS-Identity%20Platform-0A66C2)
![IAM](https://img.shields.io/badge/IAM-Identity%20Lifecycle-darkgreen)
![RBAC](https://img.shields.io/badge/RBAC-Group%20Based%20Access-4B0082)

## Overview
In this lab, I implemented identity lifecycle management processes within the Monroe Redstone Technology Group (MRTG) Active Directory environment. Building on the organizational unit and group structure established in earlier labs, this lab focused on joiner, mover, and leaver workflows using department-aligned OUs and role-based security groups. The objective was to show how user identities are created, updated, and offboarded in a controlled enterprise environment.

## Objectives
- Apply a consistent user naming convention during account creation
- Use department-aligned OUs for user placement
- Assign access through department security groups
- Simulate a joiner workflow for a new HR employee
- Simulate a mover workflow for a department transfer from HR to IT
- Simulate a leaver workflow through controlled account disablement
- Validate that user placement and group membership reflect lifecycle changes

## Scope
This lab focuses on identity lifecycle operations in an on-premises Active Directory environment, including onboarding, internal role change, and offboarding. It does not include hybrid identity, Entra ID synchronization, MFA, automated provisioning, or HR system integration.

## Lab Environment

| Component | Details |
|---|---|
| Organization | Monroe Redstone Technology Group (MRTG) |
| Domain | mrtg.local |
| Domain Controller | MRTG-DC01 |
| Platform | Hyper-V |
| Core Services | Active Directory Domain Services, DNS, Group Policy |
| Directory Structure | Department-based OUs under `_MRTG/Users` |
| Group Model | Global security groups for departmental access |

## Architecture / Design
The MRTG directory is structured around department-aligned OUs and global security groups to support organized identity administration. User objects are placed in the appropriate departmental OU, while access alignment is handled through group membership rather than direct one-off assignment. This model supports cleaner lifecycle operations by separating organizational placement from access control while still keeping both aligned during onboarding, transfers, and offboarding.

## Technologies Used
- Active Directory Users and Computers
- Active Directory Domain Services (AD DS)
- Windows Server
- DNS
- Hyper-V

## Naming Convention
This lab uses the naming convention established previously in the environment:

- **Display Name:** `First Last`
- **User Logon Name (UPN):** `first.last@mrtg.local`

Example:
- `kevin.carter@mrtg.local`

## Security Groups Used
- `GG_HR_Users`
- `GG_IT_Users`
- `GG_Finance_Users`

## Implementation Steps

### Step 1 - Review Existing MRTG Directory Structure
Reviewed the existing MRTG Active Directory structure to confirm that the environment already contained dedicated OUs for users, computers, service accounts, admin accounts, and department-specific user containers. This established the foundation for lifecycle operations without rebuilding prior lab work.

![Step 1](images/lab05-01.png)

### Step 2 - Review Department Security Groups
Reviewed the existing departmental security groups used to assign access by role and department. This confirmed that the lifecycle workflows would use group-based access assignment rather than direct per-user permission decisions.

![Step 2](images/lab05-02.png)

### Step 3 - Validate Existing HR Group Alignment
Reviewed Sarah Jones’s initial group membership and found that her account was only a member of `Domain Users`. This exposed a gap between OU placement and group-based access assignment that needed to be corrected before simulating a department transfer.

![Step 3](images/lab05-03.png)

### Step 4 - Correct HR Group Membership
Added Sarah Jones to `GG_HR_Users` to align her account with the HR department before beginning the mover workflow. This created a proper baseline state for a later department transfer to IT.

![Step 4](images/lab05-04.png)

### Step 5 - Validate Finance Group Membership
Reviewed Mike Chen’s existing group membership and confirmed that his account was properly aligned to Finance through `GG_Finance_Users`. This established a clean baseline before using his account in the leaver workflow.

![Step 5](images/lab05-05.png)

### Step 6 - Create a New User Following the Established Naming Standard
Created a new user account for Kevin Carter using the existing MRTG naming convention of `first.last`. The new account was prepared as part of the joiner workflow and placed into the HR lifecycle scenario.

![Step 6](images/lab05-06.png)

### Step 7 - Place the New Hire in the HR OU
Confirmed that Kevin Carter was placed in the HR organizational unit. This aligned the user object with the proper department container and supported a realistic onboarding scenario.

![Step 7](images/lab05-07.png)

### Step 8 - Assign the New Hire to the HR Security Group
Added Kevin Carter to `GG_HR_Users` so that his access alignment matched his department assignment. This completed the joiner workflow by pairing correct OU placement with correct group-based access.

![Step 8](images/lab05-08.png)

### Step 9 - Update Group Membership for a Department Transfer
Changed Sarah Jones’s membership from `GG_HR_Users` to `GG_IT_Users` to reflect a transfer from HR to IT. This demonstrated that access changes should follow the user’s new role or department.

![Step 9](images/lab05-09.png)

### Step 10 - Move the User Object to the IT OU
Moved Sarah Jones into the IT OU so that her organizational placement matched her new department. This completed the mover workflow by aligning both access group membership and directory placement.

![Step 10](images/lab05-10.png)

### Step 11 - Disable a Departing Employee Account
Disabled Mike Chen’s Finance account to simulate a controlled leaver workflow. Instead of deleting the account immediately, the account was retained in a disabled state, reflecting a more realistic offboarding practice for foundational IAM administration.

![Step 11](images/lab05-11.png)

## Validation / Verification
- Confirmed the MRTG directory already contained department-aligned OUs and security groups
- Verified Sarah Jones was corrected into `GG_HR_Users` before the mover workflow began
- Verified Kevin Carter was created using the established `first.last` naming standard
- Confirmed Kevin Carter was placed in the HR OU and assigned to `GG_HR_Users`
- Confirmed Sarah Jones was reassigned from `GG_HR_Users` to `GG_IT_Users`
- Confirmed Sarah Jones was moved into the IT OU
- Confirmed Mike Chen remained aligned to Finance before offboarding
- Confirmed Mike Chen’s account was disabled as the leaver action

## Outcome
This lab demonstrated a practical identity lifecycle workflow inside the MRTG Active Directory environment by showing how user identities are onboarded, updated, and offboarded in a controlled way. The lab reinforced two core IAM principles: identities should be placed correctly within the directory structure, and access should follow group membership rather than direct manual assignment. It also established a stronger foundation for the next lab, where these same groups can be applied to resource access through NTFS and share permissions.

## Next Lab

[**Lab-06 — NTFS and Share Permissions**](../Lab-06-NTFS-and-Share-Permissions)

Lab-06 builds on identity lifecycle management by applying NTFS and share permissions to control access to department resources using Active Directory users and groups.
