# Lab-17 - Windows LAPS and Local Administrator Control

![Platform](https://img.shields.io/badge/Platform-Windows%20Server-blue)
![Technology](https://img.shields.io/badge/Technology-Active%20Directory-blue)
![Feature](https://img.shields.io/badge/Feature-Windows%20LAPS-purple)
![Focus](https://img.shields.io/badge/Focus-Local%20Admin%20Control-orange)
![Security](https://img.shields.io/badge/Security-Least%20Privilege-red)
![Validation](https://img.shields.io/badge/Validation-Password%20Retrieval-brightgreen)

---

## Overview

In this lab, I implemented Windows Local Administrator Password Solution, also known as Windows LAPS, in the Monroe Redstone Technology Group Active Directory environment.

This lab focused on controlling the local Administrator password for domain-joined workstations by backing up the password to Active Directory, delegating read access, applying policy through Group Policy, and validating that LAPS processed correctly on the client system.

This lab also included troubleshooting a schema extension issue caused by an Active Directory replication and DNS health problem before completing the LAPS deployment.

---

## Objectives

- Confirm the target workstation object is located in the correct Workstations OU
- Validate Windows LAPS availability on the domain controller
- Confirm required Windows LAPS PowerShell commands are available
- Create a pre-change checkpoint before extending the AD schema
- Verify Schema Admins, Enterprise Admins, and Domain Admins context before schema work
- Identify and troubleshoot a failed LAPS schema extension attempt
- Restore AD replication health before retrying schema validation
- Verify Windows LAPS schema attributes exist in Active Directory
- Grant computer self-permission for LAPS password updates
- Create a delegated LAPS password readers group
- Delegate LAPS password read permissions using a security group
- Create and link a Windows LAPS workstation baseline GPO
- Apply the Windows LAPS policy to the client workstation
- Invoke LAPS policy processing on the client
- Validate LAPS password metadata retrieval from Active Directory
- Create final checkpoints after successful validation

---

## Scope

This lab focuses on Windows LAPS deployment for domain-joined workstations in an on-premises Active Directory environment.

This lab includes:

- Windows LAPS readiness validation
- AD schema validation
- LAPS permission delegation
- Group-based password read access
- Workstation-focused LAPS policy configuration
- Client-side policy validation
- LAPS password metadata retrieval

This lab does not include:

- Microsoft Entra ID LAPS backup
- Intune policy deployment
- Cloud-only device management
- Privileged Access Management platforms
- Automated password retrieval workflows
- Production password disclosure procedures

---

## Lab Environment

| Component | Details |
|---|---|
| Organization | Monroe Redstone Technology Group |
| Domain | `mrtg.local` |
| Primary Domain Controller | `MRTG-DC01` |
| Additional Domain Controller | `MRTG-DC02` |
| Client VM | `MRTG-CLIENT-01` |
| Client Computer Object | `CLIENT01` |
| Target OU | `_MRTG/Computers/Workstations` |
| Management Tooling | Active Directory Users and Computers, Group Policy Management, PowerShell |
| LAPS Model | Windows LAPS with Active Directory backup |
| Hypervisor | Hyper-V |

---

## Architecture / Design

The MRTG environment uses a dedicated Workstations OU to apply endpoint-specific security controls. Windows LAPS was configured through a workstation baseline GPO linked to the Workstations OU.

The design separates three key responsibilities:

| Area | Design Decision |
|---|---|
| Password storage | Back up local Administrator password data to Active Directory |
| Password management scope | Apply LAPS only to workstation computer objects in the Workstations OU |
| Password read access | Delegate read permissions to a dedicated group instead of using one-off user permissions |
| Policy enforcement | Use Group Policy to apply LAPS settings to domain-joined workstations |
| Validation | Confirm policy application, LAPS processing, and password metadata retrieval |

This supports a cleaner IAM model because local administrator password control is no longer manual, shared, or undocumented.

---

## Technologies Used

- Active Directory Domain Services
- Active Directory Users and Computers
- Group Policy Management Console
- Group Policy Management Editor
- Windows LAPS
- Windows PowerShell
- Hyper-V
- Windows Server 2022
- Windows client workstation

---

## Security Model

Windows LAPS reduces risk by ensuring that local Administrator passwords are unique, managed, and retrievable only by authorized users.

This lab used the following control model:

| Control | Purpose |
|---|---|
| Workstations OU | Defines the target scope for LAPS management |
| Windows LAPS GPO | Applies LAPS configuration to workstation systems |
| Computer self-permission | Allows workstation objects to update their own LAPS password attributes |
| LAPS password readers group | Controls who can retrieve local administrator password data |
| AD schema attributes | Stores LAPS password and expiration metadata |
| Final validation | Confirms that the workstation processed policy and wrote password data |

---

## Key Accounts, Groups, and Objects

| Object | Purpose |
|---|---|
| `CLIENT01` | Target workstation computer object |
| `MRTG-GPO-Windows-LAPS-Workstation-Baseline` | GPO used to configure Windows LAPS settings |
| `MRTG-GRP-LAPS-Password-Readers` | Delegated group allowed to read LAPS password data |
| `Administrator` | Account added to password readers group for lab validation |
| `_MRTG/Computers/Workstations` | OU targeted for LAPS management |

---

## Implementation Steps

### Step 1 - Confirm Client Computer Object Placement

Confirmed that `CLIENT01` was located under the Workstations OU. This established the correct target scope before applying Windows LAPS policy.

![Step 1](screenshots/lab-17-01-client01-computer-object-in-workstations-ou.png)

---

### Step 2 - Validate Windows LAPS Availability

Checked the domain controller for Windows LAPS module availability and confirmed the operating system build information. The first validation showed that the expected LAPS module path was not detected.

![Step 2A](screenshots/lab-17-02a-windows-laps-module-not-detected.png)

Confirmed that the required update was installed and verified that Windows LAPS PowerShell commands were available.

![Step 2B](screenshots/lab-17-02b-kb5030216-installed-and-laps-commands-available.png)

---

### Step 3 - Create a Pre-LAPS Schema Extension Checkpoint

Created a Hyper-V checkpoint before making schema-related changes. This provided a rollback point before performing higher-risk directory changes.

![Step 3](screenshots/lab-17-03-pre-laps-schema-extension-checkpoint.png)

---

### Step 4 - Validate Schema Admin Context and Troubleshoot Schema Extension

Confirmed that the current administrative context had the required privileged group memberships for schema-related work.

![Step 4A](screenshots/lab-17-04a-schema-admin-membership-check.png)

Attempted the Windows LAPS schema extension and encountered an operation error. This was treated as a real troubleshooting point instead of being skipped.

![Step 4B](screenshots/lab-17-04b-laps-schema-extension-failed-operation-error.png)

Checked replication health and identified a replication or DNS-related issue involving domain controller communication.

![Step 4C](screenshots/lab-17-04c-replication-dns-failure-before-laps-schema-retry.png)

Restored replication health and confirmed that domain controller replication returned to a healthy state before continuing.

![Step 4D](screenshots/lab-17-04d-replication-health-restored-before-laps-schema-retry.png)

Verified that the Windows LAPS schema attributes were present in Active Directory, including the `ms-LAPS-*` attributes required for password storage and expiration metadata.

![Step 4E](screenshots/lab-17-04e-windows-laps-schema-extension-verified.png)

---

### Step 5 - Grant Computer Self-Permission on the Workstations OU

Granted the required LAPS computer self-permission on the Workstations OU. This allows managed workstation computer objects to update their own LAPS password attributes in Active Directory.

![Step 5](screenshots/lab-17-05-workstations-ou-laps-computer-self-permission-set.png)

---

### Step 6 - Create the LAPS Password Readers Group

Created the `MRTG-GRP-LAPS-Password-Readers` security group to control who can read LAPS password data.

![Step 6](screenshots/lab-17-06-laps-password-readers-group-created.png)

---

### Step 7 - Add an Authorized Reader to the LAPS Password Readers Group

Added the `Administrator` account to the LAPS password readers group for lab validation.

![Step 7](screenshots/lab-17-07-admin-added-to-laps-password-readers-group.png)

---

### Step 8 - Delegate LAPS Password Read Permission

Delegated LAPS password read permission on the Workstations OU to the `MRTG-GRP-LAPS-Password-Readers` group. This avoided direct user-based permission assignment and kept access controlled through group membership.

![Step 8](screenshots/lab-17-08-laps-read-password-permission-delegated.png)

---

### Step 9 - Link the Windows LAPS GPO to the Workstations OU

Linked the Windows LAPS workstation baseline GPO to the Workstations OU. This ensured that the policy would apply only to workstation computer objects in the intended OU scope.

![Step 9](screenshots/lab-17-09-windows-laps-gpo-linked-to-workstations-ou.png)

---

### Step 10 - Configure Windows LAPS Policy Settings

Configured Windows LAPS policy settings through Group Policy. The policy was configured to back up local administrator password data to Active Directory and apply password management controls to the workstation.

![Step 10](screenshots/lab-17-10-windows-laps-policy-settings-configured.png)

---

### Step 11 - Apply and Validate Group Policy on CLIENT01

Forced a Group Policy update on `CLIENT01` and used `gpresult` to confirm that the Windows LAPS workstation baseline GPO applied successfully.

![Step 11](screenshots/lab-17-11-client01-laps-gpo-applied-with-gpresult.png)

---

### Step 12 - Invoke Windows LAPS Policy Processing

Confirmed that Windows LAPS commands were available on the client and manually invoked LAPS policy processing. The processing completed successfully.

![Step 12](screenshots/lab-17-12-client01-laps-policy-processing-invoked.png)

---

### Step 13 - Retrieve LAPS Password Metadata

Retrieved Windows LAPS password metadata for `CLIENT01` from Active Directory. The validation confirmed that password data was being stored and that decryption status was successful.

The actual password was not documented in the README.

![Step 13](screenshots/lab-17-13-laps-password-retrieval-metadata.png)

---

### Step 14 - Create Final Post-Lab Checkpoints

Created a post-lab checkpoint for `MRTG-DC01` after validating Windows LAPS configuration.

![Step 14A](screenshots/lab-17-14a-dc01-post-lab17-checkpoint.png)

Created a post-lab checkpoint for `MRTG-CLIENT-01` after confirming the client processed the Windows LAPS policy.

![Step 14B](screenshots/lab-17-14b-client01-post-lab17-checkpoint.png)

---

## Validation / Verification

| Validation Item | Result |
|---|---|
| `CLIENT01` located in Workstations OU | Passed |
| Windows LAPS commands available after update validation | Passed |
| Pre-change checkpoint created before schema work | Passed |
| Schema Admin, Enterprise Admin, and Domain Admin context reviewed | Passed |
| Initial schema extension issue identified | Passed |
| Replication health issue identified and corrected | Passed |
| Windows LAPS schema attributes verified | Passed |
| Computer self-permission applied to Workstations OU | Passed |
| LAPS password readers group created | Passed |
| Authorized reader added to delegated group | Passed |
| LAPS read password permission delegated to group | Passed |
| Windows LAPS GPO linked to Workstations OU | Passed |
| Windows LAPS policy settings configured | Passed |
| GPO applied to `CLIENT01` | Passed |
| LAPS policy processing completed successfully | Passed |
| LAPS password metadata retrieved from AD | Passed |
| Final checkpoints created | Passed |

---

## Evidence Collected

| Evidence | Screenshot |
|---|---|
| Client computer object in Workstations OU | `screenshots/lab-17-01-client01-computer-object-in-workstations-ou.png` |
| Initial LAPS module check | `screenshots/lab-17-02a-windows-laps-module-not-detected.png` |
| KB and LAPS command validation | `screenshots/lab-17-02b-kb5030216-installed-and-laps-commands-available.png` |
| Pre-LAPS schema checkpoint | `screenshots/lab-17-03-pre-laps-schema-extension-checkpoint.png` |
| Schema Admin membership check | `screenshots/lab-17-04a-schema-admin-membership-check.png` |
| Failed schema extension attempt | `screenshots/lab-17-04b-laps-schema-extension-failed-operation-error.png` |
| Replication DNS failure before retry | `screenshots/lab-17-04c-replication-dns-failure-before-laps-schema-retry.png` |
| Replication health restored | `screenshots/lab-17-04d-replication-health-restored-before-laps-schema-retry.png` |
| LAPS schema attributes verified | `screenshots/lab-17-04e-windows-laps-schema-extension-verified.png` |
| LAPS computer self-permission applied | `screenshots/lab-17-05-workstations-ou-laps-computer-self-permission-set.png` |
| LAPS password readers group created | `screenshots/lab-17-06-laps-password-readers-group-created.png` |
| Admin added to LAPS password readers group | `screenshots/lab-17-07-admin-added-to-laps-password-readers-group.png` |
| LAPS read permission delegated | `screenshots/lab-17-08-laps-read-password-permission-delegated.png` |
| Windows LAPS GPO linked | `screenshots/lab-17-09-windows-laps-gpo-linked-to-workstations-ou.png` |
| Windows LAPS policy settings configured | `screenshots/lab-17-10-windows-laps-policy-settings-configured.png` |
| Client GPO application verified | `screenshots/lab-17-11-client01-laps-gpo-applied-with-gpresult.png` |
| LAPS policy processing invoked | `screenshots/lab-17-12-client01-laps-policy-processing-invoked.png` |
| LAPS password metadata retrieved | `screenshots/lab-17-13-laps-password-retrieval-metadata.png` |
| DC01 final checkpoint | `screenshots/lab-17-14a-dc01-post-lab17-checkpoint.png` |
| CLIENT01 final checkpoint | `screenshots/lab-17-14b-client01-post-lab17-checkpoint.png` |

---

## Troubleshooting Notes

During the schema extension process, the initial Windows LAPS schema update attempt failed with an operation error.

Rather than forcing the change forward, I validated domain controller health and discovered a replication or DNS-related issue involving domain controller communication.

The issue was corrected before continuing. After replication health was restored, the required `ms-LAPS-*` schema attributes were verified successfully.

This was an important part of the lab because schema changes should not be performed on top of unhealthy Active Directory replication.

---

## Security Lessons Learned

This lab reinforced several important IAM and endpoint security lessons:

- Local administrator passwords should not be shared, reused, or manually tracked
- LAPS helps reduce lateral movement risk by rotating and storing local admin passwords securely
- Password retrieval should be delegated through groups, not assigned directly to individual users
- OU targeting matters because policy scope determines which systems are managed
- Schema changes require healthy Active Directory replication
- Troubleshooting AD health is part of real identity administration work
- Password metadata can be documented, but actual passwords should not be exposed in public documentation

---

## What I Would Improve in Production

In a production environment, I would improve this implementation by:

- Using a dedicated privileged access group instead of the built-in Administrator account for password retrieval
- Requiring separate named admin accounts for LAPS password access
- Enforcing stronger auditing for LAPS password retrieval events
- Documenting a formal break-glass process for local administrator access
- Limiting LAPS password reader membership through approval-based access
- Reviewing delegated permissions with `Find-LapsADExtendedRights`
- Using tiered administrative workstations for privileged access
- Validating replication health before all schema or domain-wide changes
- Creating formal change records before extending the AD schema
- Monitoring LAPS-related activity through centralized logging or SIEM

---

## Outcome

Lab-17 successfully implemented Windows LAPS and local administrator password control in the MRTG Active Directory environment.

The lab confirmed that:

- `CLIENT01` was properly scoped inside the Workstations OU
- Windows LAPS functionality was available after update validation
- LAPS schema attributes were present in Active Directory
- Computer self-permission was applied to allow password updates
- Password read access was delegated through a dedicated group
- The Windows LAPS GPO applied successfully to the client workstation
- LAPS policy processing completed successfully
- LAPS password metadata could be retrieved from Active Directory

This lab strengthened the IAM series by adding endpoint-level local administrator control, which is a major security improvement over shared or unmanaged local admin passwords.

---

## Next Lab

[Lab-18 - Group-Based Access Control for File and Department Resources](../Lab-18-Group-Based-Access-Control-for-File-and-Department-Resources)

Lab-18 will build on prior IAM concepts by using Active Directory security groups to control access to shared resources through a scalable authorization model.
