# Lab-17 - Windows LAPS and Local Administrator Control

![Platform](https://img.shields.io/badge/Platform-Windows%20Server-blue)
![Technology](https://img.shields.io/badge/Technology-Active%20Directory-blue)
![Tooling](https://img.shields.io/badge/Tooling-Windows%20LAPS-purple)
![Focus](https://img.shields.io/badge/Focus-Local%20Administrator%20Control-orange)
![Security](https://img.shields.io/badge/Security-Least%20Privilege-red)
![Validation](https://img.shields.io/badge/Validation-Password%20Rotation-brightgreen)

## Overview

In this lab, I implemented Windows Local Administrator Password Solution (Windows LAPS) inside the Monroe Redstone Technology Group (MRTG) Active Directory environment.

This lab focused on controlling the built-in local Administrator password on domain-joined workstations by extending the Active Directory schema, delegating required permissions, creating a Windows LAPS Group Policy Object, applying the policy to the workstation OU, and validating password backup and retrieval.

The goal was to reduce local administrator password reuse risk and establish a stronger endpoint privilege control model.

## Objectives

- Validate Windows LAPS availability in the MRTG environment
- Confirm the target workstation is located in the correct workstation OU
- Create a safe pre-lab checkpoint before schema and policy changes
- Verify schema and administrative readiness before extending Active Directory
- Extend the Active Directory schema for Windows LAPS
- Resolve and validate replication health before continuing
- Grant workstation computer objects permission to update their own LAPS attributes
- Create a delegated group for LAPS password readers
- Assign read permissions for LAPS password retrieval
- Configure a Windows LAPS workstation baseline GPO
- Link the LAPS GPO to the workstation OU
- Apply and validate Group Policy on the client workstation
- Force Windows LAPS policy processing
- Validate LAPS password backup and retrieval
- Create post-lab checkpoints for rollback and documentation

## Scope

This lab focuses on Windows LAPS in an on-premises Active Directory environment.

This lab includes:

- Windows LAPS feature validation
- Active Directory schema extension
- AD permissions for computer self-service password backup
- Delegated password read permissions
- Group Policy-based LAPS configuration
- Client-side policy validation
- Password backup and retrieval validation

This lab does not include:

- Microsoft Intune LAPS policy
- Entra ID LAPS backup
- Azure AD joined device management
- Microsoft Defender for Endpoint integration
- Privileged Access Workstation deployment
- SIEM alerting for LAPS retrieval events
- Production password reader approval workflows

## Lab Environment

| Component | Details |
|---|---|
| Organization | Monroe Redstone Technology Group |
| Domain | `mrtg.local` |
| Primary Domain Controller | `MRTG-DC01` |
| Secondary Domain Controller | `MRTG-DC02` |
| Client VM | `MRTG-CLIENT-01` |
| AD Computer Name | `CLIENT01` |
| Target OU | `OU=Workstations,OU=Computers,OU=_MRTG,DC=mrtg,DC=local` |
| Platform | Hyper-V |
| Core Services | AD DS, DNS, Group Policy, Windows LAPS |
| Management Tools | ADUC, GPMC, PowerShell, Hyper-V Manager |

## Architecture / Design

The MRTG environment uses a dedicated workstation OU to apply endpoint security controls. Windows LAPS was configured through Group Policy and targeted to the workstation OU so that domain-joined workstations can automatically rotate and back up their local Administrator password to Active Directory.

The design separates three responsibilities:

| Responsibility | Implementation |
|---|---|
| Password generation and rotation | Windows LAPS client policy on `CLIENT01` |
| Password storage | Active Directory LAPS attributes |
| Password retrieval | Controlled through delegated AD read permissions |

This design supports least privilege by reducing shared local administrator password risk and limiting password visibility to approved administrative principals.

## Security Model

Windows LAPS helps address a common endpoint security weakness: repeated use of the same local Administrator password across multiple workstations.

Without LAPS, compromise of one local admin password can lead to lateral movement across many systems.

With LAPS, each managed workstation can maintain a unique, rotated local Administrator password that is backed up to Active Directory and retrieved only by authorized administrators.

## Technologies Used

- Windows Server 2022
- Windows 11 client workstation
- Active Directory Domain Services
- Active Directory Users and Computers
- Active Directory Administrative Center
- Group Policy Management Console
- Windows LAPS PowerShell module
- Hyper-V
- DNS
- PowerShell

## Key Accounts, Groups, and Objects

| Object | Purpose |
|---|---|
| `CLIENT01` | Domain-joined workstation targeted for LAPS management |
| `MRTG-GRP-LAPS-Password-Readers` | Delegated group for LAPS password read access |
| `Administrator` | Administrative account used during validation |
| `MRTG-GPO-Windows-LAPS-Workstation-Baseline` | GPO used to configure Windows LAPS settings |
| `_MRTG/Computers/Workstations` | Target OU for workstation LAPS policy |

## Implementation Steps

### Step 1 - Confirm Workstation OU Placement

Confirmed that `CLIENT01` was located in the `_MRTG/Computers/Workstations` OU. This ensured the workstation was in the correct scope before applying Windows LAPS policy.

![Workstation OU target confirmed](screenshots/lab-17-01-workstations-ou-target-client01.png)

### Step 2 - Validate Windows LAPS Availability

Checked whether the Windows LAPS PowerShell module and related commands were available on the domain controller.

Initial validation showed that the older legacy LAPS module path was not present, so I confirmed the modern Windows LAPS module using PowerShell and verified the server build/update level.

![Initial LAPS module validation](screenshots/lab-17-02a-laps-module-before-update-check.png)

![Windows LAPS module confirmed](screenshots/lab-17-02b-laps-module-confirmed-after-update.png)

### Step 3 - Create a Pre-Lab Checkpoint

Created a Hyper-V checkpoint before making schema-level and policy-level changes. This provided a rollback point before modifying Active Directory schema and LAPS permissions.

![Pre-Lab 17 checkpoint](screenshots/lab-17-03-pre-lab17-checkpoint.png)

### Step 4 - Confirm Schema Admin Readiness

Validated administrative context and confirmed the required privileged groups before attempting the Windows LAPS schema extension.

This step mattered because schema changes are high-impact directory changes and should only be performed using appropriate administrative authority.

![Schema admin readiness confirmed](screenshots/lab-17-04-schema-admin-permissions-confirmed.png)

### Step 5 - Identify Schema Extension Issue

Attempted the Windows LAPS schema extension and encountered an operation error.

Instead of continuing blindly, I validated FSMO role ownership, replication health, and domain controller communication. This exposed a DNS/replication issue that needed to be corrected before continuing.

![LAPS schema extension error](screenshots/lab-17-05a-laps-schema-extension-error.png)

![Replication DNS issue identified](screenshots/lab-17-05b-replication-dns-failure-identified.png)

### Step 6 - Restore Replication Health

Corrected the replication and DNS issue and confirmed that replication was healthy again before continuing with Windows LAPS configuration.

This was an important operational checkpoint because LAPS schema and password attribute changes depend on healthy Active Directory replication.

![Replication health restored](screenshots/lab-17-06-replication-health-restored.png)

### Step 7 - Confirm Windows LAPS Schema Attributes

Validated that Windows LAPS schema attributes existed in Active Directory.

Confirmed attributes included:

- `ms-LAPS-EncryptedDSRMPassword`
- `ms-LAPS-EncryptedDSRMPasswordHistory`
- `ms-LAPS-EncryptedPassword`
- `ms-LAPS-EncryptedPasswordHistory`
- `ms-LAPS-Password`
- `ms-LAPS-PasswordExpirationTime`

![Windows LAPS schema attributes confirmed](screenshots/lab-17-07-laps-schema-attributes-confirmed.png)

### Step 8 - Grant Computer Self-Permission

Granted workstation computer objects permission to update their own Windows LAPS password attributes in Active Directory.

Command used:

```powershell
Set-LapsADComputerSelfPermission -Identity "OU=Workstations,OU=Computers,OU=_MRTG,DC=mrtg,DC=local"
```

![LAPS computer self-permission configured](screenshots/lab-17-08-laps-workstations-self-permission-configured.png)

### Step 9 - Create LAPS Password Reader Group

Created the `MRTG-GRP-LAPS-Password-Readers` security group to support delegated LAPS password retrieval.

This group provides a cleaner access model than assigning LAPS password read access directly to individual accounts.

![LAPS password readers group created](screenshots/lab-17-09a-laps-password-readers-group-created.png)

Added the administrative account to the LAPS password reader group for validation.

![LAPS password readers group membership](screenshots/lab-17-09b-laps-password-readers-membership.png)

### Step 10 - Delegate LAPS Password Read Permission

Granted the LAPS password reader group permission to read LAPS password information for computers in the Workstations OU.

Command used:

```powershell
Set-LapsADReadPasswordPermission -Identity "OU=Workstations,OU=Computers,OU=_MRTG,DC=mrtg,DC=local" -AllowedPrincipals "MRTG\MRTG-GRP-LAPS-Password-Readers"
```

![LAPS read password permission delegated](screenshots/lab-17-10-laps-read-password-permission-delegated.png)

### Step 11 - Link Windows LAPS GPO to Workstations OU

Created and linked the Windows LAPS workstation baseline GPO to the `_MRTG/Computers/Workstations` OU.

The GPO used in this lab was:

```text
MRTG-GPO-Windows-LAPS-Workstation-Baseline
```

![LAPS GPO linked to Workstations OU](screenshots/lab-17-11-laps-gpo-linked-workstations-ou.png)

### Step 12 - Configure LAPS Password Backup Directory

Configured the Windows LAPS policy setting to back up the local Administrator password to Active Directory.

Policy path:

```text
Computer Configuration > Policies > Administrative Templates > System > LAPS
```

Key setting configured:

```text
Configure password backup directory: Enabled
Backup directory: Active Directory
```

![LAPS backup directory enabled](screenshots/lab-17-12-laps-backup-directory-enabled.png)

### Step 13 - Validate Client Policy Application and Password Backup

Ran `gpupdate /force` and `gpresult /r /scope computer` on `CLIENT01` to confirm that the Windows LAPS GPO applied successfully.

![Client gpresult confirms LAPS GPO applied](screenshots/lab-17-13a-client-gpresult-laps-gpo-applied.png)

Confirmed Windows LAPS commands existed on the client and forced LAPS policy processing.

Command used:

```powershell
Invoke-LapsPolicyProcessing -Verbose
```

![Client LAPS policy processing successful](screenshots/lab-17-13b-client-laps-policy-processing-successful.png)

Validated that LAPS password information was backed up for `CLIENT01`.

Commands used:

```powershell
Get-LapsADPassword -Identity CLIENT01
Get-LapsADPassword -Identity CLIENT01 -AsPlainText
```

The plaintext password was intentionally masked in the screenshot.

![LAPS password retrieval validated](screenshots/lab-17-13c-laps-password-retrieval-validated.png)

### Step 14 - Create Post-Lab Checkpoints

Created post-lab checkpoints after Windows LAPS was validated.

Checkpoint name for `MRTG-DC01`:

```text
MRTG-DC01_Post-Lab-17-Windows-LAPS-and-Local-Administrator-Control-Validated
```

![MRTG-DC01 post-lab checkpoint](screenshots/lab-17-14a-dc01-post-lab17-checkpoint.png)

Checkpoint name for `MRTG-CLIENT-01`:

```text
Post-Lab-17-Windows-LAPS-and-Local-Administrator-Control-Validated
```

![MRTG-CLIENT-01 post-lab checkpoint](screenshots/lab-17-14b-client01-post-lab17-checkpoint.png)

## Validation / Verification

| Validation Item | Result |
|---|---|
| `CLIENT01` confirmed in Workstations OU | Passed |
| Windows LAPS module availability validated | Passed |
| Pre-lab checkpoint created | Passed |
| Schema/admin readiness checked | Passed |
| Initial schema/replication issue identified | Passed |
| Replication health restored before continuing | Passed |
| Windows LAPS schema attributes confirmed | Passed |
| Workstations OU granted computer self-permission | Passed |
| LAPS password reader group created | Passed |
| LAPS password read permission delegated | Passed |
| Windows LAPS GPO linked to Workstations OU | Passed |
| LAPS backup directory configured for Active Directory | Passed |
| Client received the LAPS GPO | Passed |
| LAPS policy processing completed successfully | Passed |
| LAPS password backup and retrieval validated | Passed |
| Post-lab checkpoints created | Passed |

## Evidence Collected

| Evidence | File |
|---|---|
| Workstation OU target confirmed | `screenshots/lab-17-01-workstations-ou-target-client01.png` |
| Initial LAPS module validation | `screenshots/lab-17-02a-laps-module-before-update-check.png` |
| Windows LAPS module confirmed | `screenshots/lab-17-02b-laps-module-confirmed-after-update.png` |
| Pre-Lab 17 checkpoint | `screenshots/lab-17-03-pre-lab17-checkpoint.png` |
| Schema admin readiness confirmed | `screenshots/lab-17-04-schema-admin-permissions-confirmed.png` |
| LAPS schema extension error | `screenshots/lab-17-05a-laps-schema-extension-error.png` |
| Replication DNS issue identified | `screenshots/lab-17-05b-replication-dns-failure-identified.png` |
| Replication health restored | `screenshots/lab-17-06-replication-health-restored.png` |
| Windows LAPS schema attributes confirmed | `screenshots/lab-17-07-laps-schema-attributes-confirmed.png` |
| LAPS computer self-permission configured | `screenshots/lab-17-08-laps-workstations-self-permission-configured.png` |
| LAPS password readers group created | `screenshots/lab-17-09a-laps-password-readers-group-created.png` |
| LAPS password readers group membership | `screenshots/lab-17-09b-laps-password-readers-membership.png` |
| LAPS read password permission delegated | `screenshots/lab-17-10-laps-read-password-permission-delegated.png` |
| LAPS GPO linked to Workstations OU | `screenshots/lab-17-11-laps-gpo-linked-workstations-ou.png` |
| LAPS backup directory enabled | `screenshots/lab-17-12-laps-backup-directory-enabled.png` |
| Client gpresult confirms LAPS GPO applied | `screenshots/lab-17-13a-client-gpresult-laps-gpo-applied.png` |
| Client LAPS policy processing successful | `screenshots/lab-17-13b-client-laps-policy-processing-successful.png` |
| LAPS password retrieval validated | `screenshots/lab-17-13c-laps-password-retrieval-validated.png` |
| MRTG-DC01 post-lab checkpoint | `screenshots/lab-17-14a-dc01-post-lab17-checkpoint.png` |
| MRTG-CLIENT-01 post-lab checkpoint | `screenshots/lab-17-14b-client01-post-lab17-checkpoint.png` |

## Commands Used

```powershell
Get-Command *Laps*
Get-Module -ListAvailable LAPS
Get-ComputerInfo | Select-Object WindowsProductName, OsVersion, OsBuildNumber
Get-HotFix -Id KB5030216
```

```powershell
whoami
whoami /groups | findstr /i "Schema Enterprise Domain"
net group "Schema Admins" /domain
net group "Enterprise Admins" /domain
net group "Domain Admins" /domain
```

```powershell
Update-LapsADSchema -Verbose
```

```powershell
repadmin /replsummary
dcdiag /test:advertising /test:services /test:replications
```

```powershell
$schema = (Get-ADRootDSE).schemaNamingContext
Get-ADObject -SearchBase $schema -Filter 'Name -like "ms-LAPS-*"' | Select-Object Name,ObjectClass
```

```powershell
Set-LapsADComputerSelfPermission -Identity "OU=Workstations,OU=Computers,OU=_MRTG,DC=mrtg,DC=local"
```

```powershell
Set-LapsADReadPasswordPermission -Identity "OU=Workstations,OU=Computers,OU=_MRTG,DC=mrtg,DC=local" -AllowedPrincipals "MRTG\MRTG-GRP-LAPS-Password-Readers"
```

```powershell
gpupdate /force
gpresult /r /scope computer
```

```powershell
Invoke-LapsPolicyProcessing -Verbose
```

```powershell
Get-LapsADPassword -Identity CLIENT01
Get-LapsADPassword -Identity CLIENT01 -AsPlainText
```

## Issue Encountered and Resolution

During the Windows LAPS schema extension process, an operation error occurred. Instead of treating the error as a simple command failure, I validated the domain controller environment and identified a DNS/replication issue.

The issue was corrected before continuing with the LAPS implementation.

This was important because schema changes, LAPS attributes, Group Policy, and password backup all depend on healthy Active Directory replication.

## Security Notes

- LAPS passwords should never be exposed in screenshots or public documentation.
- The plaintext password output was intentionally masked.
- LAPS password retrieval should be restricted to approved administrative roles only.
- Password reader membership should be reviewed regularly.
- Domain Admins should not be the only long-term operational model for LAPS retrieval in production.
- If encrypted LAPS passwords are used, authorized decryptors should be explicitly planned and documented.
- LAPS retrieval events should be monitored in production environments.

## What I Would Improve in Production

In a production environment, I would improve this design by:

- Defining a formal LAPS password reader approval process
- Using a dedicated privileged group for LAPS password retrieval
- Explicitly configuring authorized password decryptors
- Monitoring LAPS password retrieval events
- Alerting on unusual or repeated LAPS password reads
- Rotating local Administrator passwords immediately after use
- Limiting LAPS readers to tiered administrative accounts
- Using Privileged Access Workstations for administrative activity
- Documenting emergency access procedures
- Validating LAPS coverage across all workstation OUs
- Reviewing stale computer objects before applying policy
- Including LAPS retrieval in privileged access audits
- Testing restore and recovery procedures before production rollout

## Lessons Learned

This lab reinforced that local administrator control is a core part of identity security.

The most important lesson is that endpoint privilege is still identity privilege. If every workstation shares the same local Administrator password, one compromised endpoint can become a path to broader compromise.

Windows LAPS reduces that risk by giving each workstation a unique managed password and storing it in Active Directory under controlled access.

This lab also reinforced that schema changes should never be rushed. When replication or DNS is unhealthy, identity infrastructure work should stop until the foundation is stable.

## Outcome

Lab-17 successfully implemented Windows LAPS and local Administrator password control in the MRTG Active Directory environment.

The lab validated that:

- `CLIENT01` was properly scoped in the Workstations OU
- Windows LAPS was available and functional
- Active Directory contained Windows LAPS schema attributes
- Workstation computer objects could update LAPS attributes
- LAPS read permissions were delegated
- A Windows LAPS GPO was linked and applied
- `CLIENT01` processed LAPS policy successfully
- The local Administrator password was backed up to Active Directory
- Password retrieval was validated
- Post-lab checkpoints were created for both `MRTG-DC01` and `MRTG-CLIENT-01`

This lab strengthened the MRTG IAM series by adding endpoint-level privileged access control to the existing Active Directory, Group Policy, auditing, delegation, and tiered administration foundation.

## Next Lab

[Lab-18 - Group-Based Access Control for File and Department Resources](../Lab-18-Group-Based-Access-Control-for-File-and-Department-Resources)

Lab-18 will build on prior IAM concepts by using Active Directory security groups to control access to shared resources through a scalable authorization model.
