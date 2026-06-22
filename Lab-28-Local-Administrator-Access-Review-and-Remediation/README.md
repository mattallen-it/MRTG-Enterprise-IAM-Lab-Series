# Lab 28 - Local Administrator Access Review and Remediation

![Platform](https://img.shields.io/badge/Platform-Windows%2011-blue)
![Technology](https://img.shields.io/badge/Technology-Local%20Users%20and%20Groups-blue)
![Focus](https://img.shields.io/badge/Focus-Local%20Admin%20Review-green)
![Security](https://img.shields.io/badge/Security-Least%20Privilege-red)
![Validation](https://img.shields.io/badge/Validation-Completed-brightgreen)
![Documentation](https://img.shields.io/badge/Documentation-Audit%20Ready-purple)

---

## Overview

In this lab, I reviewed local administrator access on `MRTG-CLIENT-01`, identified unnecessary privileged access, completed remediation, and validated the corrected state.

The initial review found the following members in the local Administrators group:

```text
Administrator
localadmin
MRTG\Domain Admins
```

The standalone `localadmin` account was identified as unnecessary and removed.

After remediation, the local Administrators group contained:

```text
Administrator
MRTG\Domain Admins
```

This lab demonstrated a complete privileged access review workflow: establish a baseline, identify a finding, remediate the finding, and validate the result.

---

## Business Problem

MRTG needed to periodically review local administrator access on endpoints to ensure privileged access remained limited, justified, and controlled.

Local administrators can:

- Install software
- Modify system settings
- Disable security controls
- Access protected local data
- Create additional privileged accounts
- Change file permissions
- Establish persistence
- Support credential theft or lateral movement

Unnecessary membership in the local Administrators group increases endpoint risk and weakens least privilege.

This lab addressed that problem by reviewing the current membership, removing an unnecessary account, and preserving before-and-after evidence.

---

## Lab Summary

I created pre-lab checkpoints for `MRTG-DC01` and `MRTG-CLIENT-01`.

I reviewed the client’s local Administrators group through Local Users and Groups and confirmed its membership with `net localgroup administrators`.

The `localadmin` account was identified as unnecessary. The first remediation attempt failed with an access-denied error because the active session lacked the required administrative context.

After using the correct administrative context, I removed `localadmin` and validated the resulting membership through both the command line and graphical interface.

Finally, I created post-lab checkpoints for both systems.

---

## Objectives

- Create pre-lab checkpoints
- Review the local Administrators group
- Establish a command-line membership baseline
- Identify unnecessary local administrator access
- Document the initial remediation failure
- Use the correct administrative context
- Remove the unnecessary account
- Validate the corrected membership with PowerShell
- Validate the corrected membership through the GUI
- Document before-and-after evidence
- Create post-lab checkpoints

---

## Scope

### Included

- Hyper-V checkpoints
- Local Administrators group review
- GUI-based membership validation
- Command-line membership validation
- Privileged access finding documentation
- Local administrator removal
- Administrative context troubleshooting
- Before-and-after comparison
- Least-privilege analysis
- Audit evidence collection

### Not Included

- Removal of `MRTG\Domain Admins`
- Windows LAPS reconfiguration
- Local administrator password rotation
- Group Policy enforcement of approved membership
- Microsoft Intune account protection policies
- Enterprise-wide endpoint scanning
- Just-in-time privilege
- Privileged access management deployment
- SIEM alert creation
- Production change approval

---

## Environment

| Component | Details |
|---|---|
| Organization | Monroe Redstone Technology Group |
| Domain | `mrtg.local` |
| Domain Controller | `MRTG-DC01` |
| Endpoint | `MRTG-CLIENT-01` |
| Local Group | `Administrators` |
| Account Removed | `localadmin` |
| Retained Local Account | `Administrator` |
| Retained Domain Group | `MRTG\Domain Admins` |
| Management Tools | Local Users and Groups, PowerShell |
| Validation Command | `net localgroup administrators` |
| Hypervisor | Hyper-V |

---

## Scenario

MRTG needs to review privileged access on a Windows endpoint.

The review must determine:

- Who currently has local administrator access
- Whether each member is still required
- Which access should be removed
- Whether the reviewer has authority to perform remediation
- Whether the final state matches the approved lab baseline

The review model used in this lab was:

```text
Establish Baseline → Assess Membership → Attempt Remediation → Correct Administrative Context → Remove Access → Validate Final State
```

---

## Review Criteria

Each local Administrators group member was evaluated against the following criteria:

| Criterion | Review Question |
|---|---|
| Identity | Is the account or group clearly identifiable? |
| Purpose | Is there a documented reason for local administrator access? |
| Ownership | Is a person or team responsible for the access? |
| Necessity | Does the identity still require local administrator rights? |
| Privilege | Is local administrator access broader than required? |
| Governance | Is the account controlled through an approved process? |
| Monitoring | Can privileged activity be reviewed? |
| Lifecycle | Will the access be removed when no longer required? |

---

## Implementation Steps

### Step 1 - Created DC01 Pre-Lab Checkpoint

A checkpoint was created for `MRTG-DC01` before beginning the review.

Checkpoint name:

```text
MRTG-DC01_Pre-Lab28-Local-Admin-Access-Review
```

![DC01 Pre-Lab Checkpoint](screenshots/lab-28-01-dc01-pre-lab-checkpoint.png)

---

### Step 2 - Created CLIENT-01 Pre-Lab Checkpoint

A checkpoint was created for `MRTG-CLIENT-01` before modifying local administrator membership.

Checkpoint name:

```text
MRTG-CLIENT-01_Pre-Lab28-Local-Admin-Access-Review
```

![CLIENT-01 Pre-Lab Checkpoint](screenshots/lab-28-02-client01-pre-lab-checkpoint.png)

---

### Step 3 - Reviewed the Local Administrators Group

The local Administrators group was reviewed through Local Users and Groups.

Navigation path:

```text
Local Users and Groups
└── Groups
    └── Administrators
```

Initial membership:

```text
Administrator
localadmin
MRTG\Domain Admins
```

![Local Administrators Group Before Review](screenshots/lab-28-03-local-administrators-group-before-review.png)

---

## Baseline Findings

| Account or Group | Identity Type | Finding | Lab Decision |
|---|---|---|---|
| `Administrator` | Built-in local account | Expected privileged account | Retain and control |
| `localadmin` | Standalone local account | No longer required | Remove |
| `MRTG\Domain Admins` | Domain security group | Retained for current lab administration | Retain for lab only |

The primary remediation target was:

```text
localadmin
```

---

### Step 4 - Documented the Access-Denied Error

The first attempt to remove `localadmin` failed.

Observed error:

```text
The following error occurred while attempting to save properties for group Administrators on computer CLIENT01:

Access is denied.
```

The active session could view membership but did not have sufficient effective rights to modify the local Administrators group.

![Local Admin Remediation Access Denied](screenshots/lab-28-04-local-admin-remediation-access-denied.png)

---

## Administrative Context Analysis

The error demonstrated an important distinction:

```text
Visibility does not equal modification authority.
```

A user may be able to open a management console and inspect membership without having permission to change it.

Privileged remediation requires:

- The correct administrative account
- An elevated process
- Authority on the target endpoint
- A valid change or remediation purpose
- Evidence of the resulting state

The error was resolved by performing the remediation under the correct administrative context.

---

### Step 5 - Validated Membership Before Remediation

The baseline membership was confirmed from an elevated PowerShell session.

Command used:

```powershell
net localgroup administrators
```

Command output confirmed:

```text
Administrator
localadmin
MRTG\Domain Admins
```

This command-line evidence matched the graphical baseline.

![Net Localgroup Administrators Before Remediation](screenshots/lab-28-05-net-localgroup-administrators-before-remediation.png)

---

### Step 6 - Removed the Unnecessary Local Administrator

After using the correct administrative context, `localadmin` was removed from the local Administrators group.

Remediation performed:

```text
Remove localadmin from the local Administrators group
```

The account itself was not documented as deleted. This lab removed its privileged group membership.

That distinction matters because removing group membership reduces privilege, while deleting or disabling the account is a separate lifecycle action.

---

### Step 7 - Validated Membership After Remediation

The corrected group membership was validated with PowerShell.

Command used:

```powershell
net localgroup administrators
```

Final membership:

```text
Administrator
MRTG\Domain Admins
```

The `localadmin` account was no longer listed.

![Net Localgroup Administrators After Remediation](screenshots/lab-28-06-net-localgroup-administrators-after-remediation.png)

---

### Step 8 - Confirmed the Final State Through the GUI

Local Users and Groups was reviewed again after remediation.

The graphical view confirmed the final membership:

```text
Administrator
MRTG\Domain Admins
```

This provided a second validation method for the completed change.

![Local Administrators Group After Review](screenshots/lab-28-07-local-administrators-group-after-review.png)

---

### Step 9 - Created DC01 Post-Lab Checkpoint

A post-lab checkpoint was created for `MRTG-DC01`.

Checkpoint name:

```text
MRTG-DC01_Post-Lab28-Local-Admin-Access-Review-Validated
```

![DC01 Post-Lab Checkpoint](screenshots/lab-28-08-dc01-post-lab-checkpoint.png)

---

### Step 10 - Created CLIENT-01 Post-Lab Checkpoint

A post-lab checkpoint was created for `MRTG-CLIENT-01`.

Checkpoint name:

```text
MRTG-CLIENT-01_Post-Lab28-Local-Admin-Access-Review-Validated
```

![CLIENT-01 Post-Lab Checkpoint](screenshots/lab28-client01-post-lab-checkpoint.png)

---

## Before and After Comparison

| Review Stage | Administrator | localadmin | MRTG\Domain Admins |
|---|---|---|---|
| Before remediation | Present | Present | Present |
| After remediation | Present | Removed | Present |

---

## Access Review Results

| Account or Group | Before | After | Final Decision |
|---|---|---|---|
| `Administrator` | Local administrator | Local administrator | Retained |
| `localadmin` | Local administrator | Standard local account | Privileged membership removed |
| `MRTG\Domain Admins` | Local administrator | Local administrator | Retained for lab administration |

---

## Important Production Finding

Retaining `MRTG\Domain Admins` in the local Administrators group was acceptable for this lab’s current management model, but it is not the strongest production design.

Domain Admin credentials should generally not be used for routine workstation administration.

A stronger model would use:

- A dedicated workstation administrator group
- Separate privileged administrator accounts
- Windows LAPS for local administrator recovery
- Just-in-time privileged access
- Privileged access workstations
- Restricted Domain Admin logon rights
- Tiered administrative boundaries

This reduces the chance that a compromised workstation exposes highly privileged domain credentials.

---

## LAPS and Local Administrator Governance

This lab did not repeat the Windows LAPS deployment completed in Lab 17.

Instead, it demonstrated the review and remediation process that should operate alongside LAPS.

A mature local administrator control model includes:

- LAPS-managed local administrator passwords
- Restricted Administrators group membership
- Separate administrator identities
- Periodic access reviews
- Removal of unnecessary accounts
- Monitoring for privileged logons
- Alerts for group membership changes
- Documented business justification
- Account ownership
- Defined access expiration

LAPS protects local administrator credentials. It does not automatically determine who should belong to the local Administrators group.

---

## Risk Addressed

This lab addressed risks including:

- Excessive local administrator access
- Unnecessary privileged local accounts
- Weak endpoint privilege governance
- Unreviewed administrator membership
- Privileged access persistence
- Lack of remediation evidence
- Failure to validate changes
- Confusion between visibility and modification authority
- Use of overly broad domain privileges on endpoints

---

## Control Mapping

| Control Area | Lab Implementation |
|---|---|
| Least privilege | Removed unnecessary local administrator membership |
| Privileged access review | Reviewed every member of the local Administrators group |
| Endpoint security | Reduced privileged exposure on the workstation |
| Access remediation | Removed `localadmin` from Administrators |
| Administrative authorization | Used the correct context to complete remediation |
| Dual validation | Confirmed the result through PowerShell and the GUI |
| Audit readiness | Captured baseline, error, remediation, and final evidence |
| Local admin governance | Connected the review to LAPS and recurring access controls |
| Change protection | Created pre-lab and post-lab checkpoints |

---

## Validation Summary

| Test | Expected Result | Actual Result | Status |
|---|---|---|---|
| DC01 pre-lab checkpoint created | Rollback point exists | Checkpoint created | Passed |
| CLIENT-01 pre-lab checkpoint created | Client rollback point exists | Checkpoint created | Passed |
| GUI baseline reviewed | Current members visible | Three members identified | Passed |
| Access finding documented | Unnecessary access identified | `localadmin` identified | Passed |
| Initial failure documented | Incorrect context produces error | Access denied captured | Passed |
| Command-line baseline validated | CLI matches GUI | Three members confirmed | Passed |
| Correct administrative context used | Remediation permitted | Access removed | Passed |
| `localadmin` remediated | Account no longer privileged | Membership removed | Passed |
| Command-line final state validated | `localadmin` absent | Two members confirmed | Passed |
| GUI final state validated | GUI matches CLI | Two members confirmed | Passed |
| DC01 post-lab checkpoint created | Final state preserved | Checkpoint created | Passed |
| CLIENT-01 post-lab checkpoint created | Client state preserved | Checkpoint created | Passed |

---

## Evidence Collected

| Evidence | Screenshot |
|---|---|
| DC01 pre-lab checkpoint | `screenshots/lab-28-01-dc01-pre-lab-checkpoint.png` |
| CLIENT-01 pre-lab checkpoint | `screenshots/lab-28-02-client01-pre-lab-checkpoint.png` |
| Local Administrators group before review | `screenshots/lab-28-03-local-administrators-group-before-review.png` |
| Access-denied remediation attempt | `screenshots/lab-28-04-local-admin-remediation-access-denied.png` |
| Command-line membership before remediation | `screenshots/lab-28-05-net-localgroup-administrators-before-remediation.png` |
| Command-line membership after remediation | `screenshots/lab-28-06-net-localgroup-administrators-after-remediation.png` |
| Local Administrators group after review | `screenshots/lab-28-07-local-administrators-group-after-review.png` |
| DC01 post-lab checkpoint | `screenshots/lab-28-08-dc01-post-lab-checkpoint.png` |
| CLIENT-01 post-lab checkpoint | `screenshots/lab28-client01-post-lab-checkpoint.png` |

---

## Troubleshooting Notes

The first removal attempt failed with:

```text
Access is denied.
```

The failure occurred because the active session did not have sufficient effective administrative rights on `MRTG-CLIENT-01`.

The remediation workflow was:

```text
Review Error → Confirm Administrative Context → Elevate Correctly → Remove Membership → Validate Result
```

The error was not bypassed by granting broader permanent access. The change was completed through an authorized administrative context.

---

## Security Considerations

Removing `localadmin` from the Administrators group reduced its privilege but did not necessarily disable or delete the account.

A production review should also determine:

- Whether the account is still required
- Whether it should be disabled
- Whether it should be deleted after retention requirements are met
- Whether it has other local group memberships
- Whether it has active sessions
- Whether it owns files, services, or scheduled tasks
- Whether it has recently authenticated
- Whether its password is still valid
- Whether related credentials exist elsewhere

Privilege removal and account lifecycle management are related but separate processes.

---

## Real-World Relevance

Local administrator reviews are common in:

- Endpoint security operations
- IAM access certifications
- Privileged access management
- Help desk administration
- Compliance assessments
- Incident response
- Cybersecurity maturity reviews
- Government and defense environments

A complete review should answer:

- Who has local administrator access?
- Why do they have it?
- Who approved it?
- Is the access still required?
- When was it last reviewed?
- Can the access be reduced?
- Was remediation validated?
- Is evidence available for an auditor?

This lab demonstrated that complete workflow on a single endpoint.

---

## What I Would Do Differently in Production

In a production or government-regulated environment, I would implement:

- Centralized local administrator inventory
- Recurring access reviews
- Windows LAPS enforcement
- Dedicated workstation administrator groups
- Removal of Domain Admins from routine workstation administration
- Separate privileged accounts
- Restricted privileged logon paths
- Just-in-time access
- Just-enough administration
- Privileged access workstations
- Group Policy or Intune membership enforcement
- Alerts for additions to local Administrators
- SIEM monitoring for privileged local logons
- Change tickets for membership changes
- Business justification and access expiration
- Account disablement after privilege removal when appropriate
- Automated compliance reporting

---

## Lessons Learned

- Local administrator access requires recurring review
- LAPS does not replace membership governance
- Viewing privileged membership does not provide authority to change it
- Administrative context must be validated before remediation
- Access-denied errors should not automatically lead to broader permissions
- Privileged findings require remediation and validation
- GUI and command-line evidence should agree
- Removing group membership is different from deleting an account
- Domain Admins should not be the default workstation administration model
- Before-and-after evidence improves audit readiness
- Least privilege is an ongoing process

---

## Skills Demonstrated

- Local administrator access review
- Privileged access analysis
- Windows Local Users and Groups management
- `net localgroup` validation
- Least-privilege remediation
- Administrative context troubleshooting
- Before-and-after validation
- Endpoint privilege governance
- Windows LAPS governance analysis
- Audit evidence collection
- Hyper-V checkpoint management
- Production security planning

---

## Outcome

Lab 28 successfully reviewed and remediated local administrator exposure on `MRTG-CLIENT-01`.

The lab demonstrated:

- Pre-change rollback planning
- GUI and command-line membership review
- Identification of unnecessary local administrator access
- Administrative context troubleshooting
- Removal of `localadmin`
- Dual-method final validation
- Local administrator governance analysis
- Audit-ready before-and-after evidence
- Post-change rollback planning

The endpoint finished the lab with the unnecessary `localadmin` membership removed from the local Administrators group.

---

## Next Lab

[Lab 29 - SIEM Identity Monitoring with Splunk](../Lab-29-SIEM-Identity-Monitoring-with-Splunk/)

Lab 29 will focus on collecting identity-related Windows events, searching security logs, and using Splunk to identify authentication and account activity patterns.
