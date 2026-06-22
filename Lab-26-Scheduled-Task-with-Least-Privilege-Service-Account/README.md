# Lab 26 - Scheduled Task with Least-Privilege Service Account

![Platform](https://img.shields.io/badge/Platform-Windows%20Server-blue)
![Technology](https://img.shields.io/badge/Technology-Active%20Directory-blue)
![Focus](https://img.shields.io/badge/Focus-Least%20Privilege-green)
![Tooling](https://img.shields.io/badge/Tooling-Task%20Scheduler-orange)
![Security](https://img.shields.io/badge/Security-Service%20Account%20Control-red)
![Validation](https://img.shields.io/badge/Validation-Completed-brightgreen)
![Documentation](https://img.shields.io/badge/Documentation-Audit%20Ready-purple)

---

## Overview

In this lab, I used the governed `svc-audit-review` service account to run a Windows scheduled task while maintaining least privilege.

This lab built on Lab 25, where the account was created, documented, and validated as a non-human identity.

The service account received only the folder permissions and Windows user right required to execute the task. It was not added to an administrative or privileged Active Directory group.

---

## Business Problem

MRTG needed to run routine audit-review automation without using a personal administrator account or granting unnecessary privileges to a service account.

Service accounts are often overprivileged because broad permissions are assigned simply to make an automated process work.

This creates risk because service accounts may:

- Run without direct user interaction
- Persist for long periods
- Access sensitive resources
- Store reusable credentials
- Receive limited review
- Become targets for credential theft
- Support lateral movement when overprivileged

This lab addressed that risk by granting the service account only the access required for one controlled scheduled task.

---

## Lab Summary

I created a dedicated `C:\MRTG-Audit` folder and a PowerShell audit-review marker script.

The `svc-audit-review` account received Modify permission on only the required folder. The `Log on as a batch job` user right was configured through Group Policy so the account could run a scheduled task while no user was logged on.

I created the `MRTG Audit Review Marker` task without enabling highest privileges. The task successfully generated a timestamped output file.

A final group membership review confirmed that the service account remained a member of only Domain Users.

---

## Objectives

- Create a pre-lab Hyper-V checkpoint
- Create a dedicated audit folder
- Create a PowerShell validation script
- Identify the scheduled task batch-logon requirement
- Configure `Log on as a batch job`
- Grant only the required NTFS folder permissions
- Create a scheduled task using the governed service account
- Avoid running the task with highest privileges
- Confirm the account remained non-privileged
- Execute the task and validate its output
- Create a post-lab Hyper-V checkpoint

---

## Scope

### Included

- Hyper-V checkpoints
- Audit folder creation
- PowerShell script creation
- NTFS permission assignment
- Group Policy user rights configuration
- Scheduled task configuration
- Service account group membership review
- Task execution
- Output validation
- Audit evidence collection

### Not Included

- Group Managed Service Account deployment
- Credential vault integration
- Automated password rotation
- Centralized script repository
- PowerShell code signing
- SIEM monitoring
- Production change approval
- Scheduled task alerting
- Service account logon restriction GPOs
- Enterprise job-scheduling platform integration

---

## Environment

| Component | Details |
|---|---|
| Organization | Monroe Redstone Technology Group |
| Domain | `mrtg.local` |
| Domain Controller | `MRTG-DC01` |
| Service Account | `svc-audit-review` |
| Target Folder | `C:\MRTG-Audit` |
| Script File | `C:\MRTG-Audit\audit-review-marker.ps1` |
| Output File | `C:\MRTG-Audit\audit-review-output.txt` |
| Scheduled Task | `MRTG Audit Review Marker` |
| Group Policy | `MRTG-GPO-Lab26-Service-Account-Batch-Logon` |
| Required User Right | `Log on as a batch job` |
| Management Tools | Task Scheduler, Group Policy Management, File Explorer |
| Hypervisor | Hyper-V |

---

## Scenario

MRTG needs a service account to execute a basic audit-review automation task.

The account must be able to run the task while no user is logged on and write the result to an approved folder.

It must not receive broad administrative access.

The least-privilege model used in this lab was:

```text
Governed Identity → Specific User Right → Resource-Level Permission → Non-Elevated Task → Output Validation
```

---

## Access Model

| Requirement | Control |
|---|---|
| Execute a scheduled task while logged off | `Log on as a batch job` |
| Read the PowerShell script | Read and execute permission on `C:\MRTG-Audit` |
| Create and update the output file | Modify permission on `C:\MRTG-Audit` |
| Avoid unnecessary elevation | Highest privileges not enabled |
| Avoid broad domain access | Domain Users membership only |
| Preserve execution evidence | Timestamped output file |
| Support rollback | Pre-lab and post-lab checkpoints |

---

## Implementation Steps

### Step 1 - Created Pre-Lab Checkpoint

A Hyper-V checkpoint was created before configuring the scheduled task.

Checkpoint name:

```text
MRTG-DC01_Pre-Lab26-Least-Privilege-Scheduled-Task
```

This preserved the domain controller state before modifying folder permissions, Group Policy, or Task Scheduler.

![Pre-Lab Checkpoint](screenshots/lab-26-01-pre-lab-checkpoint.png)

---

### Step 2 - Created the MRTG Audit Folder

A dedicated folder was created on `MRTG-DC01`.

Folder path:

```text
C:\MRTG-Audit
```

The folder provided a controlled location for the PowerShell script and its output.

![MRTG Audit Folder Created](screenshots/lab-26-02-mrtg-audit-folder-created.png)

---

### Step 3 - Created the Audit Review Marker Script

A PowerShell script was created in the audit folder.

Script path:

```text
C:\MRTG-Audit\audit-review-marker.ps1
```

The script was designed to:

- Execute through Task Scheduler
- Write a timestamped audit-review marker
- Create an output file
- Provide evidence that the service account could complete the approved task

Expected output path:

```text
C:\MRTG-Audit\audit-review-output.txt
```

![Audit Review Marker Script Created](screenshots/lab-26-03-audit-review-marker-script-created.png)

---

### Step 4 - Configured the Batch Logon Right Through Group Policy

The service account required the `Log on as a batch job` user right to run the scheduled task while no user was logged on.

Group Policy:

```text
MRTG-GPO-Lab26-Service-Account-Batch-Logon
```

Policy path:

```text
Computer Configuration
└── Policies
    └── Windows Settings
        └── Security Settings
            └── Local Policies
                └── User Rights Assignment
                    └── Log on as a batch job
```

Assigned identity:

```text
mrtg\svc-audit-review
```

This granted the specific Windows user right required by Task Scheduler without placing the account in an administrative group.

![Batch Logon Right GPO Configured](screenshots/lab-26-04-batch-logon-right-gpo-configured.png)

---

### Step 5 - Documented the Batch Logon Warning

Task Scheduler displayed a warning that the selected account required `Log on as a batch job`.

Observed warning:

```text
This task requires that the user account specified has Log on as batch job rights.
```

The warning confirmed the exact user right required for the task.

The issue was addressed through the dedicated Group Policy setting instead of adding the account to a privileged group.

![Batch Logon Right Warning](screenshots/lab-26-05-batch-logon-right-warning.png)

---

### Step 6 - Assigned Folder Permissions

The `svc-audit-review` account was granted Modify access to:

```text
C:\MRTG-Audit
```

Validated permissions included:

- Modify
- Read and execute
- List folder contents
- Read
- Write

Full Control was not granted.

This allowed the service account to read the script and create or update the output file without receiving broader system permissions.

![Folder Permissions for Service Account](screenshots/lab-26-06-folder-permissions-service-account.png)

---

### Step 7 - Configured the Scheduled Task

A scheduled task named `MRTG Audit Review Marker` was created.

Task configuration:

| Setting | Value |
|---|---|
| Task Name | `MRTG Audit Review Marker` |
| Run As Account | `svc-audit-review` |
| Run Whether User Is Logged On or Not | Enabled |
| Do Not Store Password | Disabled |
| Run With Highest Privileges | Disabled |
| Script | `C:\MRTG-Audit\audit-review-marker.ps1` |

Task description:

```text
Runs a basic audit-review marker script using a least-privilege service account.
```

The task was intentionally configured without highest privileges.

![Scheduled Task Service Account](screenshots/lab-26-07-scheduled-task-service-account.png)

---

### Step 8 - Validated Service Account Group Membership

The service account was reviewed again after the scheduled task was configured.

Validated membership:

```text
Domain Users
```

The account was not added to:

```text
Domain Admins
Enterprise Admins
Schema Admins
Administrators
Account Operators
Server Operators
Backup Operators
```

This confirmed that task functionality was achieved without privileged Active Directory group membership.

![Service Account Group Membership Validation](screenshots/lab-26-08-service-account-group-membership-validation.png)

---

### Step 9 - Validated Task Output

The scheduled task was executed to validate the complete workflow.

The task successfully created:

```text
C:\MRTG-Audit\audit-review-output.txt
```

Validated output:

```text
Audit review task executed by service account at 2026-05-26 11:01:05
```

This confirmed that the service account could:

- Run the scheduled task
- Read the approved script
- Write to the approved folder
- Generate execution evidence
- Complete the task without administrative group membership

![Task Output Validation](screenshots/lab-26-09-task-output-validation.png)

---

### Step 10 - Created Post-Lab Checkpoint

A post-lab checkpoint was created after validating the task.

Checkpoint name:

```text
MRTG-DC01_Post-Lab26-Least-Privilege-Scheduled-Task-Validated
```

This preserved the validated Lab 26 configuration before beginning Lab 27.

![Post-Lab Checkpoint](screenshots/lab-26-10-post-lab-checkpoint.png)

---

## Least-Privilege Analysis

The task required three distinct capabilities:

| Capability | Permission Source |
|---|---|
| Start as a background scheduled task | `Log on as a batch job` |
| Read and execute the script | NTFS permissions on `C:\MRTG-Audit` |
| Create and update the output file | Modify permission on `C:\MRTG-Audit` |

The account did not require:

- Domain Admins membership
- Local Administrators membership
- Full Control on the folder
- Highest Task Scheduler privileges
- Interactive logon
- Broad access to unrelated folders

This demonstrates that least privilege requires reviewing multiple control layers rather than checking only Active Directory group membership.

---

## Risk Addressed

Overprivileged service accounts increase the impact of credential compromise and automation misuse.

This lab addressed risks including:

- Scheduled tasks running under personal administrator accounts
- Service accounts receiving unnecessary administrative access
- Broad file system permissions
- Uncontrolled user rights assignments
- Tasks running with highest privileges unnecessarily
- Missing evidence of task execution
- Failure to validate access after implementation
- Long-lived non-human identities with unclear permissions

---

## Control Mapping

| Control Area | Lab Implementation |
|---|---|
| Non-human identity governance | Used the governed account created in Lab 25 |
| Least privilege | Granted only required user rights and NTFS access |
| Privileged access control | Kept the account out of privileged groups |
| User rights assignment | Configured `Log on as a batch job` through Group Policy |
| Resource authorization | Granted Modify access only to the task folder |
| Execution control | Disabled highest-privilege execution |
| Access validation | Rechecked group membership after configuration |
| Audit readiness | Generated timestamped execution evidence |
| Change protection | Created pre-lab and post-lab checkpoints |

---

## Validation Summary

| Test | Expected Result | Actual Result | Status |
|---|---|---|---|
| Pre-lab checkpoint created | Rollback point exists | Checkpoint created | Passed |
| Audit folder created | `C:\MRTG-Audit` exists | Folder created | Passed |
| PowerShell script created | Marker script exists | Script created | Passed |
| Batch logon requirement identified | Required user right documented | Warning captured | Passed |
| Batch logon right configured | Service account assigned required right | GPO configured | Passed |
| Folder permission assigned | Account can modify the task folder | Modify granted | Passed |
| Full Control avoided | Account does not have Full Control | Full Control not granted | Passed |
| Scheduled task created | Task uses `svc-audit-review` | Task created | Passed |
| Highest privileges avoided | Elevated execution disabled | Setting remained disabled | Passed |
| Group membership reviewed | Account remains non-privileged | Domain Users only | Passed |
| Task executed | Script runs successfully | Execution completed | Passed |
| Output created | Timestamped output file exists | Output validated | Passed |
| Post-lab checkpoint created | Validated rollback point exists | Checkpoint created | Passed |

---

## Evidence Collected

| Evidence | Screenshot |
|---|---|
| Pre-lab checkpoint | `screenshots/lab-26-01-pre-lab-checkpoint.png` |
| Audit folder | `screenshots/lab-26-02-mrtg-audit-folder-created.png` |
| PowerShell marker script | `screenshots/lab-26-03-audit-review-marker-script-created.png` |
| Batch logon GPO | `screenshots/lab-26-04-batch-logon-right-gpo-configured.png` |
| Batch logon warning | `screenshots/lab-26-05-batch-logon-right-warning.png` |
| Service account folder permissions | `screenshots/lab-26-06-folder-permissions-service-account.png` |
| Scheduled task configuration | `screenshots/lab-26-07-scheduled-task-service-account.png` |
| Service account group membership | `screenshots/lab-26-08-service-account-group-membership-validation.png` |
| Successful task output | `screenshots/lab-26-09-task-output-validation.png` |
| Post-lab checkpoint | `screenshots/lab-26-10-post-lab-checkpoint.png` |

---

## Troubleshooting Notes

Task Scheduler initially reported that the service account required `Log on as a batch job`.

The issue was resolved by configuring the specific user right through Group Policy.

The account was not added to a privileged group, and the task was not configured to run with highest privileges.

This demonstrates an important troubleshooting principle:

```text
Identify the missing permission → Grant the narrow requirement → Retest → Confirm no extra privilege was introduced
```

---

## Security Considerations

The scheduled task required the service account password to be stored by Task Scheduler because it was configured to run while the account was logged off.

In production, this creates credential-management considerations.

Stronger controls would include:

- Group Managed Service Accounts where supported
- Automated credential rotation
- Credential vault integration
- Deny interactive logon
- Deny Remote Desktop Services logon
- Script signing
- Restricted PowerShell execution
- Task history monitoring
- Alerts for task changes
- Alerts for service account logons
- Restricted access to task definitions
- Periodic folder permission reviews

---

## Real-World Relevance

Scheduled tasks and service accounts are commonly used for:

- Report generation
- File processing
- Backup operations
- System maintenance
- Monitoring
- Log collection
- Data synchronization
- Identity lifecycle automation
- Compliance evidence generation

In enterprise and government-regulated environments, these tasks must not rely on personal administrator accounts.

A properly governed automation identity should have:

- A documented owner
- A defined purpose
- Narrow resource access
- Required user rights only
- No unnecessary administrative membership
- Protected credentials
- Monitored activity
- Retained execution evidence
- A recurring review schedule

---

## What I Would Do Differently in Production

In a production environment, I would implement:

- A formal service account request and approval workflow
- Business and technical ownership
- Group Managed Service Accounts where supported
- Credential vault integration
- Automated password rotation
- Deny interactive logon controls
- Deny Remote Desktop logon controls
- A controlled script repository
- Digitally signed PowerShell scripts
- Restricted script modification permissions
- Task Scheduler operational logging
- Centralized service account monitoring
- Alerts for task creation or modification
- Alerts for abnormal service account activity
- Periodic NTFS permission reviews
- Group Policy change control
- Documented task dependencies
- A formal task retirement process

---

## Lessons Learned

- Making a service account functional should not require administrator access
- User rights assignments are separate from group membership
- Scheduled tasks require `Log on as a batch job`
- NTFS permissions should be limited to the required resource
- Modify access can be sufficient without Full Control
- Highest privileges should remain disabled unless required
- Service account membership should be revalidated after implementation
- Task output provides useful operational evidence
- Troubleshooting should identify the narrow permission requirement
- Least privilege must be validated across identity, file system, policy, and task settings

---

## Skills Demonstrated

- Service account governance
- Least-privilege design
- Windows Task Scheduler configuration
- Group Policy user rights assignment
- NTFS permission management
- PowerShell automation
- Scheduled task troubleshooting
- Non-human identity validation
- Privileged group review
- Task output validation
- Audit evidence collection
- Hyper-V checkpoint management
- Production security analysis

---

## Outcome

Lab 26 successfully demonstrated how to use a governed service account for a scheduled task while maintaining least privilege.

The lab demonstrated:

- Controlled use of a non-human identity
- Resource-level NTFS permission assignment
- Group Policy-based batch logon rights
- Non-elevated scheduled task execution
- Successful automation output
- Continued non-privileged group membership
- Audit-ready validation evidence
- Pre-lab and post-lab rollback points

The service account completed its defined function without receiving broad administrative access.

---

## Next Lab

[Lab 27 - BitLocker and Endpoint Encryption Recovery](../Lab-27-BitLocker-and-Endpoint-Encryption-Recovery/)

Lab 27 will focus on BitLocker deployment, endpoint encryption, recovery key handling, recovery validation, and layered endpoint security controls.
