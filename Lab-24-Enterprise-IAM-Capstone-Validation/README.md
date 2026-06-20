# Lab 24 - Enterprise IAM Capstone Validation

![Platform](https://img.shields.io/badge/Platform-Windows%20Server-blue)
![Technology](https://img.shields.io/badge/Technology-Active%20Directory-blue)
![Tooling](https://img.shields.io/badge/Tooling-PowerShell-purple)
![Focus](https://img.shields.io/badge/Focus-Enterprise%20IAM%20Capstone-orange)
![Security](https://img.shields.io/badge/Security-End--to--End%20Validation-red)
![Validation](https://img.shields.io/badge/Validation-Full%20IAM%20Series-brightgreen)

---

## Overview

In this lab, I performed a final capstone validation of the Monroe Redstone Technology Group Enterprise IAM Lab Series.

The lab validated the complete identity environment, including domain health, domain controller discovery, replication, FSMO role ownership, directory structure, Group Policy security baselines, delegated administration, Windows LAPS, Active Directory Certificate Services, identity lifecycle automation, recovery artifacts, audit evidence, and operational handoff documentation.

The goal was to prove that the MRTG environment functions as one connected enterprise IAM platform rather than a collection of isolated configurations.

---

## Business Problem

MRTG needed to verify that the identity platform remained healthy, secure, recoverable, auditable, and operationally supportable after completing the full lab series.

Individual configurations can appear successful while dependencies between them are unhealthy or undocumented. A complete IAM environment requires working directory services, controlled access, enforced security policies, trusted certificate services, repeatable lifecycle processes, recovery evidence, audit records, and operational documentation.

This capstone addressed that problem by validating the major technical and operational components as one integrated identity system.

---

## Lab Summary

I began by creating a pre-lab Hyper-V checkpoint and a dedicated capstone workspace.

I validated domain controller health, discovery, replication, FSMO role ownership, domain and forest configuration, organizational units, groups, users, Group Policy Objects, security baseline inheritance, delegated administration, privileged groups, LAPS-related groups, and the AD CS Enterprise CA.

I then validated the identity lifecycle automation artifacts from Lab 20, recovery artifacts from Lab 21, audit evidence from Lab 22, and the operational handoff package from Lab 23.

Finally, I created a written capstone validation summary and a post-lab checkpoint.

---

## Objectives

- Create pre-lab and post-lab Hyper-V checkpoints
- Create a dedicated Lab 24 workspace
- Validate domain controller health and discovery
- Validate Active Directory replication
- Validate FSMO role ownership
- Validate domain and forest configuration
- Validate OU, group, and user structure
- Validate Group Policy and security baselines
- Validate delegated administration and privileged groups
- Validate Windows LAPS-related access groups
- Validate AD CS Enterprise CA availability
- Validate identity lifecycle automation artifacts
- Validate backup and recovery artifacts
- Validate IAM audit evidence
- Validate operational handoff documentation
- Create a final capstone validation summary

---

## Scope

### Included

- Active Directory health validation
- Domain controller discovery
- Replication validation
- FSMO role validation
- Domain and forest configuration review
- Identity structure review
- Group Policy review
- Security baseline inheritance review
- Delegated and privileged access review
- LAPS group review
- AD CS service and connectivity validation
- Identity automation artifact validation
- Recovery artifact validation
- Audit evidence validation
- Operational documentation validation
- Capstone report creation

### Not Included

- Major Active Directory configuration changes
- Destructive recovery testing
- Certificate enrollment testing
- Certificate template permission remediation
- Production security control certification
- Penetration testing
- Formal compliance assessment
- SIEM integration validation
- Business owner access certification
- Disaster recovery exercise
- Production readiness approval

---

## Environment

| Component | Details |
|---|---|
| Organization | Monroe Redstone Technology Group |
| Domain | `mrtg.local` |
| Primary Domain Controller | `MRTG-DC01` |
| Additional Domain Controller | `MRTG-DC02` |
| Site | `MRTG-HQ-Site` |
| Domain Mode | `Windows2016Domain` |
| Forest Mode | `Windows2016Forest` |
| Enterprise CA | `MRTG-DC01\MRTG-DC01-CA` |
| Lab Root Path | `C:\MRTG-Labs\Lab-24-Enterprise-IAM-Capstone-Validation` |
| Output Path | `C:\MRTG-Labs\Lab-24-Enterprise-IAM-Capstone-Validation\output` |
| Evidence Path | `C:\MRTG-Labs\Lab-24-Enterprise-IAM-Capstone-Validation\evidence` |
| Reports Path | `C:\MRTG-Labs\Lab-24-Enterprise-IAM-Capstone-Validation\reports` |
| References Path | `C:\MRTG-Labs\Lab-24-Enterprise-IAM-Capstone-Validation\references` |
| Tools | PowerShell, DCDIAG, REPADMIN, NLTEST, NETDOM, CertUtil, Hyper-V Manager |
| Hypervisor | Hyper-V |

---

## Scenario

MRTG has completed an enterprise-style IAM lab series containing:

- Active Directory Domain Services
- Redundant domain controllers
- Organizational Units
- Department-based security groups
- Standard, administrative, and service accounts
- Group Policy controls
- Security baselines
- Delegated administration
- Windows LAPS
- Active Directory Certificate Services
- Identity lifecycle automation
- Backup and recovery artifacts
- IAM audit evidence
- Operational handoff documentation

The capstone validation model was:

```text
Validate Infrastructure → Validate Access Control → Validate Security Services → Validate Automation → Validate Recovery → Validate Audit → Validate Handoff
```

This lab did not introduce major new configurations. It validated that the environment built across Labs 01 through 23 remained functional, documented, and supportable.

---

## Capstone Validation Design

| Validation Area | Purpose |
|---|---|
| Domain Health | Confirm critical domain controller services were healthy |
| Domain Controller Discovery | Confirm both domain controllers were discoverable |
| Replication | Confirm Active Directory replication was successful |
| FSMO Roles | Confirm critical role ownership |
| Identity Structure | Confirm OUs, groups, and users remained intact |
| Group Policy | Confirm security baseline GPOs were present and linked |
| Privileged Access | Confirm administrative access remained reviewable |
| Windows LAPS | Confirm password-reader and security groups existed |
| AD CS | Confirm the Enterprise CA was running and reachable |
| Identity Automation | Confirm Joiner, Mover, and Leaver artifacts existed |
| Recovery | Confirm backup and recovery evidence existed |
| IAM Audit | Confirm access review exports and reports existed |
| Operational Handoff | Confirm SOPs, runbooks, and handoff documents existed |
| Capstone Report | Summarize final validation results |

---

## Implementation Steps

### Step 1 - Created Pre-Lab Checkpoint

A Hyper-V checkpoint was created before beginning the capstone validation.

Checkpoint name:

```text
MRTG-DC01_Pre-Lab-24-Enterprise-IAM-Capstone-Validation
```

![Pre-Lab 24 Checkpoint](screenshots/lab-24-01-pre-lab-24-checkpoint.png)

---

### Step 2 - Created Lab 24 Folder Structure

A dedicated Lab 24 workspace was created on `MRTG-DC01`.

Folder structure:

```text
C:\MRTG-Labs\Lab-24-Enterprise-IAM-Capstone-Validation
├── evidence
├── output
├── references
└── reports
```

![Lab 24 Folder Structure Created](screenshots/lab-24-02-folder-structure-created.png)

---

## Core Active Directory Validation

### Step 3 - Validated Domain Health

Domain health was validated with `dcdiag` and `repadmin`.

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

![Domain Health Capstone Validated](screenshots/lab-24-03-domain-health-capstone-validated.png)

---

### Step 4 - Validated Domain Controller Discovery and Replication

Domain controller discovery and detailed replication status were validated.

Commands used:

```powershell
nltest /dsgetdc:mrtg.local
$env:LOGONSERVER

Get-ADDomainController -Filter * |
Select-Object HostName,IPv4Address,Site,IsGlobalCatalog |
Format-Table -AutoSize

repadmin /showrepl MRTG-DC01
```

Validated domain controllers:

| Domain Controller | IP Address | Site | Global Catalog |
|---|---|---|---|
| `MRTG-DC01.mrtg.local` | `192.168.10.10` | `MRTG-HQ-Site` | True |
| `MRTG-DC02.mrtg.local` | `192.168.10.11` | `MRTG-HQ-Site` | True |

Replication attempts for the directory partitions completed successfully.

![Domain Controller Discovery and Replication Validated](screenshots/lab-24-04-domain-controller-discovery-and-replication-validated.png)

---

### Step 5 - Validated FSMO Roles and Domain Structure

FSMO role ownership, domain mode, and forest mode were validated.

Commands used:

```powershell
netdom query fsmo

Get-ADDomain |
Select-Object DNSRoot,DomainMode,InfrastructureMaster,PDCEmulator,RIDMaster |
Format-List

Get-ADForest |
Select-Object Name,ForestMode,SchemaMaster,DomainNamingMaster |
Format-List
```

Validated FSMO roles:

| FSMO Role | Role Holder |
|---|---|
| Schema Master | `MRTG-DC01.mrtg.local` |
| Domain Naming Master | `MRTG-DC01.mrtg.local` |
| PDC Emulator | `MRTG-DC01.mrtg.local` |
| RID Master | `MRTG-DC01.mrtg.local` |
| Infrastructure Master | `MRTG-DC01.mrtg.local` |

Validated directory configuration:

| Item | Value |
|---|---|
| DNS Root | `mrtg.local` |
| Domain Mode | `Windows2016Domain` |
| Forest Mode | `Windows2016Forest` |

![FSMO and Domain Structure Validated](screenshots/lab-24-05-fsmo-and-domain-structure-validated.png)

---

## Identity Structure Validation

### Step 6 - Validated OU, Group, and User Structure

Organizational Units, department groups, and user accounts were reviewed.

Commands used:

```powershell
Get-ADOrganizationalUnit -Filter * |
Select-Object Name,DistinguishedName |
Sort-Object Name |
Format-Table -AutoSize

Get-ADGroup -Filter 'Name -like "GG_*_Users"' |
Select-Object Name,GroupScope,GroupCategory,DistinguishedName |
Sort-Object Name |
Format-Table -AutoSize

Get-ADUser -Filter * -Properties Enabled,Department,Title |
Select-Object Name,SamAccountName,Enabled,Department,Title |
Sort-Object Name |
Format-Table -AutoSize
```

Validation confirmed:

- Department OUs existed
- Computer OUs existed
- The Groups OU existed
- The Service Accounts OU existed
- Department-based `GG_*_Users` groups existed
- Standard, administrative, and service accounts were visible
- `maya.reed` remained disabled after the Lab 20 leaver workflow
- `ethan.walker` remained active in the Security department after the mover workflow

![OU Group and User Structure Validated](screenshots/lab-24-06-ou-group-and-user-structure-validated.png)

---

## Policy and Access Control Validation

### Step 7 - Validated GPOs and Security Baselines

Group Policy Objects and baseline inheritance were validated.

Commands used:

```powershell
Get-GPO -All |
Select-Object DisplayName,GpoStatus,CreationTime,ModificationTime |
Sort-Object DisplayName |
Format-Table -AutoSize

Get-GPInheritance -Target "OU=Workstations,OU=Computers,OU=_MRTG,DC=mrtg,DC=local"

Get-GPInheritance -Target "OU=Servers,OU=Computers,OU=_MRTG,DC=mrtg,DC=local"
```

Validated GPOs included:

```text
Default Domain Controllers Policy
Default Domain Policy
MRTG-DC-Identity-Auditing
MRTG-DC-Logon-Validation
MRTG-GPO-Centralized-Event-Forwarding
MRTG-GPO-Server-Security-Baseline
MRTG-GPO-Windows-LAPS-Workstation-Baseline
MRTG-GPO-Workstation-Security-Baseline
MRTG-User-Session-Lock
MRTG-Workstation-Baseline
```

Validated inheritance:

| OU | Linked or Inherited GPOs |
|---|---|
| Workstations | `MRTG-GPO-Workstation-Security-Baseline`, `MRTG-GPO-Windows-LAPS-Workstation-Baseline`, `Default Domain Policy` |
| Servers | `MRTG-GPO-Server-Security-Baseline`, `Default Domain Policy` |

![GPO and Security Baselines Validated](screenshots/lab-24-07-gpo-and-security-baselines-validated.png)

---

### Step 8 - Validated Delegation and Privileged Groups

Delegated administration and privileged group membership were reviewed.

Command used:

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

Validated membership:

| Group | Member |
|---|---|
| Administrators | Domain Admins |
| Administrators | Enterprise Admins |
| Administrators | Administrator |
| Schema Admins | Administrator |
| Enterprise Admins | Administrator |
| Domain Admins | Administrator |
| `GG_IT_HelpDesk_Admins` | `john.smith.admin` |
| `GG_PSO_Privileged_Admins` | `john.smith.admin` |
| `MRTG-GRP-Helpdesk-Password-Reset-Delegated` | `adm.hd-reset01` |

The review confirmed that delegated help desk access remained separated from Domain Admins membership.

![Delegation and Privileged Groups Validated](screenshots/lab-24-08-delegation-and-privileged-groups-validated.png)

---

### Step 9 - Listed LAPS and Endpoint Security Groups

LAPS-related and endpoint security groups were identified.

Command used:

```powershell
Get-ADGroup -Filter 'Name -like "*LAPS*" -or Name -like "*Security*" -or Name -like "*Privileged*"' |
Select-Object Name,GroupScope,GroupCategory,DistinguishedName |
Sort-Object Name |
Format-Table -AutoSize
```

Validated groups:

```text
GG_PSO_Privileged_Admins
GG_Security_Users
MRTG-GRP-LAPS-Password-Readers
```

![LAPS and Endpoint Security Groups Listed](screenshots/lab-24-09-laps-and-endpoint-security-groups-listed.png)

---

### Step 10 - Validated LAPS and Endpoint Security Group Membership

Membership of the LAPS and security-related groups was reviewed.

Command used:

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

Validated membership:

| Group | Members |
|---|---|
| `GG_Security_Users` | Alex Rivera, Ethan Walker |
| `GG_PSO_Privileged_Admins` | `john.smith.admin` |
| `MRTG-GRP-LAPS-Password-Readers` | Administrator |

![LAPS and Endpoint Security Group Membership Validated](screenshots/lab-24-10-laps-and-endpoint-security-group-membership-validated.png)

---

## Enterprise Trust Services Validation

### Step 11 - Validated AD CS Enterprise CA

Active Directory Certificate Services was validated.

Commands used:

```powershell
Get-Service CertSvc

certutil -config "MRTG-DC01\MRTG-DC01-CA" -ping

certutil -catemplates
```

Validation confirmed:

```text
CertSvc status: Running
CA configuration: MRTG-DC01\MRTG-DC01-CA
Certificate Services RPC interface: Alive
certutil -ping: Completed successfully
certutil -catemplates: Completed successfully
```

Some templates displayed `Access is denied` for auto-enrollment. This did not indicate a CA service outage. It showed that the current security context did not have auto-enrollment permission for those templates.

The validation confirmed service availability and CA connectivity, but it did not validate successful certificate enrollment.

![AD CS Enterprise CA Validated](screenshots/lab-24-11-adcs-enterprise-ca-validated.png)

---

## Identity Lifecycle Automation Validation

### Step 12 - Validated Identity Lifecycle Automation Artifacts

Identity lifecycle automation artifacts from Lab 20 were validated.

Commands used:

```powershell
$Lab20 = "C:\MRTG-Labs\Lab-20-Identity-Lifecycle-Automation"

Get-ChildItem "$Lab20\scripts"
Get-ChildItem "$Lab20\data"
Get-ChildItem "$Lab20\output"

Import-Csv "$Lab20\output\joiner-results.csv" |
Select-Object SamAccountName,UserStatus,GroupStatus,Result |
Format-Table -AutoSize

Import-Csv "$Lab20\output\mover-results.csv" |
Select-Object SamAccountName,NewDepartment,NewGroupName,Result |
Format-Table -AutoSize

Import-Csv "$Lab20\output\leaver-results.csv" |
Select-Object SamAccountName,AccountStatus,GroupStatus,Result |
Format-Table -AutoSize
```

Validated scripts:

```text
New-MRTGUsers.ps1
Move-MRTGUser.ps1
Disable-MRTGUser.ps1
```

Validated data files:

```text
new-users.csv
mover-users.csv
leaver-users.csv
```

Validated output files:

```text
joiner-results.csv
mover-results.csv
leaver-results.csv
```

Workflow results:

| Workflow | Result |
|---|---|
| Joiner | Success |
| Mover | Success |
| Leaver | Success |

![Identity Lifecycle Automation Artifacts Validated](screenshots/lab-24-12-identity-lifecycle-automation-artifacts-validated.png)

---

## Recovery, Audit, and Handoff Validation

### Step 13 - Validated Backup, Recovery, and Audit Artifacts

Recovery artifacts from Lab 21 and audit artifacts from Lab 22 were validated.

Commands used:

```powershell
$Lab21 = "C:\MRTG-Labs\Lab-21-Directory-Recovery-Backup-Operational-Resilience"
$Lab22 = "C:\MRTG-Labs\Lab-22-IAM-Security-Review-and-Access-Control-Audit"

Get-ChildItem "$Lab21\output"
Get-ChildItem "$Lab21\gpo-backup" |
Select-Object -First 5 Name,LastWriteTime
Get-ChildItem "$Lab21\runbook"

Get-ChildItem "$Lab22\output"
Get-ChildItem "$Lab22\reports"
```

Validated Lab 21 artifacts:

```text
ad-group-inventory.csv
ad-ou-inventory.csv
ad-user-inventory.csv
gpo-inventory.csv
privileged-group-membership.csv
GPO backup folders
MRTG-Directory-Recovery-Runbook.md
```

Validated Lab 22 artifacts:

```text
adcs-groups-review.csv
delegated-admin-groups-review.csv
department-groups-review.csv
disabled-accounts-review.csv
domain-admins-review.csv
laps-security-groups-review.csv
privileged-groups-review.csv
user-account-review.csv
MRTG-IAM-Security-Review-Summary.md
```

![Backup Recovery and Audit Artifacts Validated](screenshots/lab-24-13-backup-recovery-and-audit-artifacts-validated.png)

---

### Step 14 - Validated Operational Handoff Package

The operational handoff package from Lab 23 was validated.

Commands used:

```powershell
$Lab23 = "C:\MRTG-Labs\Lab-23-IAM-Runbooks-SOPs-Operational-Handoff"

Get-ChildItem "$Lab23\sops"
Get-ChildItem "$Lab23\runbooks"
Get-ChildItem "$Lab23\handoff"
Get-ChildItem "$Lab23\references"
```

Validated SOPs:

```text
MRTG-Access-Request-SOP.md
MRTG-Joiner-Mover-Leaver-SOP.md
MRTG-Password-Reset-SOP.md
MRTG-Privileged-Access-Review-SOP.md
```

Validated runbook:

```text
MRTG-Directory-Recovery-Reference.md
```

Validated handoff documents:

```text
MRTG-Admin-Responsibility-Matrix.md
MRTG-IAM-Documentation-Index.md
MRTG-Operational-Handoff-Summary.md
```

Validated references:

```text
Lab-20-Identity-Lifecycle-Output
Lab-21-Recovery-Runbook
Lab-22-Audit-Exports
Lab-22-Security-Review-Reports
```

![Operational Handoff Package Validated](screenshots/lab-24-14-operational-handoff-package-validated.png)

---

## Capstone Report

### Step 15 - Created Capstone Validation Summary

A final capstone validation summary was created.

Report file:

```text
C:\MRTG-Labs\Lab-24-Enterprise-IAM-Capstone-Validation\reports\MRTG-Enterprise-IAM-Capstone-Validation-Summary.md
```

The report documented:

- Domain controller health
- Domain controller discovery
- Active Directory replication
- FSMO role ownership
- OU, group, and user structure
- Group Policy and security baselines
- Delegated administration and privileged groups
- Windows LAPS and endpoint security groups
- Active Directory Certificate Services
- Identity lifecycle automation
- Backup and recovery artifacts
- IAM audit evidence
- Operational handoff documentation
- Final capstone outcome

![Capstone Validation Summary Created](screenshots/lab-24-15-capstone-validation-summary-created.png)

---

### Step 16 - Created Post-Lab Checkpoint

A post-lab checkpoint was created after completing the capstone validation.

Checkpoint name:

```text
MRTG-DC01_Post-Lab-24-Enterprise-IAM-Capstone-Validation
```

![Post-Lab 24 Checkpoint](screenshots/lab-24-16-post-lab-24-checkpoint.png)

---

## Key Validation Results

| Validation Area | Result |
|---|---|
| Domain health | Critical `dcdiag` tests passed |
| Replication | Both domain controllers reported `0 / 5` failures |
| Domain controller discovery | DC01 and DC02 were discovered as Global Catalog servers |
| FSMO roles | All five roles were documented on DC01 |
| Identity structure | OUs, users, groups, and service accounts were visible |
| Group Policy | Ten GPOs and baseline inheritance were reviewed |
| Privileged access | Administrative and delegated memberships were documented |
| Windows LAPS | Password-reader and related security groups were reviewed |
| AD CS | CA service was running and its RPC interface was reachable |
| Identity automation | Joiner, Mover, and Leaver artifacts showed successful results |
| Recovery readiness | Inventory, GPO backups, and the recovery runbook existed |
| IAM audit | Eight CSV exports and the audit summary existed |
| Operational handoff | SOPs, runbook, ownership matrix, index, and summary existed |

---

## Validation Summary

| Test | Expected Result | Actual Result | Status |
|---|---|---|---|
| Pre-lab checkpoint created | Checkpoint exists before validation | Checkpoint created | Passed |
| Lab folder structure created | Required folders exist | Four folders validated | Passed |
| Domain health validated | Critical DCDIAG tests pass | Tests passed | Passed |
| Replication validated | Failures show `0 / 5` | DC01 and DC02 showed zero failures | Passed |
| DC discovery validated | Both domain controllers discoverable | DC01 and DC02 discovered | Passed |
| FSMO roles validated | Role ownership documented | All roles held by DC01 | Passed |
| Domain and forest validated | Modes and DNS root visible | Configuration validated | Passed |
| Identity structure validated | OUs, groups, and users visible | Structure validated | Passed |
| GPO baselines validated | GPOs and inheritance visible | Baselines reviewed | Passed |
| Privileged groups validated | Membership visible | Membership documented | Passed |
| LAPS groups validated | Groups and members visible | Membership documented | Passed |
| AD CS availability validated | CA running and reachable | Service and RPC available | Passed |
| Lifecycle artifacts validated | Scripts, data, and outputs exist | Artifacts validated | Passed |
| Recovery artifacts validated | Recovery evidence exists | Artifacts validated | Passed |
| IAM audit evidence validated | Exports and report exist | Evidence validated | Passed |
| Operational handoff validated | Required documents exist | Package validated | Passed |
| Capstone summary created | Final report exists | Report created | Passed |
| Post-lab checkpoint created | Checkpoint exists after validation | Checkpoint created | Passed |

---

## Evidence Collected

| Evidence | Screenshot |
|---|---|
| Pre-lab checkpoint | `screenshots/lab-24-01-pre-lab-24-checkpoint.png` |
| Lab folder structure | `screenshots/lab-24-02-folder-structure-created.png` |
| Domain health validation | `screenshots/lab-24-03-domain-health-capstone-validated.png` |
| Domain controller discovery and replication | `screenshots/lab-24-04-domain-controller-discovery-and-replication-validated.png` |
| FSMO roles and domain structure | `screenshots/lab-24-05-fsmo-and-domain-structure-validated.png` |
| OU, group, and user structure | `screenshots/lab-24-06-ou-group-and-user-structure-validated.png` |
| GPO and security baselines | `screenshots/lab-24-07-gpo-and-security-baselines-validated.png` |
| Delegation and privileged groups | `screenshots/lab-24-08-delegation-and-privileged-groups-validated.png` |
| LAPS and endpoint security groups | `screenshots/lab-24-09-laps-and-endpoint-security-groups-listed.png` |
| LAPS and security group membership | `screenshots/lab-24-10-laps-and-endpoint-security-group-membership-validated.png` |
| AD CS Enterprise CA | `screenshots/lab-24-11-adcs-enterprise-ca-validated.png` |
| Identity lifecycle automation | `screenshots/lab-24-12-identity-lifecycle-automation-artifacts-validated.png` |
| Backup, recovery, and audit artifacts | `screenshots/lab-24-13-backup-recovery-and-audit-artifacts-validated.png` |
| Operational handoff package | `screenshots/lab-24-14-operational-handoff-package-validated.png` |
| Capstone validation summary | `screenshots/lab-24-15-capstone-validation-summary-created.png` |
| Post-lab checkpoint | `screenshots/lab-24-16-post-lab-24-checkpoint.png` |

---

## Troubleshooting Notes

No critical domain health or replication failures were observed during the final validation.

The AD CS template listing displayed `Access is denied` for auto-enrollment on several templates. The CA service remained running, the RPC interface responded successfully, and `certutil -catemplates` completed.

This result validated CA availability but did not prove that the current user could enroll for every listed certificate template.

A production validation would separately test:

- Certificate enrollment
- Template permissions
- Auto-enrollment policy
- Certificate issuance
- Certificate revocation
- CA audit logging
- Renewal and expiration monitoring

---

## Security Concepts Reinforced

- Defense in depth
- Identity governance
- Least privilege
- Delegated administration
- Privileged access review
- Group-based access control
- Security baseline enforcement
- Windows LAPS governance
- Enterprise certificate trust
- Identity lifecycle automation
- Recovery readiness
- Audit readiness
- Evidence retention
- Operational handoff
- Continuous control validation

---

## Real-World Relevance

Enterprise IAM work is not complete because individual components are configured.

Identity systems must operate together. Domain controllers must be healthy, replication must function, groups and OUs must remain structured, policies must apply, privileged access must be controlled, certificate services must support trust, automation must retain evidence, backups must be recoverable, audits must be documented, and operational procedures must be available.

This lab connects directly to IAM, GovTech, and regulated IT responsibilities:

- Environment-wide validation
- Evidence-based administration
- Operational readiness
- Security control review
- Recovery readiness
- Audit readiness
- Knowledge transfer
- Repeatable PowerShell validation
- Administrative accountability

The key lesson is that mature IAM is a connected operating model, not one product or task.

---

## Security Considerations

This lab validated the current state of the MRTG environment but did not certify it as production-ready.

Production improvements should include:

- Formal security control mapping
- Approved architecture baselines
- Automated domain health checks
- Scheduled privileged access reviews
- SIEM monitoring for identity events
- Centralized GPO backup retention
- Certificate template permission reviews
- Certificate enrollment testing
- Isolated restore testing
- Automated lifecycle reporting
- Service account ownership reviews
- Break-glass account validation
- Disaster recovery exercises
- Controlled documentation storage
- Change ticket references
- Evidence retention policies
- Formal system owner approval

---

## Lessons Learned

- Domain health is foundational to every IAM process
- Replication must be healthy before directory data can be trusted
- FSMO role ownership should remain documented
- Identity structures must remain visible and reviewable
- GPO links and inheritance require validation
- Delegated groups support least privilege when regularly reviewed
- LAPS password-reader groups must be treated as privileged access
- AD CS is part of enterprise identity trust
- CA availability does not automatically prove enrollment authorization
- Automation is stronger when scripts, inputs, and outputs are retained
- Recovery and audit artifacts are part of operational maturity
- Handoff documentation makes the environment supportable
- Capstone validation proves that individual labs operate as a connected system

---

## What I Would Do Differently in Production

In a production or government-regulated environment, I would expand this capstone into a formal IAM operational readiness review.

A stronger production design would include:

- Security control mapping
- Defined evidence retention standards
- Automated validation reports
- Scheduled access certifications
- Change management references
- Formal system owner approval
- Service account ownership and credential rotation
- Certificate template permission reviews
- GPO backup schedules
- Recovery testing schedules
- Privileged access monitoring
- Identity event forwarding to a SIEM
- Formal SOP approval workflows
- Quarterly operational readiness reviews
- Annual disaster recovery exercises
- Documented remediation tracking

For this lab, the goal was to validate the MRTG Enterprise IAM Lab Series as a connected, documented, and operationally supportable identity environment.

---

## Skills Demonstrated

- Enterprise IAM capstone validation
- Active Directory health validation
- Domain controller discovery
- Replication validation
- FSMO role validation
- Domain and forest review
- OU, group, and user review
- Group Policy validation
- Security baseline validation
- Delegated administration review
- Privileged access review
- Windows LAPS group validation
- AD CS Enterprise CA validation
- Identity lifecycle automation validation
- Backup and recovery evidence validation
- IAM audit evidence validation
- Operational handoff validation
- PowerShell-based enterprise validation
- Technical documentation
- Evidence review

---

## Outcome

Lab 24 successfully validated the MRTG Enterprise IAM environment as one connected identity platform.

The capstone confirmed healthy domain controllers, working replication, documented FSMO roles, structured OUs, users and groups, Group Policy security baselines, delegated administration, Windows LAPS-related access control, AD CS availability, lifecycle automation evidence, recovery artifacts, IAM audit evidence, and operational handoff documentation.

The final result was a validated enterprise IAM lab environment demonstrating the identity administration lifecycle from infrastructure deployment through security, automation, recovery, auditing, and operational handoff.

---

## Series Completion

The MRTG Enterprise IAM Lab Series is complete.

The series demonstrated:

```text
Identity infrastructure
Active Directory administration
OU and group design
Group Policy enforcement
Password and account lockout policy
DHCP integration
Replication topology
Centralized logging
Security baselines
Delegated administration
Windows LAPS
Group-based access control
Active Directory Certificate Services
Identity lifecycle automation
Directory backup and recovery
IAM security review
Operational handoff
Enterprise IAM capstone validation
```

The completed series represents a practical enterprise-style IAM environment built from the ground up and validated through security, automation, recovery, audit, and operations.
