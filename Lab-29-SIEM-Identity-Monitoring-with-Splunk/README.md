# Lab 29 - SIEM Identity Monitoring with Splunk

![Platform](https://img.shields.io/badge/Platform-Windows%20Server-blue)
![Technology](https://img.shields.io/badge/Technology-Splunk%20Enterprise-blue)
![Focus](https://img.shields.io/badge/Focus-SIEM%20Identity%20Monitoring-green)
![Security](https://img.shields.io/badge/Security-Windows%20Security%20Events-red)
![Validation](https://img.shields.io/badge/Validation-Completed-brightgreen)
![Documentation](https://img.shields.io/badge/Documentation-Audit%20Ready-purple)

---

## Overview

In this lab, I installed Splunk Enterprise on `MRTG-LOG01`, ingested its local Windows Security event log, and created identity-focused searches.

The searches reviewed successful logons, failed logons, account lifecycle activity, and security group membership changes.

The lab also demonstrated an important SIEM principle: a search returning zero results is still useful when the search logic, time range, data source, and collection scope are understood.

This lab established a foundation for centralized identity monitoring. It did not yet forward security events from `MRTG-DC01` or other remote systems.

---

## Business Problem

MRTG needed better visibility into identity-related activity.

Authentication, account management, and group membership events can reveal:

- Normal user activity
- Failed authentication attempts
- Password problems
- Account lifecycle changes
- Privilege assignments
- Unauthorized group modifications
- Possible credential attacks
- Endpoint administrator changes

Without centralized collection and search capabilities, these events remain distributed across individual Windows systems and are difficult to investigate.

This lab addressed that problem by deploying Splunk Enterprise and validating a repeatable identity-event search workflow.

---

## Lab Summary

I created pre-lab checkpoints for `MRTG-DC01` and `MRTG-LOG01`.

Because the logging server did not have direct internet access, I created `C:\Installers` and staged the Splunk Enterprise installer locally.

I confirmed that Splunk was not already installed, completed the installation using a local virtual service account, created the Splunk administrator credential, and accessed Splunk Web through port `8000`.

I added the local Windows Security event log as a Splunk input using the `main` index and `WinEventLog:Security` sourcetype.

I then tested searches for:

- All indexed Windows Security events
- Successful logons using Event ID `4624`
- Failed logons using Event ID `4625`
- Account lifecycle events
- Global and local security group changes
- Local security group additions
- Administrator-specific group changes

Finally, I created post-lab checkpoints for both systems.

---

## Objectives

- Create pre-lab checkpoints
- Prepare an offline installer staging location
- Validate that Splunk was not already installed
- Install Splunk Enterprise on `MRTG-LOG01`
- Use a local virtual service account
- Protect the Splunk administrator credential
- Validate Splunk Web access
- Add the Windows Security event log as an input
- Confirm events were indexed
- Search successful logon events
- Search failed logon events
- Test account lifecycle searches
- Search security group membership changes
- Test local administrator-specific detection
- Document positive and zero-result searches
- Create post-lab checkpoints

---

## Scope

### Included

- Splunk Enterprise installation
- Offline installer staging
- Local virtual service account selection
- Splunk administrator account creation
- Splunk Web validation
- Local Windows Security log ingestion
- SPL identity-event searches
- Authentication event review
- Account management search testing
- Security group change review
- Zero-result analysis
- Hyper-V checkpoints
- Audit evidence collection

### Not Included

- Universal Forwarder deployment
- Remote collection from `MRTG-DC01`
- Collection from `MRTG-CLIENT-01`
- Collection from `MRTG-DC02`
- Distributed Splunk architecture
- Dedicated Windows Security index
- Splunk role-based access control
- Dashboards
- Correlation searches
- Alert creation
- Long-term retention planning
- TLS configuration
- Production hardening
- Splunk Enterprise Security deployment

---

## Environment

| Component | Details |
|---|---|
| Organization | Monroe Redstone Technology Group |
| Domain | `mrtg.local` |
| Domain Controller | `MRTG-DC01` |
| Logging Server | `MRTG-LOG01` |
| SIEM Platform | Splunk Enterprise |
| Splunk Version | `10.4.0` |
| Installation Path | `C:\Program Files\Splunk\` |
| Installer Staging Path | `C:\Installers` |
| Splunk Web | `http://localhost:8000` |
| Service Identity | Local virtual account |
| Event Source | Local Windows Security event log |
| Indexed Host | `MRTG-LOG01` |
| Index | `main` |
| Sourcetype | `WinEventLog:Security` |
| App Context | Search |
| Hypervisor | Hyper-V |

---

## Scenario

MRTG needs a searchable platform for reviewing identity-related Windows events.

For this foundational lab, Splunk Enterprise is installed directly on `MRTG-LOG01`, and the server’s local Security event log is indexed.

The monitoring model used was:

```text
Install Platform → Add Security Log → Confirm Ingestion → Search Authentication → Search Account Changes → Search Group Changes → Document Findings
```

This proves the ingestion and search workflow before expanding collection to domain controllers and endpoints.

---

## Monitoring Architecture

```text
MRTG-LOG01
├── Splunk Enterprise
├── Splunk Web on TCP 8000
├── Local Windows Security Log
└── Index: main
    └── Sourcetype: WinEventLog:Security
```

The current architecture indexes events generated on `MRTG-LOG01`.

It should not be described as full domain-wide monitoring because remote Windows systems were not configured as data sources during this lab.

---

## Identity Event Reference

| Event ID | Description | Monitoring Value |
|---:|---|---|
| `4624` | Successful account logon | Establish normal authentication activity |
| `4625` | Failed account logon | Identify failures and possible password attacks |
| `4720` | User account created | Monitor account provisioning |
| `4722` | User account enabled | Monitor account activation |
| `4725` | User account disabled | Monitor offboarding or containment |
| `4726` | User account deleted | Monitor account removal |
| `4728` | Member added to global security group | Monitor domain group access changes |
| `4729` | Member removed from global security group | Monitor domain group access removal |
| `4732` | Member added to local security group | Monitor local privilege assignments |
| `4733` | Member removed from local security group | Monitor local privilege removal |

---

## Implementation Steps

### Step 1 - Created DC01 Pre-Lab Checkpoint

A checkpoint was created for `MRTG-DC01`.

Checkpoint name:

```text
MRTG-DC01_Pre-Lab29-SIEM-Identity-Monitoring
```

![DC01 Pre-Lab Checkpoint](screenshots/lab-29-01-dc01-pre-lab-checkpoint.png)

---

### Step 2 - Created LOG01 Pre-Lab Checkpoint

A checkpoint was created for `MRTG-LOG01` before installing Splunk.

Checkpoint name:

```text
MRTG-LOG01_Pre-Lab29-SIEM-Identity-Monitoring
```

![LOG01 Pre-Lab Checkpoint](screenshots/lab-29-02-log01-pre-lab-checkpoint.png)

---

## Splunk Installation

### Step 3 - Created the Installers Folder

`MRTG-LOG01` did not have direct internet access, so an offline installation workflow was used.

Installer staging folder:

```text
C:\Installers
```

This reflects a common practice in restricted environments where servers cannot download software directly.

![Installers Folder Created](screenshots/lab-29-03-installers-folder-created.png)

---

### Step 4 - Staged the Splunk Installer

The Splunk Enterprise Windows installer was copied to the staging folder.

Installer:

```text
splunk-10.4.0-f798d4d49089-windows-x64.msi
```

![Splunk Installer Staged](screenshots/lab-29-04-splunk-installer-staged.png)

---

### Step 5 - Validated That Splunk Was Not Installed

An elevated PowerShell session was used to search for existing Splunk services.

Command used:

```powershell
Get-Service *splunk*
```

No services were returned.

This established a clean pre-installation baseline.

![Splunk Not Installed Validation](screenshots/lab-29-05-splunk-not-installed-validation.png)

---

### Step 6 - Launched the Splunk Installer

The Splunk Enterprise installer was launched.

The license agreement was accepted, and the installation options were reviewed.

![Splunk Installer Wizard](screenshots/lab-29-06-splunk-installer-wizard.png)

---

### Step 7 - Confirmed the Installation Path

Splunk Enterprise was installed to the default Windows path:

```text
C:\Program Files\Splunk\
```

![Splunk Installation Path](screenshots/lab-29-07-splunk-install-path.png)

---

### Step 8 - Selected the Splunk Service Account

Splunk Enterprise was configured to run using a local virtual account.

Selected option:

```text
Virtual Account
```

The virtual account provided access to local and forwarded data without introducing a new domain service account.

This supported least privilege for the current local installation.

![Splunk Service Account Selection](screenshots/lab-29-08-splunk-service-account-selection.png)

---

### Step 9 - Configured the Splunk Administrator Account

A local Splunk administrator account was created during installation.

Username:

```text
admin
```

The password was intentionally hidden and excluded from the repository.

![Splunk Administrator Credentials Configured](screenshots/lab-29-09-splunk-admin-credentials-configured.png)

---

### Step 10 - Completed the Installation

The installer copied the required files and completed the Splunk Enterprise installation.

![Splunk Installation Progress](screenshots/lab-29-10-splunk-install-progress.png)

---

## Splunk Web Validation

### Step 11 - Opened the Splunk Web Login Page

Splunk Web was accessed locally at:

```text
http://localhost:8000
```

The login page confirmed that the web interface was listening on TCP port `8000`.

![Splunk Web Login](screenshots/lab-29-11-splunk-web-login.png)

---

### Step 12 - Validated the Splunk Enterprise Home Page

After authentication, the Splunk Enterprise home page loaded successfully.

This confirmed that:

- Splunk Enterprise was installed
- Splunk Web was running
- The administrator credential worked
- Search and Reporting was available

![Splunk Enterprise Home](screenshots/lab-29-12-splunk-enterprise-home.png)

---

## Windows Security Log Ingestion

### Step 13 - Configured the Windows Security Log Input

The local Windows Security event log was added as a Splunk input.

Input configuration:

| Setting | Value |
|---|---|
| Input Type | Windows Event Logs |
| Event Log | Security |
| App Context | Search |
| Host | `MRTG-LOG01` |
| Index | `main` |

![Windows Security Log Input Review](screenshots/lab-29-13-windows-security-log-input-review.png)

---

### Step 14 - Validated Windows Security Event Ingestion

The indexed Security events were searched with SPL.

Search used:

```spl
index=main sourcetype="WinEventLog:Security"
```

The search returned more than 13,000 Windows Security events from `MRTG-LOG01`.

This confirmed that the local Security log was being indexed and was searchable.

![Windows Security Events Search](screenshots/lab-29-14-windows-security-events-search.png)

---

## Authentication Monitoring

### Step 15 - Searched Successful Logon Events

Successful logons were searched using Event ID `4624`.

Search used:

```spl
index=main sourcetype="WinEventLog:Security" EventCode=4624
```

The search returned successful authentication events.

Event ID `4624` can support:

- User activity review
- Logon baseline development
- Account usage validation
- Source workstation analysis
- Logon type analysis
- Incident investigation

![Successful Logon Events 4624](screenshots/lab-29-15-successful-logon-events-4624.png)

---

### Step 16 - Searched Failed Logon Events

Failed logons were searched using Event ID `4625`.

Search used:

```spl
index=main sourcetype="WinEventLog:Security" EventCode=4625
```

The search returned four events in the selected dataset.

Event ID `4625` can indicate:

- Mistyped passwords
- Expired credentials
- Disabled accounts
- Service credential problems
- Password spraying
- Brute-force attempts
- Unauthorized access attempts

A failed logon is not automatically malicious. Context, frequency, source, target account, failure reason, and time must be reviewed.

![Failed Logon Events 4625](screenshots/lab-29-16-failed-logon-events-4625.png)

---

## Account Lifecycle Monitoring

### Step 17 - Tested the Account Management Search

Account lifecycle event IDs were searched.

Search used:

```spl
index=main sourcetype="WinEventLog:Security" (EventCode=4720 OR EventCode=4722 OR EventCode=4725 OR EventCode=4726)
```

Event mappings:

| Event ID | Meaning |
|---:|---|
| `4720` | User account created |
| `4722` | User account enabled |
| `4725` | User account disabled |
| `4726` | User account deleted |

The search returned zero results for the selected data and time range.

This did not prove that no account changes existed anywhere in the MRTG domain. The current input contained only the local `MRTG-LOG01` Security log.

Domain account lifecycle events would normally be collected from domain controllers.

![Account Management Event Search](screenshots/lab-29-17-account-management-event-search.png)

---

## Group Membership Monitoring

### Step 18 - Searched Security Group Membership Changes

Global and local security group membership event IDs were searched.

Search used:

```spl
index=main sourcetype="WinEventLog:Security" (EventCode=4728 OR EventCode=4729 OR EventCode=4732 OR EventCode=4733)
```

Event mappings:

| Event ID | Meaning |
|---:|---|
| `4728` | Member added to a global security group |
| `4729` | Member removed from a global security group |
| `4732` | Member added to a local security group |
| `4733` | Member removed from a local security group |

The search returned two local security group membership events.

![Group Membership Change Events](screenshots/lab-29-18-group-membership-change-events.png)

---

### Step 19 - Isolated Local Security Group Additions

Event ID `4732` was searched separately.

Search used:

```spl
index=main sourcetype="WinEventLog:Security" EventCode=4732
```

The search returned two events.

This confirmed that local security group additions were present in the indexed data.

![Local Security Group Change Events](screenshots/lab-29-19-local-security-group-change-events.png)

---

### Step 20 - Searched Local Group Additions and Removals

A combined search was used for additions to and removals from local security groups.

Search used:

```spl
index=main sourcetype="WinEventLog:Security" (EventCode=4732 OR EventCode=4733)
```

The search returned two events in the selected time range.

This search can support monitoring for changes to groups such as:

- Administrators
- Remote Desktop Users
- Backup Operators
- Event Log Readers
- Remote Management Users

![Local Group Change Events](screenshots/lab-29-20-local-group-change-events.png)

---

### Step 21 - Tested an Administrator-Specific Search

A keyword-based search was tested for additions associated with Administrators.

Search used:

```spl
index=main sourcetype="WinEventLog:Security" EventCode=4732 Administrators
```

The search returned zero results.

This result showed that a broad keyword may not match the event as expected.

A stronger search should identify the parsed field containing the target group name.

Example tuning approach:

```spl
index=main sourcetype="WinEventLog:Security" EventCode=4732
| table _time, SubjectUserName, MemberName, GroupName, ComputerName
```

The actual field names should be confirmed from the indexed events before building an alert.

![Local Admin Specific Search No Results](screenshots/lab-29-21-local-admin-specific-search-no-results.png)

---

## Search Catalog

| Use Case | Event IDs | SPL |
|---|---|---|
| All Security events | Any | `index=main sourcetype="WinEventLog:Security"` |
| Successful logons | `4624` | `index=main sourcetype="WinEventLog:Security" EventCode=4624` |
| Failed logons | `4625` | `index=main sourcetype="WinEventLog:Security" EventCode=4625` |
| Account lifecycle | `4720`, `4722`, `4725`, `4726` | `index=main sourcetype="WinEventLog:Security" (EventCode=4720 OR EventCode=4722 OR EventCode=4725 OR EventCode=4726)` |
| Global and local group changes | `4728`, `4729`, `4732`, `4733` | `index=main sourcetype="WinEventLog:Security" (EventCode=4728 OR EventCode=4729 OR EventCode=4732 OR EventCode=4733)` |
| Local group additions | `4732` | `index=main sourcetype="WinEventLog:Security" EventCode=4732` |
| Local group changes | `4732`, `4733` | `index=main sourcetype="WinEventLog:Security" (EventCode=4732 OR EventCode=4733)` |
| Administrator keyword test | `4732` | `index=main sourcetype="WinEventLog:Security" EventCode=4732 Administrators` |

---

## Monitoring Findings

| Search Area | Result | Interpretation |
|---|---|---|
| Windows Security events | Events found | Local ingestion was working |
| Successful logons | 278 events shown | Successful authentication events were searchable |
| Failed logons | 4 events shown | Failed authentication events were searchable |
| Account lifecycle events | 0 events | No matching events existed in the current LOG01 dataset and range |
| Group membership changes | 2 events | Local security group changes were present |
| Local group additions | 2 events | Event ID `4732` was searchable |
| Local group additions and removals | 2 events | Local group monitoring search worked |
| Administrator keyword search | 0 events | Search required field-aware tuning |

---

## Data Scope Limitation

The current Splunk input collected:

```text
MRTG-LOG01 local Windows Security events
```

It did not collect domain controller Security logs.

Therefore:

- The lab validated local event ingestion
- The lab validated SPL search construction
- The lab did not provide complete domain authentication monitoring
- Zero account lifecycle results were expected within the limited source scope
- Domain account and domain group monitoring would require DC event collection

The next production step would be to deploy Splunk Universal Forwarders to `MRTG-DC01`, `MRTG-DC02`, and selected endpoints.

---

## Post-Lab Checkpoints

### Step 22 - Created DC01 Post-Lab Checkpoint

A post-lab checkpoint was created for `MRTG-DC01`.

Checkpoint name:

```text
MRTG-DC01_Post-Lab29-SIEM-Identity-Monitoring-Validated
```

![DC01 Post-Lab Checkpoint](screenshots/lab-29-22-dc01-post-lab-checkpoint.png)

---

### Step 23 - Created LOG01 Post-Lab Checkpoint

A post-lab checkpoint was created for `MRTG-LOG01`.

Checkpoint name:

```text
MRTG-LOG01_Post-Lab29-SIEM-Identity-Monitoring-Validated
```

![LOG01 Post-Lab Checkpoint](screenshots/lab-29-23-log01-post-lab-checkpoint.png)

---

## IAM and Security Relevance

| IAM Area | Monitoring Value |
|---|---|
| Authentication | Successful and failed logons establish account activity |
| Identity lifecycle | Account creation, enablement, disablement, and deletion can be monitored |
| Authorization | Group changes reveal access assignments and removals |
| Privileged access | Local administrator changes may indicate privilege escalation |
| Least privilege | Monitoring identifies when access expands |
| Incident response | Searchable events support investigation timelines |
| Audit readiness | Indexed events provide reviewable security evidence |
| Governance | Searches can support recurring identity control reviews |

---

## Risk Addressed

This lab addressed risks including:

- Limited visibility into Windows Security events
- Missed failed authentication activity
- Unreviewed local security group changes
- Lack of searchable identity-event evidence
- No SIEM workflow for IAM investigations
- Weak understanding of event source scope
- Misinterpretation of zero-result searches
- Overreliance on raw keyword searches

---

## Control Mapping

| Control Area | Lab Implementation |
|---|---|
| SIEM deployment | Installed Splunk Enterprise |
| Least privilege | Used a local virtual service account |
| Event collection | Added the local Windows Security log |
| Authentication monitoring | Searched Event IDs `4624` and `4625` |
| Lifecycle monitoring | Tested Event IDs `4720`, `4722`, `4725`, and `4726` |
| Privilege monitoring | Searched group change Event IDs |
| Detection engineering | Built and tested reusable SPL |
| Search validation | Documented both positive and zero-result searches |
| Audit readiness | Preserved installation and search evidence |
| Change protection | Created pre-lab and post-lab checkpoints |

---

## Validation Summary

| Validation Item | Expected Result | Actual Result | Status |
|---|---|---|---|
| DC01 pre-lab checkpoint | Checkpoint exists | Created | Passed |
| LOG01 pre-lab checkpoint | Checkpoint exists | Created | Passed |
| Installer folder | `C:\Installers` exists | Created | Passed |
| Installer staged | Splunk MSI available | Staged | Passed |
| Pre-installation check | No Splunk service exists | No service returned | Passed |
| Splunk installation | Application installs successfully | Installed | Passed |
| Virtual account | Local virtual account selected | Selected | Passed |
| Administrator credential | Admin account configured securely | Configured | Passed |
| Splunk Web | Port `8000` responds | Login page loaded | Passed |
| Splunk authentication | Home page accessible | Login successful | Passed |
| Security input | Local Security log configured | Input added | Passed |
| Event ingestion | Security events searchable | More than 13,000 events shown | Passed |
| Successful logons | Event ID `4624` searchable | Events found | Passed |
| Failed logons | Event ID `4625` searchable | Events found | Passed |
| Account lifecycle search | Search executes | Zero results documented | Passed |
| Group change search | Relevant events searchable | Two events found | Passed |
| Local group search | Event IDs `4732` and `4733` searchable | Two events found | Passed |
| Administrator search | Search behavior documented | Zero results documented | Passed |
| DC01 post-lab checkpoint | Final checkpoint exists | Created | Passed |
| LOG01 post-lab checkpoint | Final checkpoint exists | Created | Passed |

---

## Evidence Collected

| Evidence | Screenshot |
|---|---|
| DC01 pre-lab checkpoint | `screenshots/lab-29-01-dc01-pre-lab-checkpoint.png` |
| LOG01 pre-lab checkpoint | `screenshots/lab-29-02-log01-pre-lab-checkpoint.png` |
| Installers folder | `screenshots/lab-29-03-installers-folder-created.png` |
| Splunk installer | `screenshots/lab-29-04-splunk-installer-staged.png` |
| Pre-installation validation | `screenshots/lab-29-05-splunk-not-installed-validation.png` |
| Installer wizard | `screenshots/lab-29-06-splunk-installer-wizard.png` |
| Installation path | `screenshots/lab-29-07-splunk-install-path.png` |
| Virtual service account | `screenshots/lab-29-08-splunk-service-account-selection.png` |
| Administrator credential | `screenshots/lab-29-09-splunk-admin-credentials-configured.png` |
| Installation progress | `screenshots/lab-29-10-splunk-install-progress.png` |
| Splunk Web login | `screenshots/lab-29-11-splunk-web-login.png` |
| Splunk Enterprise home | `screenshots/lab-29-12-splunk-enterprise-home.png` |
| Windows Security input | `screenshots/lab-29-13-windows-security-log-input-review.png` |
| Security event search | `screenshots/lab-29-14-windows-security-events-search.png` |
| Successful logons | `screenshots/lab-29-15-successful-logon-events-4624.png` |
| Failed logons | `screenshots/lab-29-16-failed-logon-events-4625.png` |
| Account management search | `screenshots/lab-29-17-account-management-event-search.png` |
| Group membership changes | `screenshots/lab-29-18-group-membership-change-events.png` |
| Local security group additions | `screenshots/lab-29-19-local-security-group-change-events.png` |
| Local group changes | `screenshots/lab-29-20-local-group-change-events.png` |
| Administrator-specific search | `screenshots/lab-29-21-local-admin-specific-search-no-results.png` |
| DC01 post-lab checkpoint | `screenshots/lab-29-22-dc01-post-lab-checkpoint.png` |
| LOG01 post-lab checkpoint | `screenshots/lab-29-23-log01-post-lab-checkpoint.png` |

---

## Troubleshooting Notes

### Offline Installation

`MRTG-LOG01` did not have direct internet access.

The installer was transferred through a controlled staging folder:

```text
C:\Installers
```

This allowed the installation to proceed without enabling internet access on the server.

### Account Management Search Returned Zero Results

The account lifecycle search returned no results because the current input contained LOG01’s local Security log.

Domain account lifecycle events are generated and audited primarily on domain controllers.

This result identified a collection gap rather than proving that no account activity existed.

### Administrator Keyword Search Returned Zero Results

The search below returned no results:

```spl
index=main sourcetype="WinEventLog:Security" EventCode=4732 Administrators
```

The event existed, but the target group value may not have been indexed as a searchable raw keyword in the expected format.

The next step would be to inspect the parsed fields and search the actual group-name field.

---

## Security Considerations

The Splunk administrator password was not exposed in the evidence.

Production security should also include:

- Restricted Splunk administrator membership
- Role-based access control
- TLS for Splunk Web
- Secure management ports
- Firewall restrictions
- Protected service credentials
- Configuration backups
- Audit Trail monitoring
- Separation of search and administrative roles
- Controlled app installation
- Index access restrictions
- Data retention requirements
- Time synchronization
- Capacity monitoring

---

## Real-World Relevance

SIEM platforms are central to IAM operations because identity attacks leave evidence in authentication and authorization logs.

Common monitoring use cases include:

- Password spraying
- Brute-force attempts
- Disabled account use
- Service account failures
- New account creation
- Privileged account enablement
- Domain Admin membership changes
- Local Administrators group changes
- Suspicious logon types
- After-hours authentication
- Lateral movement
- Repeated lockouts

This lab established the collection and search fundamentals required to build those detections.

---

## What I Would Do Differently in Production

In a production or government-regulated environment, I would implement:

- Splunk Universal Forwarders on domain controllers and endpoints
- A dedicated Windows Security index
- TLS-protected event forwarding
- Standardized host and source naming
- Index retention policies
- Splunk role-based access control
- Separate Splunk administrator accounts
- Domain controller account-management monitoring
- Alerts for repeated Event ID `4625`
- Alerts for privileged group changes
- Monitoring for Domain Admin membership
- Monitoring for local Administrators changes
- Dashboards for identity activity
- Correlation of authentication events across systems
- Time synchronization validation
- Configuration backups
- Health monitoring for missing forwarders
- Documented alert ownership and response procedures
- Ticket integration
- Formal detection testing

---

## Lessons Learned

- Installing a SIEM does not create visibility by itself
- The correct logs must be collected
- Data source scope determines what a search can prove
- Event ID `4624` identifies successful logons
- Event ID `4625` identifies failed logons
- Account lifecycle events should be collected from domain controllers
- Event IDs `4732` and `4733` support local group monitoring
- Zero-result searches can reveal data gaps or weak search logic
- Keyword searches should be replaced with parsed-field searches when possible
- Failed logons require context before being labeled suspicious
- Local virtual accounts can reduce unnecessary domain access
- Useful SIEM monitoring depends on collection, searching, tuning, and response

---

## Skills Demonstrated

- Splunk Enterprise installation
- Offline software staging
- Windows service validation
- Splunk Web administration
- Windows Security log ingestion
- SPL search construction
- Successful logon analysis
- Failed logon analysis
- Account lifecycle monitoring
- Security group change monitoring
- Local administrator monitoring
- Zero-result analysis
- SIEM scope assessment
- Least-privilege service configuration
- Audit evidence collection
- Hyper-V checkpoint management
- Production monitoring planning

---

## Outcome

Lab 29 successfully installed Splunk Enterprise on `MRTG-LOG01` and established a foundation for SIEM identity monitoring.

The lab demonstrated:

- Offline Splunk installation
- Local virtual service account usage
- Splunk Web access
- Windows Security event ingestion
- Successful logon monitoring
- Failed logon monitoring
- Account lifecycle search testing
- Security group change monitoring
- Local group change monitoring
- SPL search tuning
- Data source limitation analysis
- Audit-ready evidence collection
- Pre-lab and post-lab rollback points

The final environment could search identity-related Security events generated on `MRTG-LOG01`.

Full domain-wide identity monitoring would require forwarding Security logs from `MRTG-DC01`, `MRTG-DC02`, and selected endpoints.

---

## Next Lab

[Lab 30 - IAM Operations, Monitoring, and Governance Capstone](../Lab-30-IAM-Operations-Monitoring-and-Governance-Capstone/)

Lab 30 will consolidate service account governance, least-privilege automation, endpoint encryption, local administrator remediation, SIEM monitoring, and operational evidence into the final IAM expansion capstone.
