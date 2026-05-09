# Lab-17 — Windows LAPS and Local Administrator Control

![Status](https://img.shields.io/badge/Status-Complete-brightgreen)
![Platform](https://img.shields.io/badge/Platform-Windows%20Server-blue)
![Technology](https://img.shields.io/badge/Technology-Active%20Directory-blue)
![Tooling](https://img.shields.io/badge/Tooling-Windows%20LAPS-purple)
![Tooling](https://img.shields.io/badge/Tooling-Group%20Policy-purple)
![Tooling](https://img.shields.io/badge/Tooling-PowerShell-purple)
![Focus](https://img.shields.io/badge/Focus-Privileged%20Endpoint%20Protection-orange)
![Validation](https://img.shields.io/badge/Validation-gpupdate%20%7C%20gpresult%20%7C%20Get--LapsADPassword-brightgreen)

---

## Objective

The objective of this lab was to deploy and validate Windows LAPS in the MRTG Active Directory environment to centrally manage local administrator passwords on domain-joined workstations.

This lab focused on protecting privileged local administrator access by using Windows LAPS, Active Directory schema extensions, scoped Organizational Unit permissions, Group Policy targeting, and controlled password retrieval.

---

## Lab Summary

In this lab, Windows LAPS was configured for the workstation endpoint `CLIENT01`.

The lab began by confirming the workstation computer object placement inside the Workstations OU. During implementation, Windows LAPS PowerShell cmdlets were not initially available on `MRTG-DC01`, requiring an offline Windows Server update. After installing the required update, the LAPS module became available.

The Active Directory schema extension initially failed. Instead of forcing the configuration forward, administrative group membership was verified and Active Directory replication health was checked. A DNS-related replication issue involving `MRTG-DC02` was identified and corrected before retrying the schema-related work.

After replication health was restored, Windows LAPS schema objects were verified, workstation computer self-permissions were configured, a dedicated LAPS password readers group was created, LAPS read permissions were delegated, and a workstation-targeted GPO was created and linked.

Finally, the LAPS policy was applied to `CLIENT01`, LAPS processing was manually invoked, and LAPS password metadata was successfully retrieved from Active Directory without exposing the managed local administrator password in documentation.

---

## Environment

| Component | Value |
|---|---|
| Domain | `mrtg.local` |
| Primary Domain Controller | `MRTG-DC01` |
| Additional Domain Controller | `MRTG-DC02` |
| Client VM | `MRTG-CLIENT-01` |
| AD Computer Object | `CLIENT01` |
| Target OU | `OU=Workstations,OU=Computers,OU=_MRTG,DC=mrtg,DC=local` |
| LAPS GPO | `MRTG-GPO-Windows-LAPS-Workstation-Baseline` |
| LAPS Readers Group | `MRTG-GRP-LAPS-Password-Readers` |
| Management Tooling | ADUC, GPMC, PowerShell, Hyper-V |
| Validation Tools | `gpupdate`, `gpresult`, `Invoke-LapsPolicyProcessing`, `Get-LapsADPassword` |

---

## Key Concepts

This lab reinforced several IAM and endpoint security concepts:

- Local administrator passwords should not be reused across endpoints
- Local admin credentials are privileged credentials
- Windows LAPS allows local administrator passwords to be rotated and stored centrally
- LAPS access should be delegated only to authorized administrators
- Password retrieval should be controlled through dedicated groups
- Schema changes should not be performed when domain replication is unhealthy
- Group Policy should target the correct OU instead of applying broadly
- Privileged password evidence should be documented without exposing secrets

---

## Architecture

```text
MRTG Active Directory Domain
└── _MRTG
    ├── Computers
    │   └── Workstations
    │       └── CLIENT01
    │
    └── Groups
        └── MRTG-GRP-LAPS-Password-Readers
```

Windows LAPS flow:

```text
CLIENT01
↓
Receives Windows LAPS GPO
↓
Processes LAPS policy
↓
Rotates local Administrator password
↓
Backs password metadata up to Active Directory
↓
Authorized admin retrieves password through delegated LAPS permissions
```

---

## Security Model

This lab used a scoped privileged access model.

```text
Workstation computer objects
↓
Granted permission to update their own LAPS password attributes
↓
Dedicated LAPS readers group
↓
Delegated permission to retrieve LAPS passwords
↓
Administrator account added to readers group
```

The delegated password reset account from the previous lab was intentionally not added to the LAPS readers group.

Windows LAPS password retrieval is a higher-risk privileged action than resetting a standard user password. Access to retrieve local administrator credentials should be tightly controlled.

---

## Implementation Steps

### 1. Confirmed CLIENT01 Computer Object Placement

The `CLIENT01` computer object was confirmed inside the Workstations OU before applying Windows LAPS policy targeting.

![CLIENT01 in Workstations OU](screenshots/01-aduc-client01-computer-object-in-workstations-ou.png)

---

### 2. Verified Windows LAPS Was Initially Missing

Windows LAPS cmdlets were not initially available on `MRTG-DC01`.

The system was running Windows Server 2022, but the LAPS PowerShell module was not detected.

![Windows LAPS module not detected](screenshots/02a-dc01-windows-laps-module-not-detected.png)

This confirmed that the domain controller required an update before Windows LAPS configuration could continue.

---

### 3. Installed Required Windows Server Update and Verified LAPS Cmdlets

After installing `KB5030216`, the Windows LAPS PowerShell module became available.

Validated commands included:

```powershell
Get-Command *Laps*
Get-Module -ListAvailable LAPS
```

![KB5030216 installed and LAPS commands available](screenshots/02b-dc01-kb5030216-installed-and-laps-commands-available.png)

Key cmdlets verified included:

```powershell
Update-LapsADSchema
Set-LapsADComputerSelfPermission
Set-LapsADReadPasswordPermission
Get-LapsADPassword
Invoke-LapsPolicyProcessing
```

---

### 4. Created Pre-Schema Checkpoint

Before modifying the Active Directory schema, a Hyper-V checkpoint was created for `MRTG-DC01`.

![Pre-LAPS schema extension checkpoint](screenshots/03-hyperv-dc01-pre-laps-schema-extension-checkpoint.png)

This provided a rollback point before performing a Tier 0 schema-related change.

---

### 5. Verified Schema-Level Administrative Membership

After the first schema extension attempt failed, administrative group membership was verified.

The account was confirmed to have membership in:

```text
Domain Admins
Enterprise Admins
Schema Admins
```

![Schema admin membership check](screenshots/04a-dc01-schema-admin-membership-check.png)

This confirmed that the issue was not caused by missing Schema Admins membership.

---

### 6. Captured Initial Schema Extension Failure

The first Windows LAPS schema extension attempt failed while attempting to add the `ms-LAPS-Password` schema attribute.

![LAPS schema extension failed](screenshots/04b-dc01-laps-schema-extension-failed-operation-error.png)

This failure was treated as a troubleshooting signal instead of being ignored.

---

### 7. Identified Replication and DNS Health Issue

A replication health check showed DNS lookup failures involving `MRTG-DC02`.

![Replication DNS failure before schema retry](screenshots/04c-dc01-replication-dns-failure-before-laps-schema-retry.png)

The failure showed that Active Directory replication was not healthy enough to safely continue with schema-related work.

---

### 8. Restored Replication Health

After correcting the replication/DNS issue, replication health was rechecked.

![Replication health restored](screenshots/04d-dc01-replication-health-restored-before-laps-schema-retry.png)

Replication failures were cleared before continuing.

---

### 9. Verified Windows LAPS Schema Objects

After retrying the schema process, Windows LAPS schema objects were verified in Active Directory.

![Windows LAPS schema extension verified](screenshots/04e-dc01-windows-laps-schema-extension-verified.png)

Verified schema objects included:

```text
ms-LAPS-EncryptedDSRMPassword
ms-LAPS-EncryptedDSRMPasswordHistory
ms-LAPS-EncryptedPassword
ms-LAPS-EncryptedPasswordHistory
ms-LAPS-Password
ms-LAPS-PasswordExpirationTime
```

This confirmed that the Windows LAPS schema extension had completed successfully.

---

### 10. Configured Computer Self-Permissions on Workstations OU

Computer self-permissions were configured on the Workstations OU so workstation computer objects could update their own LAPS password attributes.

Command used:

```powershell
Set-LapsADComputerSelfPermission -Identity "OU=Workstations,OU=Computers,OU=_MRTG,DC=mrtg,DC=local"
```

![Workstations OU LAPS computer self-permission set](screenshots/05-workstations-ou-laps-computer-self-permission-set.png)

---

### 11. Created Dedicated LAPS Password Readers Group

A dedicated security group was created for LAPS password retrieval.

Group created:

```text
MRTG-GRP-LAPS-Password-Readers
```

![LAPS password readers group created](screenshots/06-laps-password-readers-group-created.png)

This group was created to avoid using broad administrative or help desk groups for privileged password retrieval.

---

### 12. Added Administrator to LAPS Password Readers Group

The primary administrator account was added to the LAPS password readers group.

![Admin added to LAPS password readers group](screenshots/07-admin-added-to-laps-password-readers-group.png)

The delegated help desk reset account from Lab 16 was intentionally not added.

---

### 13. Delegated LAPS Password Read Permission

Windows LAPS password read permissions were delegated to the dedicated readers group.

Command used:

```powershell
Set-LapsADReadPasswordPermission -Identity "OU=Workstations,OU=Computers,OU=_MRTG,DC=mrtg,DC=local" -AllowedPrincipals "MRTG\MRTG-GRP-LAPS-Password-Readers"
```

![LAPS read password permission delegated](screenshots/08-laps-read-password-permission-delegated.png)

This scoped LAPS password retrieval rights to workstation computer objects only.

---

### 14. Created and Linked Windows LAPS GPO

A new Group Policy Object was created and linked to the Workstations OU.

GPO name:

```text
MRTG-GPO-Windows-LAPS-Workstation-Baseline
```

![Windows LAPS GPO linked to Workstations OU](screenshots/09-gpmc-windows-laps-gpo-linked-to-workstations-ou.png)

This ensured that the LAPS policy targeted workstation endpoints instead of being applied broadly across the domain.

---

### 15. Configured Windows LAPS Policy Settings

The Windows LAPS policy settings were configured under:

```text
Computer Configuration
└── Policies
    └── Administrative Templates
        └── System
            └── LAPS
```

Configured settings included:

| Setting | State |
|---|---|
| Configure password backup directory | Enabled |
| Backup directory | Active Directory |
| Password Settings | Enabled |
| Password complexity | Large letters, small letters, numbers, and special characters |
| Password length | 16 |
| Password age | 30 days |

![Windows LAPS policy settings configured](screenshots/10-gpme-windows-laps-policy-settings-configured.png)

---

### 16. Applied Group Policy on CLIENT01

Group Policy was updated on `CLIENT01`.

Commands used:

```powershell
gpupdate /force
gpresult /r /scope computer
```

![CLIENT01 LAPS GPO applied with gpresult](screenshots/11-client01-laps-gpo-applied-with-gpresult.png)

The LAPS GPO appeared under Applied Group Policy Objects:

```text
MRTG-GPO-Windows-LAPS-Workstation-Baseline
```

---

### 17. Invoked Windows LAPS Policy Processing

Windows LAPS policy processing was manually invoked on `CLIENT01`.

Command used:

```powershell
Invoke-LapsPolicyProcessing -Verbose
```

![CLIENT01 LAPS policy processing invoked](screenshots/12-client01-laps-policy-processing-invoked.png)

The command completed successfully and confirmed that the client was domain-joined and able to process LAPS policy.

---

### 18. Retrieved LAPS Password Metadata from Active Directory

LAPS password metadata was retrieved from `MRTG-DC01`.

Command used:

```powershell
Get-LapsADPassword -Identity CLIENT01
```

A plaintext retrieval test was also performed, but the actual password was redacted from the screenshot.

![LAPS password retrieval metadata](screenshots/13-dc01-laps-password-retrieval-metadata.png)

The metadata confirmed:

```text
ComputerName: CLIENT01
Account: Administrator
Source: EncryptedPassword
DecryptionStatus: Success
```

The actual local administrator password was intentionally not exposed in documentation.

---

### 19. Created Post-Lab Checkpoints

Post-lab checkpoints were created for both `MRTG-DC01` and `MRTG-CLIENT-01`.

![MRTG-DC01 post-lab checkpoint](screenshots/14a-hyperv-dc01-post-lab17-checkpoint.png)

![MRTG-CLIENT-01 post-lab checkpoint](screenshots/14b-hyperv-client01-post-lab17-checkpoint.png)

Checkpoint name:

```text
Post-Lab-17-Windows-LAPS-and-Local-Administrator-Control-Validated
```

---

## Validation Results

| Validation Item | Result |
|---|---|
| CLIENT01 located in Workstations OU | Successful |
| Windows LAPS initially missing | Confirmed |
| KB5030216 installed | Successful |
| Windows LAPS PowerShell module available | Successful |
| Pre-schema checkpoint created | Successful |
| Schema admin membership verified | Successful |
| Initial schema extension issue identified | Successful |
| Replication/DNS issue identified | Successful |
| Replication health restored | Successful |
| Windows LAPS schema objects verified | Successful |
| Workstations OU computer self-permission configured | Successful |
| LAPS password readers group created | Successful |
| Administrator added to LAPS readers group | Successful |
| LAPS password read permission delegated | Successful |
| LAPS GPO created and linked | Successful |
| LAPS policy settings configured | Successful |
| CLIENT01 received LAPS GPO | Successful |
| LAPS policy processing completed | Successful |
| LAPS password metadata retrieved | Successful |
| Plaintext password protected from documentation | Successful |
| Post-lab checkpoints created | Successful |

---

## Commands Used

### Check Windows LAPS Availability

```powershell
Get-Command *Laps*
Get-Module -ListAvailable LAPS
Test-Path "$env:windir\System32\WindowsPowerShell\v1.0\Modules\LAPS"
Get-ComputerInfo | Select-Object WindowsProductName, OsVersion, OsBuildNumber
```

### Confirm Installed Update

```powershell
(Get-ItemProperty "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion").UBR
Get-HotFix -Id KB5030216
```

### Verify Administrative Group Membership

```powershell
whoami
whoami /groups | findstr /i "Schema Enterprise Domain"
net group "Schema Admins" /domain
net group "Enterprise Admins" /domain
net group "Domain Admins" /domain
```

### Check AD Replication Health

```powershell
netdom query fsmo
repadmin /replsummary
dcdiag /test:advertising /test:services /test:replications
```

### Verify LAPS Schema Objects

```powershell
$schema = (Get-ADRootDSE).schemaNamingContext

Get-ADObject -SearchBase $schema -Filter 'Name -like "ms-LAPS-*"' |
Select-Object Name,ObjectClass
```

### Configure LAPS OU Permissions

```powershell
Set-LapsADComputerSelfPermission -Identity "OU=Workstations,OU=Computers,OU=_MRTG,DC=mrtg,DC=local"
```

### Delegate LAPS Password Read Permission

```powershell
Set-LapsADReadPasswordPermission -Identity "OU=Workstations,OU=Computers,OU=_MRTG,DC=mrtg,DC=local" -AllowedPrincipals "MRTG\MRTG-GRP-LAPS-Password-Readers"
```

### Apply and Validate Group Policy on CLIENT01

```powershell
gpupdate /force
gpresult /r /scope computer
```

### Invoke LAPS Processing

```powershell
Invoke-LapsPolicyProcessing -Verbose
```

### Retrieve LAPS Password Metadata

```powershell
Get-LapsADPassword -Identity CLIENT01
```

---

## Troubleshooting Notes

### Issue 1: Windows LAPS Cmdlets Were Missing

The LAPS cmdlets were not initially available on `MRTG-DC01`.

The server required an offline Windows update before Windows LAPS could be configured.

Resolution:

```text
Installed KB5030216
Verified UBR 1970
Confirmed LAPS PowerShell module availability
```

---

### Issue 2: Initial Schema Extension Failed

The first schema extension attempt failed while adding the `ms-LAPS-Password` schema attribute.

Administrative group membership was checked and confirmed that the Administrator account had the necessary privileged group memberships.

This confirmed the issue was not caused by missing Schema Admins membership.

---

### Issue 3: Replication/DNS Failure

Replication health checks showed DNS lookup failures involving `MRTG-DC02`.

The schema extension was paused until replication health was corrected.

Resolution:

```text
Started MRTG-DC02
Corrected DC/DNS registration behavior
Confirmed replication health with repadmin /replsummary
Retried and verified LAPS schema objects
```

---

## Security Notes

This lab intentionally avoided exposing the managed local administrator password in documentation.

The screenshot showing LAPS password retrieval was redacted to protect the credential.

This is important because LAPS passwords are privileged secrets. Even in a lab, they should be treated like real administrative credentials.

---

## IAM and Security Takeaways

This lab reinforced a core IAM principle:

```text
Privileged access must be controlled, scoped, rotated, and auditable.
```

Windows LAPS reduces the risk of shared or reused local administrator passwords across endpoints.

From an IAM perspective, this lab showed how local endpoint privilege can be managed through:

- Active Directory schema support
- OU-scoped permissions
- Dedicated reader groups
- Group Policy targeting
- Controlled password retrieval
- Validation without credential exposure

This is directly relevant to enterprise endpoint security and government-regulated environments where privileged access must be controlled and documented.

---

## Outcome

The MRTG environment now has Windows LAPS configured for workstation local administrator password management.

`CLIENT01` successfully received the Windows LAPS policy, processed the policy, backed up local administrator password metadata to Active Directory, and allowed controlled retrieval by an authorized administrator.

The lab also documented a realistic troubleshooting path involving missing LAPS components, offline update installation, schema extension failure, replication/DNS repair, and final validation.

---

## Next Lab

Lab-18 will focus on group-based access control for file and department resources.

The next lab will build on prior IAM concepts by using AD security groups to control access to shared resources through a scalable authorization model.
