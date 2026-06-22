# Lab 17: Windows LAPS and Local Administrator Control

![Platform](https://img.shields.io/badge/Platform-Windows%20Server-blue)
![Technology](https://img.shields.io/badge/Technology-Active%20Directory-blue)
![Feature](https://img.shields.io/badge/Feature-Windows%20LAPS-purple)
![Focus](https://img.shields.io/badge/Focus-Local%20Admin%20Control-orange)
![Security](https://img.shields.io/badge/Security-Credential%20Protection-red)
![Validation](https://img.shields.io/badge/Validation-Password%20Backup-brightgreen)

---

## Objective

Implement Windows Local Administrator Password Solution in the `mrtg.local` Active Directory environment.

This lab configures Active Directory backup for a workstation's managed local administrator password, delegates password-read permission through a security group, applies policy through Group Policy, and validates client-side LAPS processing.

The lab also documents troubleshooting performed after an initial schema-update error exposed an Active Directory replication and DNS health issue.

---

## Business Scenario

Monroe Redstone Technology Group requires a secure method for managing local administrator credentials on domain-joined workstations.

Reusing the same local administrator password across multiple systems increases lateral-movement risk. If one workstation and its local credential are compromised, the same password may provide access to other systems.

This lab addresses the need to:

- Generate and manage workstation local administrator passwords
- Avoid shared local administrator credentials
- Back up managed password information to Active Directory
- Limit password retrieval to authorized administrators
- Apply LAPS through centralized policy
- Validate client processing and Active Directory backup
- Confirm directory health before schema-related changes

---

## Lab Summary

In this lab, I configured Windows LAPS for the MRTG workstation environment.

The Windows client computer object `CLIENT01` was confirmed in `_MRTG\Computers\Workstations`, and Windows LAPS commands were validated on the domain controller.

An initial `Update-LapsADSchema` attempt returned an operation error. Replication and DNS health were reviewed, an unhealthy condition was corrected, and the required `ms-LAPS-*` schema attributes were then confirmed.

Computer self-permission was applied to the Workstations OU. A dedicated LAPS password-readers group was created, and read permission was delegated to that group.

The `MRTG-GPO-Windows-LAPS-Workstation-Baseline` GPO was linked to the Workstations OU. `CLIENT01` applied the policy, completed LAPS processing, and wrote password information to Active Directory.

Password metadata retrieval was validated without publishing the managed password.

---

## Environment

| Component | Details |
|---|---|
| Organization | Monroe Redstone Technology Group |
| Domain | `mrtg.local` |
| Original Domain Controller | `MRTG-DC01` |
| Additional Domain Controller | `MRTG-DC02` |
| Client Windows Computer Name | `CLIENT01` |
| Client Hyper-V VM Name | `MRTG-CLIENT-01` |
| Target OU | `_MRTG\Computers\Workstations` |
| LAPS GPO | `MRTG-GPO-Windows-LAPS-Workstation-Baseline` |
| Password Readers Group | `MRTG-GRP-LAPS-Password-Readers` |
| Password Backup Directory | Active Directory |
| Tools | ADUC, Group Policy Management, PowerShell, and Hyper-V |

---

## Prerequisites

- Operational `mrtg.local` domain
- Healthy replication between `MRTG-DC01` and `MRTG-DC02`
- Active Directory-integrated DNS
- Supported Windows Server and Windows client builds
- Windows LAPS PowerShell module
- Domain functional level compatible with the selected LAPS features
- `CLIENT01` located in the Workstations OU
- Administrative approval for schema-related work
- Supported Active Directory backup and recovery procedures

---

## Scope

### Included

- Windows LAPS readiness review
- Schema command and attribute validation
- Replication and DNS troubleshooting
- Workstation OU self-permission
- Password-readers group creation
- Group-based read-permission delegation
- Workstation LAPS GPO configuration
- Group Policy application validation
- Manual LAPS policy processing
- Active Directory password-backup validation
- Password metadata retrieval
- Temporary Hyper-V checkpoints

### Not Included

- Microsoft Entra ID password backup
- Intune policy deployment
- Cloud-only device management
- Group Managed Service Accounts
- Just-in-time password access
- Automated access approval
- SIEM alerting for password retrieval
- Production password-disclosure workflow
- Least-privilege reader-account retrieval test
- Active Directory schema rollback testing

---

## Architecture

```text
mrtg.local
`-- _MRTG
    |-- Groups
    |   `-- MRTG-GRP-LAPS-Password-Readers
    |
    `-- Computers
        `-- Workstations
            `-- CLIENT01
                `-- Managed local administrator password
```

Policy and permission flow:

```text
MRTG-GPO-Windows-LAPS-Workstation-Baseline
                    |
                    v
        _MRTG\Computers\Workstations
                    |
                    v
                 CLIENT01
                    |
                    v
     Password information backed up to AD
                    |
                    v
 Authorized readers retrieve through delegation
```

---

## Windows LAPS Control Model

| Control | Purpose |
|---|---|
| Workstations OU | Defines the managed computer scope |
| LAPS GPO | Configures password management and Active Directory backup |
| Computer Self-Permission | Allows each computer to update its own LAPS attributes |
| Password Readers Group | Represents approved password-retrieval access |
| Read-Permission Delegation | Grants the group access to the required LAPS password attributes |
| Active Directory Attributes | Store password and expiration information |
| Client Processing | Generates, applies, and backs up the managed password |
| Retrieval Validation | Confirms that password information exists in Active Directory |

Windows LAPS manages a local administrator password. It does not create a custom local account or automatically enable a disabled account unless separate configuration handles those requirements.

---

## Key Objects

| Object | Purpose |
|---|---|
| `CLIENT01` | Managed workstation computer object |
| `MRTG-GPO-Windows-LAPS-Workstation-Baseline` | Applies Windows LAPS settings |
| `MRTG-GRP-LAPS-Password-Readers` | Receives delegated password-read permission |
| `_MRTG\Computers\Workstations` | Defines the LAPS policy and permission scope |
| `Administrator` | Privileged account used during lab retrieval validation |

Using the built-in domain Administrator account confirms that password information can be retrieved, but it does not independently prove least-privilege reader access because that account already holds broad privileges.

---

## Implementation and Validation

### 1. Created a Pre-Change Lab Checkpoint

Checkpoint name:

```text
MRTG-DC01_Pre-Lab-17-Windows-LAPS-Schema-Extension
```

![Pre-LAPS schema extension checkpoint](screenshots/lab-17-01-pre-laps-schema-extension-checkpoint.png)

The checkpoint provided a temporary VM recovery point only.

An Active Directory schema extension is forest-wide and is not safely reversed by restoring one domain controller checkpoint. Schema changes should be treated as effectively irreversible and protected through supported forest-recovery planning.

---

### 2. Confirmed the Workstation OU Placement

The `CLIENT01` computer object was confirmed under:

```text
_MRTG\Computers\Workstations
```

![CLIENT01 in Workstations OU](screenshots/lab-17-02-client01-computer-object-in-workstations-ou.png)

Correct OU placement ensured that the workstation was within the intended policy and permission scope.

---

### 3. Validated Windows LAPS Availability

Commands used:

```powershell
Get-HotFix -Id KB5030216
Get-Command *Laps*
Get-Module -ListAvailable LAPS
```

![Windows LAPS commands available](screenshots/lab-17-03-kb5030216-installed-and-laps-commands-available.png)

This confirmed that Windows LAPS functionality and PowerShell commands were available in the lab environment.

The specific KB documents the lab state at the time. Production readiness should be based on a currently supported and fully patched operating-system build rather than one historical update number.

---

### 4. Documented the Module Path Check

The expected module path was reviewed along with the operating-system build.

![Windows LAPS module path check](screenshots/lab-17-04-windows-laps-module-not-detected.png)

The expected path was not detected, but the LAPS commands remained available through the installed module.

This demonstrated why command availability and module discovery provide stronger evidence than checking one hard-coded path.

---

### 5. Reviewed the Schema-Change Security Context

The administrative token was reviewed for:

```text
Schema Admins
Enterprise Admins
Domain Admins
```

![Schema Admin membership check](screenshots/lab-17-05-schema-admin-membership-check.png)

These memberships provided the authority required for the lab's forest-wide schema work.

In production, Schema Admins membership should be temporary, approved, monitored, and removed immediately after the authorized change.

---

### 6. Attempted the Schema Update

Command used:

```powershell
Update-LapsADSchema -Verbose
```

![LAPS schema extension operation error](screenshots/lab-17-06-laps-schema-extension-failed-operation-error.png)

The command returned an operation error. The error was documented, and directory health was investigated before further schema work.

---

### 7. Identified Replication and DNS Health Problems

Commands used:

```cmd
netdom query fsmo
repadmin /replsummary
dcdiag /test:advertising /test:services /test:replications
```

![Replication and DNS failure before schema validation](screenshots/lab-17-07-replication-dns-failure-before-laps-schema-retry.png)

The results showed a DNS-related replication problem between the domain controllers.

A forest-wide change should not continue while replication health is uncertain.

---

### 8. Restored Replication Health

Command used:

```cmd
repadmin /replsummary
```

![Replication health restored](screenshots/lab-17-08-replication-health-restored-before-laps-schema-retry.png)

The final summary confirmed healthy replication before LAPS deployment continued.

---

### 9. Verified the Windows LAPS Schema Attributes

Confirmed attributes included:

```text
ms-LAPS-EncryptedDSRMPassword
ms-LAPS-EncryptedDSRMPasswordHistory
ms-LAPS-EncryptedPassword
ms-LAPS-EncryptedPasswordHistory
ms-LAPS-Password
ms-LAPS-PasswordExpirationTime
```

![Windows LAPS schema attributes verified](screenshots/lab-17-09-windows-laps-schema-extension-verified.png)

This confirmed that the required Windows LAPS schema objects were present.

The evidence confirms attribute presence. It does not establish whether the initial command completed partially or whether a later operation completed the schema update.

---

### 10. Granted Computer Self-Permission

Command used:

```powershell
Set-LapsADComputerSelfPermission `
    -Identity "OU=Workstations,OU=Computers,OU=_MRTG,DC=mrtg,DC=local"
```

![Workstations OU LAPS computer self-permission](screenshots/lab-17-10-workstations-ou-laps-computer-self-permission-set.png)

This allows computer objects in the Workstations OU to update their own LAPS password attributes.

---

### 11. Created the Password Readers Group

Group name:

```text
MRTG-GRP-LAPS-Password-Readers
```

![LAPS password readers group created](screenshots/lab-17-11-laps-password-readers-group-created.png)

The group provides a centralized assignment point for password-retrieval access.

---

### 12. Added the Lab Reader

The built-in domain Administrator account was added to the password readers group for lab validation.

![Administrator added to LAPS password readers group](screenshots/lab-17-12-admin-added-to-laps-password-readers-group.png)

This documented group membership but did not provide a least-privilege test because the account already had extensive directory privileges.

---

### 13. Delegated Password-Read Permission

Command used:

```powershell
Set-LapsADReadPasswordPermission `
    -Identity "OU=Workstations,OU=Computers,OU=_MRTG,DC=mrtg,DC=local" `
    -AllowedPrincipals "MRTG\MRTG-GRP-LAPS-Password-Readers"
```

![LAPS read permission delegated](screenshots/lab-17-13-laps-read-password-permission-delegated.png)

This assigned LAPS password-read permission to a group rather than directly to an individual account.

---

### 14. Linked the Windows LAPS GPO

GPO:

```text
MRTG-GPO-Windows-LAPS-Workstation-Baseline
```

Target:

```text
_MRTG\Computers\Workstations
```

![Windows LAPS GPO linked](screenshots/lab-17-14-windows-laps-gpo-linked-to-workstations-ou.png)

This scoped the LAPS policy to workstation computer objects.

---

### 15. Configured the Windows LAPS Policy

The GPO was configured to manage the workstation's local administrator password and back up password information to Active Directory.

![Windows LAPS policy settings configured](screenshots/lab-17-15-windows-laps-policy-settings-configured.png)

Only settings visible in the captured policy evidence should be treated as verified. Password length, age, complexity, encryption, and account-name values should not be assumed unless explicitly documented.

---

### 16. Applied and Reviewed Group Policy on CLIENT01

Commands used:

```cmd
gpupdate /force
gpresult /r /scope computer
```

![CLIENT01 Windows LAPS GPO applied](screenshots/lab-17-16-client01-laps-gpo-applied-with-gpresult.png)

The result confirmed that `MRTG-GPO-Windows-LAPS-Workstation-Baseline` was processed by the workstation.

---

### 17. Invoked Windows LAPS Processing

Command used:

```powershell
Invoke-LapsPolicyProcessing -Verbose
```

![CLIENT01 Windows LAPS processing](screenshots/lab-17-17-client01-laps-policy-processing-invoked.png)

The command completed successfully, confirming client-side LAPS processing.

---

### 18. Retrieved LAPS Password Information

Commands used:

```powershell
Get-LapsADPassword -Identity CLIENT01
Get-LapsADPassword -Identity CLIENT01 -AsPlainText
```

![LAPS password retrieval metadata](screenshots/lab-17-18-laps-password-retrieval-metadata.png)

The results confirmed that LAPS password information and expiration metadata were available in Active Directory.

The managed password is intentionally omitted from this README. Screenshots and public documentation should redact plaintext credentials.

---

### 19. Created the MRTG-DC01 Final Lab Checkpoint

Checkpoint name:

```text
MRTG-DC01_Post-Lab-17-Windows-LAPS-and-Local-Administrator-Control-Validated
```

![MRTG-DC01 post-Lab 17 checkpoint](screenshots/lab-17-19-dc01-post-lab17-checkpoint.png)

---

### 20. Created the Client Final Lab Checkpoint

Checkpoint name:

```text
Post-Lab-17-Windows-LAPS-and-Local-Administrator-Control-Validated
```

![CLIENT01 post-Lab 17 checkpoint](screenshots/lab-17-20-client01-post-lab17-checkpoint.png)

The checkpoints were temporary lab recovery points and were not substitutes for supported backups or forest-recovery procedures.

---

## Troubleshooting Summary

The initial schema-update attempt returned an operation error.

The response was to:

1. Stop the schema workflow
2. Identify FSMO role ownership
3. Review replication health
4. Run domain controller diagnostics
5. Correct the DNS and replication issue
6. Confirm healthy replication
7. Verify the required Windows LAPS schema attributes
8. Continue with permissions, policy, and client validation

This reinforced a critical Active Directory rule: forest-wide changes should not proceed while directory replication is unhealthy.

---

## Validation Limitation

The lab confirmed that:

- The password-readers group was created
- LAPS read permission was assigned to the group
- Password information could be retrieved by a privileged administrator

The lab did not capture retrieval testing with a non-privileged named account whose only relevant permission came from `MRTG-GRP-LAPS-Password-Readers`.

A complete least-privilege test would verify:

- Authorized named reader can retrieve the password
- Standard user cannot retrieve the password
- Reader cannot modify unrelated computer attributes
- Retrieval activity is logged and centrally monitored

---

## Security and IAM Relevance

Windows LAPS reduces the risk created by shared or unmanaged local administrator passwords.

This lab supports:

- Unique local administrator credentials
- Managed password rotation
- Centralized Active Directory backup
- Group-based password-read delegation
- Workstation-scoped policy
- Reduced credential reuse
- Reduced lateral-movement opportunity
- Privileged credential governance
- Directory-health validation before schema changes

LAPS protects one credential layer. It does not remove the need to restrict local administrator membership, secure privileged workstations, monitor retrieval, or protect Active Directory.

---

## Risks Addressed

This lab reduces the risk of:

- Shared local administrator passwords
- Reused local credentials across workstations
- Manual password tracking
- Unmanaged password age
- Direct per-user retrieval permissions
- Missing workstation password backup
- Unhealthy replication during directory-wide changes
- Public exposure of managed credentials

---

## Control Mapping

| Control Area | Lab Contribution |
|---|---|
| Local Administrator Governance | Configures Windows LAPS for the workstation scope |
| Credential Protection | Replaces shared-password management with per-device password data |
| Group-Based Delegation | Assigns password-read permission to a security group |
| Policy Enforcement | Applies Windows LAPS through Group Policy |
| Directory Integrity | Validates replication health before continuing |
| Endpoint Validation | Confirms client policy processing |
| Audit Readiness | Captures configuration and password-metadata evidence |
| Least Privilege | Establishes a dedicated reader group, with further reader testing identified |

---

## Validation Results

| Validation Item | Result |
|---|---|
| Temporary pre-change checkpoint created | Passed |
| `CLIENT01` located in Workstations OU | Passed |
| Windows LAPS commands available | Passed |
| Initial module-path issue documented | Passed |
| Schema-change security context reviewed | Passed |
| Initial schema operation error documented | Passed |
| Replication and DNS issue identified | Passed |
| Replication health restored | Passed |
| Required LAPS schema attributes confirmed | Passed |
| Computer self-permission applied | Passed |
| Password readers group created | Passed |
| Lab reader added to group | Passed |
| Password-read permission delegated | Passed |
| Windows LAPS GPO linked | Passed |
| Windows LAPS policy configured | Passed |
| GPO processed by `CLIENT01` | Passed |
| LAPS policy processing completed | Passed |
| Password information retrieved from Active Directory | Passed |
| Non-privileged reader-only retrieval test | Not tested |
| Unauthorized retrieval test | Not tested |
| Temporary final checkpoints created | Passed |

---

## Evidence Collected

| Evidence | Screenshot |
|---|---|
| Pre-change checkpoint | `screenshots/lab-17-01-pre-laps-schema-extension-checkpoint.png` |
| Workstation OU placement | `screenshots/lab-17-02-client01-computer-object-in-workstations-ou.png` |
| Windows LAPS command availability | `screenshots/lab-17-03-kb5030216-installed-and-laps-commands-available.png` |
| Module-path check | `screenshots/lab-17-04-windows-laps-module-not-detected.png` |
| Schema Admin context | `screenshots/lab-17-05-schema-admin-membership-check.png` |
| Schema operation error | `screenshots/lab-17-06-laps-schema-extension-failed-operation-error.png` |
| Replication and DNS failure | `screenshots/lab-17-07-replication-dns-failure-before-laps-schema-retry.png` |
| Restored replication health | `screenshots/lab-17-08-replication-health-restored-before-laps-schema-retry.png` |
| Windows LAPS schema attributes | `screenshots/lab-17-09-windows-laps-schema-extension-verified.png` |
| Computer self-permission | `screenshots/lab-17-10-workstations-ou-laps-computer-self-permission-set.png` |
| Password readers group | `screenshots/lab-17-11-laps-password-readers-group-created.png` |
| Lab reader membership | `screenshots/lab-17-12-admin-added-to-laps-password-readers-group.png` |
| Password-read delegation | `screenshots/lab-17-13-laps-read-password-permission-delegated.png` |
| Windows LAPS GPO link | `screenshots/lab-17-14-windows-laps-gpo-linked-to-workstations-ou.png` |
| Windows LAPS policy | `screenshots/lab-17-15-windows-laps-policy-settings-configured.png` |
| Client Group Policy result | `screenshots/lab-17-16-client01-laps-gpo-applied-with-gpresult.png` |
| Client LAPS processing | `screenshots/lab-17-17-client01-laps-policy-processing-invoked.png` |
| Password information retrieval | `screenshots/lab-17-18-laps-password-retrieval-metadata.png` |
| MRTG-DC01 checkpoint | `screenshots/lab-17-19-dc01-post-lab17-checkpoint.png` |
| Client checkpoint | `screenshots/lab-17-20-client01-post-lab17-checkpoint.png` |

---

## What I Would Improve in Production

In a production environment, I would:

- Use named administrative accounts for password retrieval
- Test retrieval with an account that has only the delegated reader role
- Confirm unauthorized users cannot retrieve password information
- Use approval-based or time-bound reader access
- Enable password encryption where supported and required
- Define password length, age, and complexity standards
- Audit Windows LAPS events and password retrieval activity
- Forward LAPS-related events to centralized monitoring
- Review extended rights with `Find-LapsADExtendedRights`
- Remove unnecessary extended rights from the managed OU
- Use hardened privileged workstations
- Maintain a formal emergency-access process
- Keep Schema Admins membership empty except during approved changes
- Use formal change management for schema extensions
- Maintain supported System State and forest-recovery backups
- Never publish plaintext passwords in screenshots or documentation
- Use Hyper-V checkpoints only as temporary lab tools

---

## Lessons Learned

This lab reinforced that Windows LAPS deployment includes more than creating a GPO.

Successful deployment requires:

- Supported operating-system functionality
- Healthy Active Directory replication
- Required schema attributes
- Computer self-permission
- Controlled reader permissions
- Correct OU targeting
- Client-side policy processing
- Active Directory password backup
- Secure retrieval practices

The most important lesson was that schema work should stop when directory health is uncertain.

The lab also exposed an important validation gap: password retrieval by a broadly privileged account does not prove that least-privilege delegation works. A dedicated reader-only account should be tested in production-quality validation.

---

## Outcome

Lab 17 successfully implemented Windows LAPS for `CLIENT01`.

The lab confirmed that:

- The workstation was in the correct OU
- Windows LAPS commands were available
- A replication and DNS issue was identified and corrected
- Required Windows LAPS schema attributes were present
- Computer self-permission was configured
- A password-readers group was created
- Password-read permission was delegated
- The Windows LAPS GPO applied to the workstation
- Client-side LAPS processing completed
- Password information was backed up to Active Directory
- Password metadata could be retrieved without publishing the credential

The environment now has a functional foundation for managed local administrator passwords and reduced credential-reuse risk.

---

## Next Lab

[Lab 18: Group-Based Access Control for File and Department Resources](../Lab-18-Group-Based-Access-Control-for-File-and-Department-Resources/)

Lab 18 applies a scalable group-nesting model to departmental file access and validates authorization after identity-role changes.
