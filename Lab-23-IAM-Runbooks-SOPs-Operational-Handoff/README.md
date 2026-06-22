# Lab 23: IAM Runbooks, SOPs, and Operational Handoff

![Platform](https://img.shields.io/badge/Platform-Windows%20Server-blue)
![Technology](https://img.shields.io/badge/Technology-Active%20Directory-blue)
![Tooling](https://img.shields.io/badge/Tooling-PowerShell%20%26%20Markdown-purple)
![Focus](https://img.shields.io/badge/Focus-Operational%20Handoff-orange)
![Documentation](https://img.shields.io/badge/Documentation-SOPs%20%26%20Runbooks-brightgreen)
![Validation](https://img.shields.io/badge/Validation-Handoff%20Package-blue)

---

## Objective

Create an operational documentation and handoff package for the `mrtg.local` Active Directory and IAM environment.

This lab converts earlier lifecycle, recovery, and audit work into Standard Operating Procedures, operational references, ownership documentation, a handoff summary, and a central documentation index.

The goal is to reduce dependence on undocumented administrator knowledge and provide a structured foundation for repeatable IAM operations.

---

## Business Scenario

Monroe Redstone Technology Group requires documentation that allows identity operations to continue during staff transitions, incidents, audits, and routine support.

Without documented procedures:

- Lifecycle tasks may be completed inconsistently
- Access approvals may be missed
- Password resets may bypass identity-verification requirements
- Privileged access reviews may lack evidence
- Recovery references may be difficult to locate
- Process ownership may remain unclear
- Knowledge may be lost when administrators change roles

This lab creates a structured handoff package based on previously completed and validated lab work.

---

## Lab Summary

In this lab, I created a documentation workspace with folders for SOPs, runbooks, handoff documents, references, output, and evidence.

Artifacts from Labs 20, 21, and 22 were copied into the reference structure.

Four SOPs were created:

- Joiner, Mover, and Leaver
- Access Request
- Password Reset
- Privileged Access Review

The package also included:

- Directory Recovery Reference
- Administrative Responsibility Matrix
- Operational Handoff Summary
- IAM Documentation Index

PowerShell was used to inventory the completed files and confirm that the expected package components existed.

This validation confirmed package completeness and file presence. It did not prove that another administrator could successfully execute every procedure without a walkthrough or tabletop exercise.

---

## Environment

| Component | Details |
|---|---|
| Organization | Monroe Redstone Technology Group |
| Domain | `mrtg.local` |
| Original Domain Controller | `MRTG-DC01` |
| Additional Domain Controller | `MRTG-DC02` |
| Active Directory Site | `MRTG-HQ-Site` |
| Lab Root | `C:\MRTG-Labs\Lab-23-IAM-Runbooks-SOPs-Operational-Handoff` |
| SOPs Folder | `sops` |
| Runbooks Folder | `runbooks` |
| Handoff Folder | `handoff` |
| References Folder | `references` |
| Output Folder | `output` |
| Evidence Folder | `evidence` |
| Tools | PowerShell, Markdown, and Hyper-V |
| Hypervisor | Hyper-V |

---

## Prerequisites

- Completed identity lifecycle automation from Lab 20
- Completed recovery-preparation artifacts from Lab 21
- Completed IAM security review from Lab 22
- Access to the prior lab outputs and reports
- Defined operational areas requiring procedures
- Identified primary and backup ownership roles
- Approved documentation structure
- Secure storage location for sensitive operational documentation

---

## Scope

### Included

- Temporary Hyper-V checkpoints
- Documentation workspace creation
- Prior-lab reference collection
- Joiner, Mover, and Leaver SOP
- Access Request SOP
- Password Reset SOP
- Privileged Access Review SOP
- Directory Recovery Reference
- Administrative Responsibility Matrix
- Operational Handoff Summary
- Documentation Index
- PowerShell file-inventory validation

### Not Included

- Active Directory configuration changes
- Formal document approval
- Content walkthrough with another administrator
- Procedure execution testing
- Recovery tabletop exercise
- Emergency-access testing
- Ticketing-system integration
- Compliance-control mapping
- Central documentation platform
- Document version-control system
- Formal RACI approval
- Automated review-date tracking

---

## Operational Handoff Model

```text
Reference Prior Evidence
          |
          v
Create Standard Procedures
          |
          v
Create Recovery Reference
          |
          v
Define Operational Ownership
          |
          v
Create Handoff Summary
          |
          v
Build Documentation Index
          |
          v
Validate Package Contents
          |
          v
Conduct Walkthrough and Approval
```

The walkthrough and formal approval stages were outside this lab's scope.

---

## Documentation Design

| Documentation Type | Purpose |
|---|---|
| SOP | Defines a repeatable operational procedure |
| Runbook Reference | Provides technical validation and recovery references |
| Responsibility Matrix | Identifies primary and backup operational ownership |
| Handoff Summary | Describes the environment and operational priorities |
| Documentation Index | Provides one navigation point for the package |
| References | Connect procedures to earlier lifecycle, recovery, and audit evidence |
| Evidence | Supports validation of package creation |

---

## Documentation Package

```text
C:\MRTG-Labs\Lab-23-IAM-Runbooks-SOPs-Operational-Handoff
|-- evidence
|-- handoff
|   |-- MRTG-Admin-Responsibility-Matrix.md
|   |-- MRTG-IAM-Documentation-Index.md
|   `-- MRTG-Operational-Handoff-Summary.md
|
|-- output
|-- references
|   |-- Lab-20-Identity-Lifecycle-Output
|   |-- Lab-21-Recovery-Runbook
|   |-- Lab-22-Audit-Exports
|   `-- Lab-22-Security-Review-Reports
|
|-- runbooks
|   `-- MRTG-Directory-Recovery-Reference.md
|
`-- sops
    |-- MRTG-Access-Request-SOP.md
    |-- MRTG-Joiner-Mover-Leaver-SOP.md
    |-- MRTG-Password-Reset-SOP.md
    `-- MRTG-Privileged-Access-Review-SOP.md
```

The package was stored locally on `MRTG-DC01` for the lab. This is not a resilient or appropriate long-term documentation location for production.

---

## Implementation and Validation

### 1. Created a Pre-Change Lab Checkpoint

Checkpoint name:

```text
MRTG-DC01_Pre-Lab-23-IAM-Runbooks-SOPs-Operational-Handoff
```

![Pre-Lab 23 checkpoint](screenshots/lab-23-01-pre-lab-23-checkpoint.png)

The checkpoint was a temporary lab recovery point and was not part of the documentation package or backup strategy.

---

### 2. Created the Documentation Workspace

```text
C:\MRTG-Labs\Lab-23-IAM-Runbooks-SOPs-Operational-Handoff
|-- evidence
|-- handoff
|-- output
|-- references
|-- runbooks
`-- sops
```

![Lab 23 folder structure](screenshots/lab-23-02-folder-structure-created.png)

This separated procedures, recovery references, handoff documents, and supporting evidence.

---

### 3. Collected Prior-Lab References

| Reference Folder | Source |
|---|---|
| `Lab-20-Identity-Lifecycle-Output` | Joiner, Mover, and Leaver results |
| `Lab-21-Recovery-Runbook` | Directory-recovery documentation |
| `Lab-22-Audit-Exports` | IAM audit CSV files |
| `Lab-22-Security-Review-Reports` | IAM security-review summary |

![Prior-lab evidence referenced](screenshots/lab-23-03-prior-lab-evidence-referenced.png)

These copies connected the handoff documents to previous work.

Because the references remained on the same server and host, they were organizational copies rather than independent archives.

---

## Standard Operating Procedures

### 4. Created the Joiner, Mover, and Leaver SOP

File:

```text
sops\MRTG-Joiner-Mover-Leaver-SOP.md
```

![Joiner, Mover, and Leaver SOP](screenshots/lab-23-04-joiner-mover-leaver-sop-created.png)

The SOP documented:

- Approved-request requirements
- New-user onboarding
- Department transfers
- Attribute and OU updates
- Old-access removal
- New-access assignment
- User offboarding
- Account disablement
- Evidence requirements
- Post-change validation

A complete Leaver process also requires review of active sessions, non-AD applications, data ownership, devices, certificates, and retention requirements.

---

### 5. Created the Access Request SOP

File:

```text
sops\MRTG-Access-Request-SOP.md
```

The SOP documented:

- Requester identification
- Target-user identification
- Business justification
- Manager or resource-owner approval
- Group-based access assignment
- Membership validation
- Evidence retention
- Escalation for sensitive access

![Access Request and Password Reset SOPs](screenshots/lab-23-05-access-request-and-password-reset-sops-created.png)

---

### 6. Created the Password Reset SOP

File:

```text
sops\MRTG-Password-Reset-SOP.md
```

The SOP documented:

- User identity verification
- Suspicious-request evaluation
- Approved reset execution
- Password change at next sign-in
- User communication
- Ticket documentation
- Escalation requirements

Temporary passwords must never be recorded in tickets, screenshots, chat messages, or public documentation.

A password reset should also consider active compromise, session revocation, and additional account-recovery controls.

---

### 7. Created the Privileged Access Review SOP

File:

```text
sops\MRTG-Privileged-Access-Review-SOP.md
```

![Privileged Access Review SOP](screenshots/lab-23-06-privileged-access-review-sop-created.png)

Review areas included:

- Domain Admins
- Enterprise Admins
- Schema Admins
- Administrators
- Account Operators
- Server Operators
- Backup Operators
- Delegated administration
- LAPS password readers
- AD CS-related groups

The procedure documented:

1. Validate directory health
2. Export direct and effective membership
3. Review Domain Admins separately
4. Review delegated administration
5. Review LAPS readers
6. Review PKI-related access
7. Identify questionable accounts
8. Compare findings with approved records
9. Document findings
10. Assign remediation for validated exceptions

---

## Recovery Reference

### 8. Created the Directory Recovery Reference

File:

```text
runbooks\MRTG-Directory-Recovery-Reference.md
```

![Directory Recovery Reference](screenshots/lab-23-07-directory-recovery-runbook-reference-created.png)

The reference included:

- Domain controller health commands
- DNS and domain-controller discovery
- Replication-health commands
- FSMO role ownership
- System State Backup reference
- GPO backup reference
- Directory inventory
- Privileged-group reference
- Automation-artifact reference
- Post-recovery validation

Key commands:

```cmd
dcdiag /s:MRTG-DC01
repadmin /replsummary
repadmin /showrepl
netdom query fsmo
nltest /dsgetdc:mrtg.local
wbadmin get versions -backuptarget:E:
```

This document is an operational reference. It is not a complete forest-recovery plan and was not tested through a restore exercise.

---

## Handoff Documentation

### 9. Created the Administrative Responsibility Matrix

File:

```text
handoff\MRTG-Admin-Responsibility-Matrix.md
```

![Administrative Responsibility Matrix](screenshots/lab-23-08-admin-responsibility-matrix-created.png)

| Area | Primary Role | Backup Role | Required Evidence |
|---|---|---|---|
| User onboarding | Help Desk or IAM Administrator | Senior Administrator | Request, input data, output, and validation |
| User offboarding | Help Desk or IAM Administrator | Senior Administrator | Request, disablement, access review, and validation |
| Password resets | Help Desk or IAM Administrator | Ticket Owner | Identity verification and ticket record |
| Access requests | IAM Administrator | Department Owner | Approval and membership validation |
| Privileged review | Senior Administrator | Security Reviewer | Membership exports and review summary |
| LAPS reader review | Security Administrator | Senior Administrator | Reader-group and extended-rights review |
| AD CS review | PKI or AD Administrator | Security Administrator | CA and template access review |
| Directory recovery | Senior Administrator | Systems Administrator | Runbook, backup status, and health evidence |
| IAM audit evidence | IAM Administrator | Security Reviewer | Exports, findings, and retained evidence |

The matrix identifies responsibility assignments but was not formally approved as a RACI model.

---

### 10. Created the Operational Handoff Summary

File:

```text
handoff\MRTG-Operational-Handoff-Summary.md
```

![Operational Handoff Summary](screenshots/lab-23-09-operational-handoff-summary-created.png)

The summary documented:

- Environment overview
- Handoff-package contents
- Operational priorities
- Known limitations
- Required evidence
- Ownership references
- Maintenance expectations

Environment components included:

- Active Directory Domain Services
- OU structure
- Department security groups
- Group Policy
- Delegated administration
- Windows LAPS
- Active Directory Certificate Services
- Lifecycle automation
- Recovery artifacts
- IAM review evidence

---

### 11. Created the Documentation Index

File:

```text
handoff\MRTG-IAM-Documentation-Index.md
```

![IAM Documentation Index](screenshots/lab-23-10-documentation-index-created.png)

The index organized:

- SOPs
- Recovery references
- Handoff documents
- Prior-lab references
- Intended operational use

The index provides a central entry point for the package.

---

## Package Validation

### 12. Inventoried the Handoff Files

Command used:

```powershell
$LabRoot = "C:\MRTG-Labs\Lab-23-IAM-Runbooks-SOPs-Operational-Handoff"

Get-ChildItem $LabRoot -Recurse |
    Where-Object { -not $_.PSIsContainer } |
    Select-Object Directory, Name, Length |
    Format-Table -AutoSize
```

![Handoff package validated](screenshots/lab-23-11-handoff-package-validated.png)

Validated handoff documents:

```text
MRTG-Admin-Responsibility-Matrix.md
MRTG-IAM-Documentation-Index.md
MRTG-Operational-Handoff-Summary.md
```

Validated recovery reference:

```text
MRTG-Directory-Recovery-Reference.md
```

Validated SOPs:

```text
MRTG-Access-Request-SOP.md
MRTG-Joiner-Mover-Leaver-SOP.md
MRTG-Password-Reset-SOP.md
MRTG-Privileged-Access-Review-SOP.md
```

This command confirmed that expected files existed and had content. It did not validate technical accuracy, approvals, links, readability, or procedure effectiveness.

---

### 13. Created the Final Lab Checkpoint

Checkpoint name:

```text
MRTG-DC01_Post-Lab-23-IAM-Runbooks-SOPs-Operational-Handoff-Validated
```

![Post-Lab 23 checkpoint](screenshots/lab-23-12-post-lab-23-checkpoint.png)

The checkpoint was a temporary lab tool and was not a documentation backup.

---

## Handoff Deliverables

| Category | Deliverable | Purpose |
|---|---|---|
| SOP | `MRTG-Joiner-Mover-Leaver-SOP.md` | Standardizes lifecycle procedures |
| SOP | `MRTG-Access-Request-SOP.md` | Standardizes access approval and assignment |
| SOP | `MRTG-Password-Reset-SOP.md` | Standardizes identity verification and password-reset handling |
| SOP | `MRTG-Privileged-Access-Review-SOP.md` | Standardizes elevated-access review |
| Runbook Reference | `MRTG-Directory-Recovery-Reference.md` | Provides recovery checks and artifact references |
| Handoff | `MRTG-Admin-Responsibility-Matrix.md` | Identifies primary and backup roles |
| Handoff | `MRTG-Operational-Handoff-Summary.md` | Summarizes the environment and operating priorities |
| Handoff | `MRTG-IAM-Documentation-Index.md` | Provides central package navigation |
| References | Labs 20, 21, and 22 artifacts | Connects procedures to previous evidence |

---

## Validation Results

| Validation Item | Result |
|---|---|
| Temporary pre-change checkpoint created | Passed |
| Documentation workspace created | Passed |
| Prior-lab references collected | Passed |
| Joiner, Mover, and Leaver SOP created | Passed |
| Access Request SOP created | Passed |
| Password Reset SOP created | Passed |
| Privileged Access Review SOP created | Passed |
| Directory Recovery Reference created | Passed |
| Administrative Responsibility Matrix created | Passed |
| Operational Handoff Summary created | Passed |
| Documentation Index created | Passed |
| Expected files inventoried | Passed |
| Formal document approval | Not completed |
| Second-administrator walkthrough | Not completed |
| Procedure execution testing | Not completed |
| Recovery tabletop exercise | Not completed |
| Version-control workflow | Not implemented |
| Temporary final checkpoint created | Passed |

---

## Security and IAM Relevance

IAM operations require technology, process, ownership, evidence, and continuity.

This lab supports:

- Standardized lifecycle operations
- Controlled access requests
- Secure password-reset procedures
- Privileged-access governance
- Recovery documentation
- Primary and backup ownership
- Evidence requirements
- Knowledge transfer
- Operational continuity
- Audit preparation

Documentation is itself a sensitive asset. Recovery instructions, privileged procedures, infrastructure details, and account-management workflows require access control and change monitoring.

---

## Risks Addressed

This lab reduces the risk of:

- Undocumented identity procedures
- Inconsistent lifecycle processing
- Missing access approvals
- Weak password-reset handling
- Unstructured privileged-access reviews
- Unclear administrative ownership
- Difficult-to-locate recovery references
- Knowledge loss during staff transitions
- Missing handoff documentation

The package does not yet mitigate outdated procedures, incorrect content, unauthorized document changes, or untested instructions.

---

## Control Mapping

| Control Area | Lab Contribution |
|---|---|
| Lifecycle Governance | Documents Joiner, Mover, and Leaver procedures |
| Access Governance | Documents approval and group-assignment requirements |
| Account Recovery | Documents password-reset identity verification |
| Privileged Access | Documents recurring review procedures |
| Recovery Readiness | Provides directory-recovery references |
| Operational Ownership | Identifies primary and backup roles |
| Knowledge Transfer | Creates a handoff summary and index |
| Audit Readiness | Defines evidence expectations |
| Documentation Governance | Identifies approval, versioning, and review gaps |

---

## Evidence Collected

| Evidence | Screenshot |
|---|---|
| Pre-change checkpoint | `screenshots/lab-23-01-pre-lab-23-checkpoint.png` |
| Documentation workspace | `screenshots/lab-23-02-folder-structure-created.png` |
| Prior-lab references | `screenshots/lab-23-03-prior-lab-evidence-referenced.png` |
| Lifecycle SOP | `screenshots/lab-23-04-joiner-mover-leaver-sop-created.png` |
| Access Request and Password Reset SOPs | `screenshots/lab-23-05-access-request-and-password-reset-sops-created.png` |
| Privileged Access Review SOP | `screenshots/lab-23-06-privileged-access-review-sop-created.png` |
| Directory Recovery Reference | `screenshots/lab-23-07-directory-recovery-runbook-reference-created.png` |
| Administrative Responsibility Matrix | `screenshots/lab-23-08-admin-responsibility-matrix-created.png` |
| Operational Handoff Summary | `screenshots/lab-23-09-operational-handoff-summary-created.png` |
| Documentation Index | `screenshots/lab-23-10-documentation-index-created.png` |
| Package inventory | `screenshots/lab-23-11-handoff-package-validated.png` |
| Final lab checkpoint | `screenshots/lab-23-12-post-lab-23-checkpoint.png` |

---

## What I Would Improve in Production

In a production environment, I would:

- Store documentation outside the domain controller
- Use a version-controlled documentation platform
- Assign document owners
- Record approval and review dates
- Add document classifications
- Restrict access by role
- Monitor unauthorized changes
- Integrate procedures with the ticketing platform
- Link procedures to approved request forms
- Add escalation contacts and decision points
- Create a formally approved RACI matrix
- Map procedures to applicable controls
- Conduct a second-administrator walkthrough
- Test procedures with controlled scenarios
- Conduct recovery tabletop exercises
- Create emergency-access and break-glass procedures
- Create administrator-onboarding checklists
- Schedule periodic documentation reviews
- Archive superseded versions
- Store references and evidence in protected off-host storage
- Avoid relying on Hyper-V checkpoints for documentation protection

---

## Lessons Learned

This lab reinforced that technical implementation is only one part of IAM maturity.

Procedures must explain:

- Who performs the task
- Who approves the task
- What inputs are required
- Which systems are affected
- How success is validated
- What evidence is retained
- When escalation is required
- Who owns the document

The main lesson was that file existence is not the same as operational validation. A high-quality handoff requires review, approval, walkthroughs, and periodic testing.

---

## Outcome

Lab 23 successfully created a structured IAM operational handoff package.

The package included:

- Four IAM SOPs
- One Directory Recovery Reference
- One Administrative Responsibility Matrix
- One Operational Handoff Summary
- One IAM Documentation Index
- References from Labs 20, 21, and 22

The files were inventoried and confirmed present.

The package provides a strong documentation foundation, but formal approval and second-administrator procedure testing remain necessary before it could be considered production-ready.

---

## Next Lab

[Lab 24: Enterprise IAM Capstone Validation](../Lab-24-Enterprise-IAM-Capstone-Validation/)

Lab 24 validates the MRTG identity environment across architecture, lifecycle operations, privileged access, monitoring, recovery preparation, and operational documentation.
