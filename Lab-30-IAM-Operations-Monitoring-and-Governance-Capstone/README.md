# Lab 30: IAM Operations, Monitoring, and Governance Capstone

![Platform](https://img.shields.io/badge/Platform-Windows%20Server-blue)
![Technology](https://img.shields.io/badge/Technology-Active%20Directory-blue)
![Technology](https://img.shields.io/badge/Technology-Splunk%20Enterprise-purple)
![Focus](https://img.shields.io/badge/Focus-IAM%20Operations-green)
![Security](https://img.shields.io/badge/Security-Governance%20Review-red)
![Validation](https://img.shields.io/badge/Validation-Controls%20Reviewed-brightgreen)
![Documentation](https://img.shields.io/badge/Documentation-Evidence%20Captured-purple)

---

## Overview

This capstone reviewed identity, endpoint, automation, and monitoring controls implemented across Labs 25 through 29 in the Monroe Redstone Technology Group environment.

The review covered:

- Service account governance
- Least-privilege scheduled-task automation
- BitLocker endpoint encryption
- Local administrator remediation
- Splunk identity-event monitoring
- Configuration evidence
- Temporary lab-state checkpoints

The purpose was to determine whether the controls remained visible and reviewable after implementation while identifying areas that required additional operational validation.

---

## Business Problem

MRTG needed to verify that previously implemented identity and security controls had not become stale, misconfigured, or unnecessarily privileged.

A control provides limited long-term value if the organization does not periodically confirm that:

- The identity still has a valid purpose
- Responsibility remains documented
- Assigned permissions remain appropriate
- Automation still uses the intended identity
- Endpoint encryption remains active
- Removed privileged access has not returned
- Monitoring services remain available
- Detection searches still execute
- Configuration changes are traceable
- Evidence remains available for review

This lab addressed that requirement through a structured operational governance review.

---

## Lab Summary

Pre-lab checkpoints were created for `MRTG-DC01`, `MRTG-LOG01`, and the `MRTG-CLIENT-01` virtual machine.

On the Windows client, BitLocker remained active with TPM and numerical password protectors. The local Administrators group contained only the built-in `Administrator` account. The previously removed `localadmin` account remained absent, and `MRTG\Domain Admins`, which was present during Lab 28, was no longer listed.

On `MRTG-DC01`, the Service Accounts OU and `svc-audit-review` account were reviewed. The account description continued to identify its purpose, responsible team, and quarterly review frequency. Its direct Active Directory membership displayed only `Domain Users`.

The scheduled task from Lab 26 was reviewed on `MRTG-DC01`. Its configured identity, PowerShell action, script, and prior output file remained present. The task was not executed again during this capstone.

On `MRTG-LOG01`, Splunk Enterprise remained accessible. Searches for successful logons, failed logons, and local security group changes executed against the local Security event dataset.

Post-lab checkpoints were created for all three virtual machines.

---

## Objectives

- Create pre-lab Hyper-V checkpoints
- Validate the current BitLocker state
- Review local administrator membership
- Review the Service Accounts OU
- Validate service account documentation
- Review direct service account group membership
- Review the scheduled-task identity
- Review the scheduled-task action
- Confirm the script and prior output artifacts exist
- Confirm Splunk Enterprise remains accessible
- Review successful logon events
- Review failed logon events
- Review local group-change events
- Document monitoring and validation limitations
- Identify unexplained configuration drift
- Create post-lab Hyper-V checkpoints
- Complete the IAM operations expansion track

---

## Scope

### Included

- Active Directory service account review
- Service account description review
- Direct group membership review
- Scheduled-task configuration review
- Scheduled-task action review
- Script and output artifact review
- BitLocker status validation
- Direct local Administrators membership review
- Splunk Web availability validation
- Authentication event searches
- Local group-change searches
- Hyper-V checkpoints
- Governance findings
- Evidence collection

### Not Included

- New service account creation
- Effective-access analysis
- Nested group expansion
- Service account password rotation
- Scheduled-task reconfiguration
- Fresh scheduled-task execution
- BitLocker recovery testing
- Recovery-password retrieval testing
- New local administrator remediation
- Root-cause analysis for all configuration drift
- Remote Windows event forwarding
- New Splunk dashboards or alerts
- Splunk Enterprise Security
- Production policy enforcement
- Formal compliance certification
- Backup or disaster-recovery testing

---

## Environment

| Component | Details |
|---|---|
| Organization | Monroe Redstone Technology Group |
| Domain | `mrtg.local` |
| Primary Domain Controller | `MRTG-DC01` |
| Logging Server | `MRTG-LOG01` |
| Hyper-V Client VM | `MRTG-CLIENT-01` |
| Windows Client Guest | `CLIENT01` |
| Splunk Platform | Splunk Enterprise `10.4.0` |
| Splunk Web | `http://localhost:8000` |
| Endpoint Encryption | BitLocker |
| Service Account | `svc-audit-review` |
| Scheduled Task | `MRTG Audit Review Marker` |
| Automation Platform | Windows Task Scheduler |
| Hypervisor | Hyper-V |

---

## Systems Reviewed

| System | Role | Capstone Review |
|---|---|---|
| `MRTG-DC01` | Domain controller | Service account governance and scheduled task |
| `MRTG-LOG01` | Logging server | Splunk availability and local event searches |
| `MRTG-CLIENT-01` | Windows client VM | BitLocker and local Administrators membership |

---

## Expansion Labs Consolidated

| Lab | Control Area | Capstone Review |
|---|---|---|
| Lab 25 | Service Account Governance | Account location, description, review frequency, and direct membership |
| Lab 26 | Least-Privilege Scheduled Task | Task identity, action, script, and prior output artifact |
| Lab 27 | BitLocker Protection | Encryption status and key protectors |
| Lab 28 | Local Administrator Remediation | Current direct local Administrators membership |
| Lab 29 | Splunk Identity Monitoring | Platform availability and identity-event searches |

---

## Governance Review Model

```text
Review identity
       |
       v
Review privilege
       |
       v
Review automation
       |
       v
Validate endpoint controls
       |
       v
Review monitoring
       |
       v
Document evidence and gaps
```

---

## Implementation Steps

### Step 1: Create the DC01 Pre-Lab Checkpoint

A pre-lab checkpoint was created for `MRTG-DC01`.

Checkpoint name:

```text
MRTG-DC01_Pre-Lab30-IAM-Operations-Governance-Capstone
```

![DC01 Pre-Lab Checkpoint](screenshots/lab-30-01-dc01-pre-lab-checkpoint.png)

---

### Step 2: Create the LOG01 Pre-Lab Checkpoint

A pre-lab checkpoint was created for `MRTG-LOG01`.

Checkpoint name:

```text
MRTG-LOG01_Pre-Lab30-IAM-Operations-Governance-Capstone
```

![LOG01 Pre-Lab Checkpoint](screenshots/lab-30-02-log01-pre-lab-checkpoint.png)

---

### Step 3: Create the Client Pre-Lab Checkpoint

A pre-lab checkpoint was created for the `MRTG-CLIENT-01` virtual machine.

Checkpoint name:

```text
MRTG-CLIENT-01_Pre-Lab30-IAM-Operations-Governance-Capstone
```

> Hyper-V checkpoints preserve temporary lab states. They are not substitutes for tested backups, recovery-key escrow, or disaster-recovery procedures.

![CLIENT-01 Pre-Lab Checkpoint](screenshots/lab-30-03-client01-pre-lab-checkpoint.png)

---

## Endpoint Security Review

### Step 4: Review BitLocker Status

BitLocker status was reviewed on the Windows client.

Command used:

```powershell
manage-bde -status C:
```

Observed results:

| Setting | Value |
|---|---|
| Volume | `C:` |
| BitLocker Version | `2.0` |
| Conversion Status | Used Space Only Encrypted |
| Percentage Encrypted | `100.0%` |
| Encryption Method | `XTS-AES 128` |
| Protection Status | `Protection On` |
| Lock Status | `Unlocked` |
| Key Protector | TPM |
| Key Protector | Numerical Password |

The evidence confirmed that the volume remained encrypted, BitLocker protection was active, and both documented protectors were present.

It did not validate recovery-password escrow, retrieval, or forced recovery.

![BitLocker Status Review](screenshots/lab-30-04-bitlocker-status-review.png)

---

### BitLocker Governance Finding

| Governance Check | Result |
|---|---|
| Volume fully encrypted | Confirmed |
| Protection status enabled | Confirmed |
| TPM protector present | Confirmed |
| Numerical password protector present | Confirmed |
| Recovery value excluded from evidence | Confirmed |
| Recovery-key escrow validated | Not tested |
| Recovery operation validated | Not tested |

---

### Step 5: Review Local Administrator Membership

The local Administrators group was reviewed on the Windows client.

Command used:

```powershell
net localgroup administrators
```

Observed direct membership:

```text
Administrator
```

The previously remediated `localadmin` account was not listed.

`MRTG\Domain Admins`, which appeared during the Lab 28 review, was also not listed. The evidence confirms the current direct membership but does not identify which policy or administrative action removed the domain group.

![Local Administrator Membership Review](screenshots/lab-30-05-local-admin-membership-review.png)

---

### Local Administrator Governance Finding

| Governance Check | Result |
|---|---|
| Direct local Administrators membership reviewed | Confirmed |
| Built-in `Administrator` present | Confirmed |
| `localadmin` absent | Confirmed |
| `MRTG\Domain Admins` absent | Confirmed |
| Cause of Domain Admins removal identified | Not validated |
| Nested or indirect privilege paths reviewed | Not tested |
| Built-in Administrator LAPS management validated | Not tested |

The current direct membership was narrower than the Lab 28 result. In a controlled environment, that change should be traceable to an approved policy or administrative action.

---

## Service Account Governance Review

### Step 6: Review the Service Accounts OU

The Service Accounts OU was reviewed through Active Directory Users and Computers.

Path:

```text
mrtg.local
└── _MRTG
    └── Service Accounts
```

Visible service accounts:

```text
Service App Deploy
Service Audit Review
Service Backup
```

The dedicated OU continued to provide organizational separation between these non-human identities and standard user accounts.

OU placement supports administration and policy targeting, but it does not independently establish ownership, least privilege, monitoring, or lifecycle control.

![Service Account OU Review](screenshots/lab-30-06-service-account-ou-review.png)

---

### Step 7: Review Service Account Properties

The properties of Service Audit Review were examined.

Account:

```text
svc-audit-review
```

Description:

```text
Lab 25 svc acct. Owner: IT Ops. Review: Qtrly.
```

The description documented:

- A lab purpose
- A responsible team
- A quarterly review frequency

The description did not identify a named accountable owner or prove that a quarterly review had occurred.

![Service Account Properties Review](screenshots/lab-30-07-service-account-properties-review.png)

---

### Step 8: Review Service Account Group Membership

The account's Member Of tab displayed:

```text
Domain Users
```

The account was not shown as a direct member of the following reviewed groups:

```text
Domain Admins
Enterprise Admins
Schema Admins
Administrators
Account Operators
Server Operators
Backup Operators
```

This evidence validates the displayed direct Active Directory membership. It does not establish complete effective access or exclude permissions obtained through user rights, delegated ACLs, local groups, nested membership, scheduled tasks, or file permissions.

![Service Account Group Membership Review](screenshots/lab-30-08-service-account-group-membership-review.png)

---

### Service Account Governance Finding

| Governance Check | Result |
|---|---|
| Stored in Service Accounts OU | Confirmed |
| Lab purpose documented | Confirmed |
| Responsible team documented | Confirmed |
| Named accountable owner documented | Not confirmed |
| Review frequency documented | Confirmed |
| Completed quarterly review recorded | Not confirmed |
| Direct `Domain Users` membership displayed | Confirmed |
| Reviewed privileged direct memberships absent | Confirmed |
| Complete effective-access review | Not performed |

The account retained the descriptive governance fields created in Lab 25, but additional ownership, credential, logon, and effective-access controls remain necessary for production use.

---

## Least-Privilege Automation Review

### Step 9: Review the Scheduled-Task Identity

The Lab 26 scheduled task was reviewed on `MRTG-DC01`.

Task name:

```text
MRTG Audit Review Marker
```

Run-as account:

```text
svc-audit-review
```

Task description:

```text
Runs a basic audit-review marker script using a least-privilege service account.
```

The task remained present and referenced the expected service account.

This configuration review did not validate the account's current credential state or the task's ability to execute successfully.

![Scheduled Task Service Account Review](screenshots/lab-30-09-scheduled-task-service-account-review.png)

---

### Step 10: Review the Scheduled-Task Action

The task action was reviewed.

Configured action:

```text
powershell.exe -ExecutionPolicy Bypass -File "C:\MRTG-Audit\audit-review-marker.ps1"
```

The action continued to reference the expected PowerShell script.

The use of `ExecutionPolicy Bypass` reduces reliance on PowerShell execution-policy controls. Production automation should use stronger script integrity and application-control measures.

![Scheduled Task Action Review](screenshots/lab-30-10-scheduled-task-action-review.png)

---

### Step 11: Review the Scheduled-Task Artifacts

The task folder was reviewed on `MRTG-DC01`.

Folder:

```text
C:\MRTG-Audit
```

Visible artifacts:

```text
audit-review-marker.ps1
audit-review-output.txt
```

The script and prior output file remained present.

Artifact presence does not independently prove:

- The script remained unchanged
- The output was current
- The task executed successfully during Lab 30
- The output was generated by the expected identity
- The account still had all required effective permissions

![Scheduled Task Output Review](screenshots/lab-30-11-scheduled-task-output-review.png)

---

### Automation Governance Finding

| Governance Check | Result |
|---|---|
| Scheduled task present | Confirmed |
| Expected service account configured | Confirmed |
| Expected script path configured | Confirmed |
| Script artifact present | Confirmed |
| Prior output artifact present | Confirmed |
| Script integrity validated | Not tested |
| Fresh task execution validated | Not tested |
| Current Last Run Result reviewed | Not documented |
| Current service account logon event validated | Not tested |

The task configuration remained reviewable, but current operational health was not established.

The service account also retained Modify permission on the folder containing the script from Lab 26. A stronger design would separate read-only script content from writable output.

---

## SIEM Monitoring Review

### Step 12: Review Splunk Enterprise Availability

Splunk Enterprise was opened locally on `MRTG-LOG01`.

URL:

```text
http://localhost:8000
```

The home page loaded successfully.

This confirmed local Splunk Web availability. It did not validate remote availability, TLS, ingestion health for every source, alerting, or Splunk Enterprise Security functionality.

![Splunk Home Review](screenshots/lab-30-12-splunk-home-review.png)

---

### Step 13: Review Successful Logon Events

Successful logons were searched using Event ID `4624`.

Search:

```spl
index=main sourcetype="WinEventLog:Security" EventCode=4624
```

The selected 24-hour window displayed 99 matching events.

This confirmed that successful logon events were searchable in the available `MRTG-LOG01` dataset.

![Splunk Successful Logon Review](screenshots/lab-30-13-splunk-successful-logon-review.png)

---

### Step 14: Review Failed Logon Events

Failed logons were searched using Event ID `4625`.

Search:

```spl
index=main sourcetype="WinEventLog:Security" EventCode=4625
```

The selected 24-hour window returned zero matching events.

This means no matching events were found in the available dataset and time range. It does not prove that no failed logons occurred elsewhere in the MRTG environment.

![Splunk Failed Logon Review](screenshots/lab-30-14-splunk-failed-logon-review.png)

---

### Step 15: Review Local Group-Change Events

Local security group changes were searched using Event IDs `4732` and `4733`.

Search:

```spl
index=main sourcetype="WinEventLog:Security" (EventCode=4732 OR EventCode=4733)
```

The selected 24-hour window returned zero matching events.

This means no matching local group changes were found in the available `MRTG-LOG01` dataset and selected time range.

![Splunk Local Group Change Review](screenshots/lab-30-15-splunk-local-group-change-review.png)

---

### SIEM Governance Finding

| Monitoring Check | Result |
|---|---|
| Splunk Web locally accessible | Confirmed |
| Successful-logon search executed | Confirmed |
| Successful-logon matches | 99 |
| Failed-logon search executed | Confirmed |
| Failed-logon matches | 0 |
| Local group-change search executed | Confirmed |
| Local group-change matches | 0 |
| Domain controller event collection | Not configured |
| Endpoint event collection | Not configured |
| Alerts or correlation searches | Not validated |

Event counts apply only to the selected search window and available indexed data.

---

## SIEM Scope Limitation

The Splunk configuration reviewed in this capstone indexed the local Windows Security log from:

```text
MRTG-LOG01
```

The evidence did not show remote Security event collection from:

- `MRTG-DC01`
- `MRTG-DC02`
- `MRTG-CLIENT-01`

Therefore, the results must not be interpreted as domain-wide identity monitoring.

A production design would collect approved Security events from domain controllers, selected servers, and endpoints while monitoring source health and ingestion gaps.

---

## Governance Review Summary

| Control Area | Source Lab | Current Finding | Governance Decision |
|---|---|---|---|
| Service account governance | Lab 25 | Descriptive governance fields and direct membership remained visible | Add named ownership and effective-access review |
| Least-privilege automation | Lab 26 | Task, identity, action, and artifacts remained present | Add current execution and script-integrity validation |
| Endpoint encryption | Lab 27 | BitLocker protection and protectors remained present | Add escrow and controlled recovery testing |
| Local administrator access | Lab 28 | Only built-in Administrator was directly listed | Trace the Domain Admins removal and validate LAPS |
| Identity-event monitoring | Lab 29 | Splunk Web and local searches remained available | Expand collection and implement tested detections |

---

## Control Validation Summary

| Control | Validation Method | Observed Result | Status |
|---|---|---|---|
| DC01 pre-lab state | Hyper-V checkpoint | Created | Passed |
| LOG01 pre-lab state | Hyper-V checkpoint | Created | Passed |
| Client pre-lab state | Hyper-V checkpoint | Created | Passed |
| BitLocker protection | `manage-bde -status C:` | Protection On | Passed |
| TPM protector | `manage-bde` | Present | Passed |
| Numerical password protector | `manage-bde` | Present | Passed |
| Recovery-key escrow | Not performed | Not validated | Not Tested |
| Local Administrators membership | `net localgroup administrators` | `Administrator` only | Passed |
| Domain Admins removal source | Change history | Not identified | Needs Review |
| Service account organization | Active Directory Users and Computers | Dedicated OU confirmed | Passed |
| Service account description | Description field | Team and frequency present | Passed |
| Named account owner | Description field | Not present | Needs Review |
| Service account direct membership | Member Of tab | `Domain Users` displayed | Passed |
| Effective service account access | Not performed | Not validated | Not Tested |
| Scheduled-task identity | Task Scheduler | `svc-audit-review` | Passed |
| Scheduled-task action | Task Scheduler | Expected script referenced | Passed |
| Script artifact | File Explorer | Present | Passed |
| Prior output artifact | File Explorer | Present | Passed |
| Fresh task execution | Not performed | Not validated | Not Tested |
| Splunk availability | Splunk Web | Home page loaded | Passed |
| Successful-logon search | Event ID `4624` | 99 matches | Passed |
| Failed-logon search | Event ID `4625` | 0 matches | Reviewed |
| Local group-change search | Event IDs `4732`, `4733` | 0 matches | Reviewed |
| Domain-wide event collection | Source inventory | Not configured | Not Tested |

---

## Post-Lab Checkpoints

### Step 16: Create the DC01 Post-Lab Checkpoint

Checkpoint name:

```text
MRTG-DC01_Post-Lab30-IAM-Operations-Governance-Capstone-Validated
```

![DC01 Post-Lab Checkpoint](screenshots/lab-30-16-dc01-post-lab-checkpoint.png)

---

### Step 17: Create the LOG01 Post-Lab Checkpoint

Checkpoint name:

```text
MRTG-LOG01_Post-Lab30-IAM-Operations-Governance-Capstone-Validated
```

![LOG01 Post-Lab Checkpoint](screenshots/lab-30-17-log01-post-lab-checkpoint.png)

---

### Step 18: Create the Client Post-Lab Checkpoint

Checkpoint name:

```text
MRTG-CLIENT-01_Post-Lab30-IAM-Operations-Governance-Capstone-Validated
```

![CLIENT-01 Post-Lab Checkpoint](screenshots/lab-30-18-client01-post-lab-checkpoint.png)

---

## Evidence Collected

| Evidence | Screenshot |
|---|---|
| DC01 pre-lab checkpoint | `screenshots/lab-30-01-dc01-pre-lab-checkpoint.png` |
| LOG01 pre-lab checkpoint | `screenshots/lab-30-02-log01-pre-lab-checkpoint.png` |
| Client pre-lab checkpoint | `screenshots/lab-30-03-client01-pre-lab-checkpoint.png` |
| BitLocker status | `screenshots/lab-30-04-bitlocker-status-review.png` |
| Local administrator membership | `screenshots/lab-30-05-local-admin-membership-review.png` |
| Service Accounts OU | `screenshots/lab-30-06-service-account-ou-review.png` |
| Service account properties | `screenshots/lab-30-07-service-account-properties-review.png` |
| Service account group membership | `screenshots/lab-30-08-service-account-group-membership-review.png` |
| Scheduled-task identity | `screenshots/lab-30-09-scheduled-task-service-account-review.png` |
| Scheduled-task action | `screenshots/lab-30-10-scheduled-task-action-review.png` |
| Scheduled-task artifacts | `screenshots/lab-30-11-scheduled-task-output-review.png` |
| Splunk home page | `screenshots/lab-30-12-splunk-home-review.png` |
| Successful-logon search | `screenshots/lab-30-13-splunk-successful-logon-review.png` |
| Failed-logon search | `screenshots/lab-30-14-splunk-failed-logon-review.png` |
| Local group-change search | `screenshots/lab-30-15-splunk-local-group-change-review.png` |
| DC01 post-lab checkpoint | `screenshots/lab-30-16-dc01-post-lab-checkpoint.png` |
| LOG01 post-lab checkpoint | `screenshots/lab-30-17-log01-post-lab-checkpoint.png` |
| Client post-lab checkpoint | `screenshots/lab-30-18-client01-post-lab-checkpoint.png` |

---

## Troubleshooting and Review Notes

### Local Administrators Membership Changed

Lab 28 ended with:

```text
Administrator
MRTG\Domain Admins
```

Lab 30 displayed:

```text
Administrator
```

The current direct membership was narrower, but the capstone evidence did not identify the source of the change.

In production, the difference should be traced through:

- Change tickets
- Group Policy
- Endpoint management policy
- Administrative audit records
- Configuration management history
- Relevant Windows Security events

An unexplained beneficial change is still unexplained configuration drift.

### Scheduled Task Was Not Re-Executed

The scheduled task, script, and prior output file existed.

However, the evidence did not show a new Lab 30 execution.

A stronger operational validation would review:

- Last Run Time
- Last Run Result
- New output timestamp
- Task Scheduler operational events
- Service account logon events
- Script hash or signature
- Output contents and provenance

### Splunk Searches Returned Zero Events

The failed-logon and local group-change searches returned zero results in the selected 24-hour window.

This does not indicate that the queries failed. It means no matching events were found within the current `MRTG-LOG01` dataset and time range.

Zero results do not establish the absence of activity on systems that were not collected.

---

## Security Considerations

The review identified several production concerns:

- `ExecutionPolicy Bypass` reduces script execution-policy enforcement
- The service account could modify the folder containing its script
- Existing output does not prove current task health
- The service account used stored reusable credentials
- A named accountable service account owner was not documented
- Splunk collected only local `MRTG-LOG01` events
- Splunk Web used local HTTP in the demonstrated workflow
- Hyper-V checkpoints were not backups
- Built-in Administrator LAPS management was not revalidated
- BitLocker recovery-key escrow and recovery were not tested
- Splunk administrative role separation was not demonstrated
- The removal of `MRTG\Domain Admins` was not traced to a documented change

These limitations define the next control-maturity requirements.

---

## IAM and Security Relevance

| Principle | Capstone Application |
|---|---|
| Least privilege | Reviewed direct service account and local administrator membership |
| Identity governance | Reviewed account purpose, responsible team, and review frequency |
| Defense in depth | Combined encryption, privilege control, automation, and monitoring |
| Continuous validation | Rechecked selected controls after implementation |
| Evidence | Preserved configuration and search screenshots |
| Detection engineering | Reused identity-focused SPL searches |
| Non-human identity management | Reviewed service account placement and automation use |
| Endpoint governance | Reviewed BitLocker and local administrator membership |
| Configuration management | Identified unexplained membership drift |

---

## Risk Addressed

This capstone reviewed risks associated with controls becoming stale after deployment, including:

- Orphaned service accounts
- Excessive service account privilege
- Scheduled tasks using inappropriate identities
- Missing automation artifacts
- Disabled endpoint encryption
- Reintroduced local administrator membership
- Unavailable monitoring tools
- Untested search logic
- Missing operational evidence
- Unexplained configuration drift

Residual risks remained where testing, collection, ownership, or change records were incomplete.

---

## Production Improvements

A production or government-regulated implementation should include:

- Formal recurring service account reviews
- Named business and technical owners
- Group Managed Service Accounts where supported
- Automated credential rotation
- Effective-access analysis
- Interactive logon restrictions
- Central scheduled-task inventory
- Current task execution validation
- Separate read-only script and writable output locations
- Script signing and application control
- Removal of unnecessary `ExecutionPolicy Bypass`
- BitLocker compliance reporting
- Recovery-key escrow and retrieval auditing
- Controlled BitLocker recovery testing
- Windows LAPS enforcement
- Local administrator membership baselines
- Change tracking for privileged membership
- Splunk Universal Forwarders
- Domain controller Security log collection
- Dedicated Windows Security indexes
- Splunk role-based access control
- Authentication and privilege dashboards
- Alerts for failed-logon thresholds
- Alerts for privileged group changes
- SIEM source-health monitoring
- Formal exception management
- Framework-specific control mapping
- Evidence retention standards
- Tested backup and disaster-recovery procedures

---

## Lessons Learned

- IAM controls require recurring review
- Service account responsibility must remain visible
- A team label is not the same as a named accountable owner
- Group membership should be revalidated after implementation
- Direct membership does not establish complete effective access
- Automation configuration and execution health are separate checks
- Existing output is not proof of current execution
- BitLocker encryption, protection, and recovery must be validated separately
- Local administrator membership can drift between reviews
- Configuration differences should be traceable to approved changes
- SIEM search scope depends on collected data sources
- Zero results do not mean a query failed
- Hyper-V checkpoints support lab recovery but do not replace backups
- Operational IAM connects governance, endpoint security, automation, monitoring, and change management

---

## Skills Demonstrated

- IAM governance review
- Service account auditing
- Non-human identity governance
- Active Directory Users and Computers
- Direct group membership validation
- Scheduled-task review
- PowerShell automation review
- BitLocker validation
- Local administrator review
- Splunk availability validation
- SPL authentication searches
- Zero-result analysis
- Configuration-drift analysis
- Evidence collection
- Hyper-V checkpoint management
- Production control planning

---

## Outcome

Lab 30 completed the IAM operations, monitoring, and governance capstone.

The review confirmed that:

- The Service Accounts OU remained present
- `svc-audit-review` retained its documented lab purpose, responsible team, and review frequency
- Its displayed direct membership remained `Domain Users`
- The scheduled task continued to reference the expected service account and script
- The expected script and prior output artifacts remained present
- BitLocker remained fully encrypted with protection active
- TPM and numerical password protectors remained present
- `localadmin` remained absent from direct local Administrators membership
- The current local Administrators group displayed only `Administrator`
- Splunk Enterprise remained locally accessible
- Successful-logon events remained searchable
- Failed-logon and local group-change queries remained executable
- Review evidence was captured

The capstone also identified controls that were not fully validated:

- Named service account ownership
- Complete effective-access analysis
- Current scheduled-task execution
- Script integrity
- BitLocker recovery-key escrow and recovery
- The cause of local administrator membership drift
- Domain-wide Security event collection
- SIEM alerting and response

The capstone demonstrated a repeatable governance-review process without overstating configuration evidence as complete operational assurance.

---

## Series Completion

Lab 30 completes the IAM operations expansion track:

- Lab 25: Service Account Governance Foundation
- Lab 26: Scheduled Task with a Least-Privilege Service Account
- Lab 27: BitLocker and Endpoint Encryption Recovery
- Lab 28: Local Administrator Access Review and Remediation
- Lab 29: SIEM Identity Monitoring with Splunk
- Lab 30: IAM Operations, Monitoring, and Governance Capstone

Together, these labs demonstrate practical experience with:

- Non-human identity governance
- Least-privilege automation
- Endpoint encryption
- Local administrator remediation
- Identity-event monitoring
- Operational control review
- Evidence documentation
- Configuration-drift analysis
- Security governance

The completed MRTG IAM lab series provides an end-to-end portfolio covering Active Directory foundations, identity lifecycle operations, security controls, recovery concepts, auditing, documentation, endpoint protection, monitoring, and governance.
