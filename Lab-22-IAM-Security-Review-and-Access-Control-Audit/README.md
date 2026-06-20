# Lab 22 - IAM Security Review and Access Control Audit

![Platform](https://img.shields.io/badge/Platform-Windows%20Server-blue)
![Technology](https://img.shields.io/badge/Technology-Active%20Directory-blue)
![Tooling](https://img.shields.io/badge/Tooling-PowerShell-purple)
![Focus](https://img.shields.io/badge/Focus-IAM%20Security%20Review-orange)
![Security](https://img.shields.io/badge/Security-Access%20Control%20Audit-red)
![Validation](https://img.shields.io/badge/Validation-Audit%20Evidence-brightgreen)

---

## Overview

In this lab, I performed an IAM security review and access control audit in the Monroe Redstone Technology Group Active Directory environment.

This lab focused on reviewing privileged access, Domain Admins membership, disabled accounts, department-based access groups, delegated administrative groups, LAPS-related groups, AD CS-related groups, and stale or unusual accounts.

The goal was to move beyond configuration and demonstrate evidence-based IAM auditing using PowerShell and Active Directory administrative tools.

---

## Business Problem

MRTG needed a repeatable way to verify that identity access remained appropriate after multiple rounds of directory configuration, delegation, endpoint administration, certificate services deployment, and identity lifecycle activity.

Configuration alone does not prove that access is still correct. Privileged groups can accumulate unnecessary members, disabled users can retain group memberships, delegated roles can expand beyond their intended scope, and service or administrative accounts can remain active without clear ownership.

This lab addressed that problem by reviewing high-risk identity objects, documenting the observed access state, exporting structured evidence, and creating a written audit summary for operational and compliance review.

---

## Lab Summary

I began by creating a pre-lab Hyper-V checkpoint and a dedicated folder structure for evidence, output files, reports, and scripts. Before auditing access, I validated domain controller services and replication to ensure the review was performed against a healthy directory.

I then reviewed privileged groups, Domain Admins, disabled accounts, department groups, delegated administration groups, LAPS and security groups, AD CS-related groups, and the broader user account inventory.

The review confirmed expected administrative separation, identified the current members of sensitive groups, and verified that the Lab 20 mover and leaver results remained reflected in Active Directory.

Finally, I exported eight CSV evidence files, created an IAM security review summary, and captured a post-lab checkpoint after validation.

---

## Objectives

- Create pre-lab and post-lab Hyper-V checkpoints
- Create a dedicated Lab 22 audit workspace
- Validate domain controller and replication health before the review
- Review privileged and Domain Admins membership
- Review disabled and potentially unusual accounts
- Review department-based access groups
- Review delegated administrative access
- Review LAPS password-reader and security-related groups
- Review AD CS and certificate-related groups
- Export structured audit evidence to CSV
- Create a written IAM security review summary

---

## Scope

### Included

- Hyper-V checkpoint creation
- Lab folder structure creation
- Domain health validation
- Replication health validation
- Privileged group review
- Domain Admins review
- Disabled account review
- Department group review
- Delegated admin group review
- LAPS and security group review
- AD CS-related group review
- User account status review
- CSV evidence exports
- IAM security review summary

### Not Included

- Changing group membership or account status
- Approving or certifying access for a business owner
- Reviewing file system, application, or cloud permissions
- Performing an exhaustive OU delegation ACL review
- Reviewing certificate template permissions
- Correlating access with change tickets or HR records
- Configuring recurring access certification campaigns
- Integrating evidence with a GRC or SIEM platform

---

## Environment

| Component | Details |
|---|---|
| Organization | Monroe Redstone Technology Group |
| Domain | `mrtg.local` |
| Primary Domain Controller | `MRTG-DC01` |
| Additional Domain Controller | `MRTG-DC02` |
| Site | `MRTG-HQ-Site` |
| Lab Root Path | `C:\MRTG-Labs\Lab-22-IAM-Security-Review-and-Access-Control-Audit` |
| Output Path | `C:\MRTG-Labs\Lab-22-IAM-Security-Review-and-Access-Control-Audit\output` |
| Reports Path | `C:\MRTG-Labs\Lab-22-IAM-Security-Review-and-Access-Control-Audit\reports` |
| Scripts Path | `C:\MRTG-Labs\Lab-22-IAM-Security-Review-and-Access-Control-Audit\scripts` |
| Evidence Path | `C:\MRTG-Labs\Lab-22-IAM-Security-Review-and-Access-Control-Audit\evidence` |
| Tools | PowerShell, Active Directory Module, DCDIAG, REPADMIN |
| Hypervisor | Hyper-V |

---

## Scenario

Monroe Redstone Technology Group has built an enterprise-style Active Directory environment with organizational units, users, groups, Group Policy, delegation, LAPS, AD CS, and identity lifecycle automation.

After building and backing up the environment, the next step was to review it from an IAM security and access control perspective.

The security review model used in this lab was:

```text
Validate Health → Review Privileged Access → Review Standard Access → Review Disabled Accounts → Export Evidence → Create Summary
```

This lab did not make access changes. It focused on reviewing the current identity state and producing audit evidence.

---

## Audit Design

| Audit Area | Purpose |
|---|---|
| Domain Health | Confirm the directory was healthy before auditing access |
| Privileged Groups | Identify users or groups with elevated access |
| Domain Admins | Review the most sensitive daily privileged group |
| Disabled Accounts | Confirm built-in and offboarded accounts remained disabled |
| Department Groups | Review business-aligned access groups |
| Delegated Admin Groups | Review least-privilege administrative delegation |
| LAPS and Security Groups | Review endpoint and password-reader access |
| AD CS Groups | Review certificate and PKI-related access |
| User Accounts | Identify disabled, service, admin, stale, or unusual accounts |
| Audit Exports | Preserve review evidence in CSV format |
| Summary Report | Document findings and conclusions |

---

## Implementation Steps

### Step 1 - Created Pre-Lab Checkpoint

A Hyper-V checkpoint was created before beginning the IAM security review.

Checkpoint name:

```text
MRTG-DC01_Pre-Lab-22-IAM-Security-Review-and-Access-Control-Audit
```

![Pre-Lab 22 Checkpoint](screenshots/lab-22-01-pre-lab-22-checkpoint.png)

---

### Step 2 - Created Lab 22 Folder Structure

A dedicated Lab 22 workspace was created on `MRTG-DC01`.

Folder structure:

```text
C:\MRTG-Labs\Lab-22-IAM-Security-Review-and-Access-Control-Audit
├── evidence
├── output
├── reports
└── scripts
```

![Lab 22 Folder Structure Created](screenshots/lab-22-02-folder-structure-created.png)

---

## Pre-Audit Health Validation

### Step 3 - Validated Domain Health Before Audit

Domain controller health and replication were validated before reviewing access.

Commands used:

```powershell
dcdiag /s:MRTG-DC01 /test:Advertising /test:Services /test:Replications /test:KnowsOfRoleHolders
repadmin /replsummary
```

Validation confirmed:

```text
DCDIAG passed:
- Connectivity
- Advertising
- KnowsOfRoleHolders
- Replications
- Services

REPADMIN passed:
- MRTG-DC01 failures: 0 / 5
- MRTG-DC02 failures: 0 / 5
```

![Domain Health Pre-Audit Validated](screenshots/lab-22-03-domain-health-pre-audit-validated.png)

---

## Privileged Access Review

### Step 4 - Reviewed Privileged Groups

Privileged groups were reviewed to identify accounts or nested groups with elevated access.

Groups reviewed:

```text
Domain Admins
Enterprise Admins
Schema Admins
Administrators
Account Operators
Server Operators
Backup Operators
Print Operators
```

Command used:

```powershell
$PrivilegedGroups = @(
    "Domain Admins",
    "Enterprise Admins",
    "Schema Admins",
    "Administrators",
    "Account Operators",
    "Server Operators",
    "Backup Operators",
    "Print Operators"
)

$PrivilegedGroups | ForEach-Object {
    $GroupName = $_

    Get-ADGroupMember -Identity $GroupName -Recursive | ForEach-Object {
        [PSCustomObject]@{
            GroupName      = $GroupName
            MemberName     = $_.Name
            SamAccountName = $_.SamAccountName
            ObjectClass    = $_.ObjectClass
        }
    }
} | Format-Table -AutoSize
```

Observed privileged membership:

| Group | Member |
|---|---|
| Domain Admins | Administrator |
| Enterprise Admins | Administrator |
| Schema Admins | Administrator |
| Administrators | Administrator |

![Privileged Groups Reviewed](screenshots/lab-22-04-privileged-groups-reviewed.png)

---

### Step 5 - Reviewed Domain Admins Membership

Domain Admins membership was reviewed separately because it is one of the most sensitive groups in Active Directory.

Command used:

```powershell
Get-ADGroupMember "Domain Admins" -Recursive |
Select-Object Name,SamAccountName,ObjectClass |
Format-Table -AutoSize
```

Validation confirmed:

```text
Domain Admins → Administrator
```

No unexpected standard user accounts were observed in Domain Admins.

![Domain Admins Membership Reviewed](screenshots/lab-22-05-domain-admins-membership-reviewed.png)

---

## Account Status Review

### Step 6 - Reviewed Disabled Accounts

Disabled accounts were reviewed to identify expected built-in accounts and previously offboarded users.

Command used:

```powershell
Get-ADUser -Filter 'Enabled -eq $false' -Properties Department,Title,Description,LastLogonDate |
Select-Object Name,SamAccountName,Department,Title,Description,LastLogonDate |
Format-Table -AutoSize
```

Disabled accounts reviewed:

| Account | Purpose or Finding |
|---|---|
| Guest | Built-in guest account |
| krbtgt | Built-in Kerberos Key Distribution Center account |
| maya.reed | Disabled during the Lab 20 leaver workflow |

The `maya.reed` account remained disabled following the Lab 20 offboarding workflow.

![Disabled Accounts Reviewed](screenshots/lab-22-06-disabled-accounts-reviewed.png)

---

## Access Group Review

### Step 7 - Reviewed Department Groups

Department-based access groups were reviewed to confirm business access group structure and membership.

Commands used:

```powershell
Get-ADGroup -Filter 'Name -like "GG_*_Users"' -Properties Description |
Select-Object Name,GroupScope,GroupCategory,Description,DistinguishedName |
Format-Table -AutoSize
```

```powershell
Get-ADGroup -Filter 'Name -like "GG_*_Users"' | ForEach-Object {
    $Group = $_.Name

    Get-ADGroupMember $Group | ForEach-Object {
        [PSCustomObject]@{
            GroupName      = $Group
            MemberName     = $_.Name
            SamAccountName = $_.SamAccountName
            ObjectClass    = $_.ObjectClass
        }
    }
} | Format-Table -AutoSize
```

Important findings:

```text
ethan.walker was assigned to GG_Security_Users.
maya.reed was not listed in GG_Security_Users.
```

This confirmed that the Lab 20 mover and leaver changes remained reflected in the current access state.

![Department Groups Reviewed](screenshots/lab-22-07-department-groups-reviewed.png)

---

### Step 8 - Reviewed Delegated Admin Groups

Administrative and delegation-related groups were reviewed to identify delegated access.

Commands used:

```powershell
Get-ADGroup -Filter 'Name -like "*Admin*" -or Name -like "*Delegat*" -or Name -like "*Tier*"' -Properties Description |
Select-Object Name,GroupScope,GroupCategory,Description,DistinguishedName |
Format-Table -AutoSize
```

```powershell
Get-ADGroup -Filter 'Name -like "*Admin*" -or Name -like "*Delegat*" -or Name -like "*Tier*"' | ForEach-Object {
    $Group = $_.Name

    Get-ADGroupMember $Group -ErrorAction SilentlyContinue | ForEach-Object {
        [PSCustomObject]@{
            GroupName      = $Group
            MemberName     = $_.Name
            SamAccountName = $_.SamAccountName
            ObjectClass    = $_.ObjectClass
        }
    }
} | Format-Table -AutoSize
```

Observed delegated access:

| Group | Member |
|---|---|
| `GG_IT_HelpDesk_Admins` | `john.smith.admin` |
| `GG_PSO_Privileged_Admins` | `john.smith.admin` |
| `MRTG-GRP-Helpdesk-Password-Reset-Delegated` | `adm.hd-reset01` |

This supports least privilege by separating delegated administration from broad Domain Admins membership.

![Delegated Admin Groups Reviewed](screenshots/lab-22-08-delegated-admin-groups-reviewed.png)

---

### Step 9 - Reviewed LAPS and Security Groups

LAPS and security-related groups were reviewed to identify users with sensitive endpoint or password-reader access.

Commands used:

```powershell
Get-ADGroup -Filter 'Name -like "*LAPS*" -or Name -like "*Security*" -or Name -like "*Privileged*"' -Properties Description |
Select-Object Name,GroupScope,GroupCategory,Description,DistinguishedName |
Format-Table -AutoSize
```

```powershell
Get-ADGroup -Filter 'Name -like "*LAPS*" -or Name -like "*Security*" -or Name -like "*Privileged*"' | ForEach-Object {
    $Group = $_.Name

    Get-ADGroupMember $Group -ErrorAction SilentlyContinue | ForEach-Object {
        [PSCustomObject]@{
            GroupName      = $Group
            MemberName     = $_.Name
            SamAccountName = $_.SamAccountName
            ObjectClass    = $_.ObjectClass
        }
    }
} | Format-Table -AutoSize
```

Observed membership:

| Group | Members |
|---|---|
| `GG_Security_Users` | Alex Rivera, Ethan Walker |
| `GG_PSO_Privileged_Admins` | `john.smith.admin` |
| `MRTG-GRP-LAPS-Password-Readers` | Administrator |

![LAPS and Security Groups Reviewed](screenshots/lab-22-09-laps-and-security-groups-reviewed.png)

---

### Step 10 - Reviewed AD CS-Related Groups

Certificate and PKI-related groups were reviewed after the AD CS deployment completed in Lab 19.

Commands used:

```powershell
Get-ADGroup -Filter 'Name -like "*Cert*" -or Name -like "*CA*" -or Name -like "*PKI*"' -Properties Description |
Select-Object Name,GroupScope,GroupCategory,Description,DistinguishedName |
Format-Table -AutoSize
```

```powershell
Get-ADGroup -Filter 'Name -like "*Cert*" -or Name -like "*CA*" -or Name -like "*PKI*"' | ForEach-Object {
    $Group = $_.Name

    Get-ADGroupMember $Group -ErrorAction SilentlyContinue | ForEach-Object {
        [PSCustomObject]@{
            GroupName      = $Group
            MemberName     = $_.Name
            SamAccountName = $_.SamAccountName
            ObjectClass    = $_.ObjectClass
        }
    }
} | Format-Table -AutoSize
```

Observed certificate-related groups included:

```text
Certificate Service DCOM Access
Cert Publishers
```

Key finding:

```text
Cert Publishers → MRTG-DC01
```

This aligns with `MRTG-DC01` hosting the AD CS role from Lab 19.

![AD CS-Related Groups Reviewed](screenshots/lab-22-10-adcs-related-groups-reviewed.png)

---

## User Account Review

### Step 11 - Reviewed Stale or Unusual Accounts

User accounts were reviewed for status, department and title fields, logon activity, password timestamps, and account purpose indicators.

Command used:

```powershell
Get-ADUser -Filter * -Properties Enabled,LastLogonDate,PasswordLastSet,Department,Title,Description |
Select-Object Name,SamAccountName,Enabled,Department,Title,LastLogonDate,PasswordLastSet,Description |
Sort-Object LastLogonDate |
Format-Table -AutoSize
```

Account categories identified:

```text
Disabled accounts:
- krbtgt
- Guest
- maya.reed

Service accounts:
- svc_backup
- svc_appdeploy

Administrative accounts:
- alex.rivera.admin
- john.smith.admin
- adm.hd-reset01

Lab-created users:
- ava.brooks
- noah.bennett
- ethan.walker
- sophia.carter
```

This review identified accounts requiring different governance controls. It did not independently prove that an account was stale or unauthorized.

![Stale or Unusual Accounts Reviewed](screenshots/lab-22-11-stale-or-unusual-accounts-reviewed.png)

---

## Audit Evidence

### Step 12 - Created Audit Exports

Audit evidence was exported to CSV files.

Exported files:

```text
adcs-groups-review.csv
delegated-admin-groups-review.csv
department-groups-review.csv
disabled-accounts-review.csv
domain-admins-review.csv
laps-security-groups-review.csv
privileged-groups-review.csv
user-account-review.csv
```

![Audit Exports Created](screenshots/lab-22-12-audit-exports-created.png)

---

### Step 13 - Created IAM Security Review Summary

An IAM security review summary was created to document the audit areas, findings, evidence, and conclusion.

Report file:

```text
C:\MRTG-Labs\Lab-22-IAM-Security-Review-and-Access-Control-Audit\reports\MRTG-IAM-Security-Review-Summary.md
```

The report documented:

- Domain health validation
- Privileged access findings
- Disabled account review
- Department group access review
- Delegated administration review
- LAPS and security group review
- AD CS group review
- Audit evidence created
- IAM security review conclusion

![IAM Security Review Summary Created](screenshots/lab-22-13-iam-security-review-summary-created.png)

---

### Step 14 - Created Post-Lab Checkpoint

A post-lab checkpoint was created after completing and validating the IAM security review.

Checkpoint name:

```text
MRTG-DC01_Post-Lab-22-IAM-Security-Review-and-Access-Control-Audit-Validated
```

![Post-Lab 22 Checkpoint](screenshots/lab-22-14-post-lab-22-checkpoint.png)

---

## Key Audit Findings

| Review Area | Finding | Assessment |
|---|---|---|
| Domain health | `dcdiag` passed and replication showed `0 / 5` failures for both domain controllers | Healthy audit baseline |
| Domain Admins | Only the built-in `Administrator` account was returned | No unexpected standard users observed |
| Disabled accounts | `Guest`, `krbtgt`, and `maya.reed` were disabled | Expected built-in and leaver states observed |
| Department access | `ethan.walker` appeared in `GG_Security_Users`; `maya.reed` did not | Prior mover and leaver changes remained visible |
| Delegated administration | Help desk access used dedicated groups and admin accounts | Supports separation from Domain Admins |
| LAPS readers | `Administrator` belonged to `MRTG-GRP-LAPS-Password-Readers` | Privileged access identified for periodic review |
| AD CS groups | `MRTG-DC01` appeared in `Cert Publishers` | Consistent with the Lab 19 CA deployment |
| Account inventory | Service, admin, disabled, and standard accounts were distinguishable | Ownership and inactivity require policy-based review |

No access was remediated during this lab. The findings document the observed state and would require validation against approved access records before production changes.

---

## Validation Summary

| Test | Expected Result | Actual Result | Status |
|---|---|---|---|
| Pre-lab checkpoint created | Checkpoint exists before changes | Pre-lab checkpoint created | Passed |
| Lab folder structure created | Required folders exist | Folder structure validated | Passed |
| Domain health validated | DCDIAG and replication checks pass | Health checks passed | Passed |
| Privileged groups reviewed | Elevated groups reviewed | Privileged memberships identified | Passed |
| Domain Admins reviewed | Membership identified | Administrator only | Passed |
| Disabled accounts reviewed | Disabled accounts listed | Guest, krbtgt, and maya.reed reviewed | Passed |
| Department groups reviewed | Groups and members reviewed | Groups and members listed | Passed |
| Delegated admin groups reviewed | Delegated groups identified | Delegated members reviewed | Passed |
| LAPS and security groups reviewed | Sensitive groups reviewed | Groups and members listed | Passed |
| AD CS groups reviewed | Certificate groups reviewed | Related groups and members reviewed | Passed |
| User accounts reviewed | Accounts reviewed for status and purpose | Account categories identified | Passed |
| Audit exports created | CSV evidence created | Eight CSV files exported | Passed |
| Security summary created | Markdown summary created | Report created | Passed |
| Post-lab checkpoint created | Checkpoint exists after validation | Post-lab checkpoint created | Passed |

---

## Evidence Collected

| Evidence | Screenshot |
|---|---|
| Pre-lab checkpoint | `screenshots/lab-22-01-pre-lab-22-checkpoint.png` |
| Lab folder structure | `screenshots/lab-22-02-folder-structure-created.png` |
| Domain health and replication validation | `screenshots/lab-22-03-domain-health-pre-audit-validated.png` |
| Privileged group review | `screenshots/lab-22-04-privileged-groups-reviewed.png` |
| Domain Admins review | `screenshots/lab-22-05-domain-admins-membership-reviewed.png` |
| Disabled account review | `screenshots/lab-22-06-disabled-accounts-reviewed.png` |
| Department group review | `screenshots/lab-22-07-department-groups-reviewed.png` |
| Delegated admin group review | `screenshots/lab-22-08-delegated-admin-groups-reviewed.png` |
| LAPS and security group review | `screenshots/lab-22-09-laps-and-security-groups-reviewed.png` |
| AD CS-related group review | `screenshots/lab-22-10-adcs-related-groups-reviewed.png` |
| Stale or unusual account review | `screenshots/lab-22-11-stale-or-unusual-accounts-reviewed.png` |
| CSV audit exports | `screenshots/lab-22-12-audit-exports-created.png` |
| IAM security review summary | `screenshots/lab-22-13-iam-security-review-summary-created.png` |
| Post-lab checkpoint | `screenshots/lab-22-14-post-lab-22-checkpoint.png` |

---

## Troubleshooting Notes

No major technical failures occurred during the audit.

The main challenge was distinguishing observed account state from proof that access was approved or appropriate. Active Directory can show group membership and account status, but it cannot independently confirm business justification, ownership, or current authorization.

A production review would compare the exported evidence against:

- Approved access requests
- HR employment records
- Account ownership records
- Group owner certifications
- Change tickets
- Privileged access policies
- Inactivity thresholds

---

## Security Concepts Reinforced

- Identity governance
- Access certification
- Privileged access review
- Least privilege
- Administrative account separation
- Disabled account governance
- Group-based access control
- Delegated administration
- LAPS password-reader governance
- Certificate services access review
- Service account governance
- Evidence-based auditing
- Audit trail preservation
- Separation of review and remediation

---

## Real-World Relevance

IAM security is not only about creating users and assigning access. It also requires regular review.

In enterprise and government-regulated environments, identity teams must be able to prove who has access, who has elevated rights, which accounts are disabled, which groups control sensitive permissions, and whether access aligns with policy.

This lab connects directly to real-world IAM and security responsibilities:

- Reviewing privileged access
- Auditing Domain Admins membership
- Confirming offboarded users remain disabled
- Reviewing access after lifecycle changes
- Reviewing delegated administration boundaries
- Reviewing LAPS password-reader access
- Reviewing certificate-related access
- Producing compliance evidence
- Creating written security review summaries
- Separating evidence collection from remediation

The key lesson is that IAM work must be reviewable, documented, and evidence-based.

---

## Security Considerations

This lab reviewed the current access state but did not make access changes.

In production, findings should be reviewed against policy, ticket history, business ownership, and approval records before remediation.

Production-ready improvements would include:

- Comparing privileged access with an approved baseline
- Identifying inactive administrative accounts
- Reviewing nested group membership
- Confirming disabled users were removed from all access groups
- Reviewing service account ownership
- Validating administrative account separation
- Reviewing certificate authority and certificate template permissions
- Reviewing LAPS password-reader eligibility
- Reviewing delegated OU permissions
- Tracking findings in a formal risk register
- Opening remediation tickets for unauthorized access
- Creating recurring access review schedules
- Integrating evidence with SIEM or GRC tooling

---

## Lessons Learned

- IAM audits should begin with domain health validation
- Privileged access should be reviewed separately from standard user access
- Domain Admins membership should remain small and tightly controlled
- Disabled accounts still require review and cleanup
- Department groups provide useful business-aligned review points
- Delegated admin groups support least privilege when properly controlled
- LAPS password-reader access must be treated as privileged access
- AD CS groups matter because certificates affect trust and authentication
- Account state alone does not prove authorization
- Audit evidence should be exported instead of only viewed on screen
- Written summaries make audit results easier to understand and hand off

---

## What I Would Do Differently in Production

In a production or government-regulated environment, I would expand this audit into a formal access certification process.

A stronger production design would include:

- Defined business and technical owners for each group
- Formal quarterly access reviews
- Approved privileged-access baselines
- Nested group membership analysis
- Service account ownership and credential rotation reviews
- Inactive account thresholds
- Privileged account usage monitoring
- Certificate template permission reviews
- Delegated OU permission reviews
- Separation between reviewers and approvers
- Ticket references for access approvals
- Risk ratings for findings
- Remediation deadlines and tracking
- Centralized evidence retention
- Automated reporting
- SIEM alerts for privileged group changes
- Documented auditor access controls

For this lab, the goal was to demonstrate the core mechanics of an evidence-based IAM security review using Active Directory and PowerShell.

---

## Skills Demonstrated

- IAM security review
- Access control auditing
- Privileged group review
- Domain Admins review
- Disabled account review
- Department group membership review
- Delegated admin group review
- LAPS and security group review
- AD CS group review
- User account status review
- Active Directory PowerShell reporting
- CSV evidence export
- IAM audit documentation
- Security review summary creation
- Domain health validation
- Replication health validation
- Audit finding documentation
- Production remediation planning

---

## Outcome

Lab 22 successfully completed an IAM security review and access control audit in the MRTG enterprise Active Directory environment.

The audit reviewed privileged access, Domain Admins membership, disabled accounts, department groups, delegated administrative groups, LAPS and security groups, AD CS-related groups, and the broader user account inventory.

Eight CSV evidence files and a written IAM security review summary were created to preserve the results.

The final result was an evidence-based IAM audit package that demonstrated how identity access can be reviewed, documented, and prepared for compliance or remediation workflows.

---

## Next Lab

[Lab 23 - IAM Runbooks, SOPs, and Operational Handoff](../Lab-23-IAM-Runbooks-SOPs-Operational-Handoff/)

Lab 23 will build on this access review by creating operational runbooks, standard operating procedures, and handoff documentation for the MRTG enterprise IAM environment.
