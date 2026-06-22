# Lab 08: Identity Monitoring and Auditing

![Platform](https://img.shields.io/badge/Platform-Windows%20Server%202022-blue)
![Technology](https://img.shields.io/badge/Technology-Active%20Directory-blue)
![Tooling](https://img.shields.io/badge/Tooling-GPMC%20%26%20Event%20Viewer-purple)
![Focus](https://img.shields.io/badge/Focus-Identity%20Monitoring-orange)
![Security](https://img.shields.io/badge/Security-Audit%20Policy-red)
![Validation](https://img.shields.io/badge/Validation-Security%20Events-brightgreen)

---

## Objective

Implement identity-focused monitoring and auditing in the `mrtg.local` Active Directory domain.

This lab builds on the delegated administration model from Lab 07 by configuring advanced audit policy, applying object-level auditing, performing controlled identity changes, and validating the resulting Windows Security events.

The goal is to make identity activity visible, traceable, and reviewable.

---

## Business Scenario

Monroe Redstone Technology Group requires visibility into administrative identity actions.

Delegated access reduces excessive privilege, but administrative actions must still be logged. Without auditing, password resets, account changes, directory modifications, security group changes, and audit policy changes may occur without enough evidence for investigation or review.

This lab addresses the need to:

- Configure identity-focused audit policy
- Apply audit settings to domain controllers
- Validate effective audit policy
- Configure object-level auditing for user objects
- Generate controlled identity events
- Review the resulting Security log events
- Improve administrative accountability
- Support audit and investigation readiness

---

## Lab Summary

In this lab, I created the `MRTG-DC-Identity-Auditing` GPO and linked it to the Domain Controllers OU.

Advanced audit policy settings were configured for logon activity, user account management, directory service changes, and security group management.

The effective configuration was validated with `gpupdate /force` and `auditpol /get /category:*`.

Object-level auditing was then configured on `_MRTG\Users` so modifications to descendant user objects could generate detailed directory service change events.

Controlled changes were performed against Kevin Carter's account, including a password reset and a Description attribute update. Event IDs `4724`, `4738`, `5136`, and `4719` were then reviewed in Event Viewer.

---

## Environment

| Component | Details |
|---|---|
| Domain | `mrtg.local` |
| Domain Controller | `MRTG-DC01` |
| Directory Service | Active Directory Domain Services |
| Auditing GPO | `MRTG-DC-Identity-Auditing` |
| Object-Auditing Target | `_MRTG\Users` |
| Validation User | Kevin Carter |
| Delegated Administrative Account | `john.smith.admin` |
| Delegated Administrative Group | `GG_IT_HelpDesk_Admins` |
| Tools | Group Policy Management, ADUC, Event Viewer, `gpupdate`, and `auditpol` |
| Virtualization Platform | Hyper-V |
| Organization | Monroe Redstone Technology Group |

---

## Prerequisites

- Operational `mrtg.local` Active Directory domain
- `MRTG-DC01` functioning as a domain controller
- Existing `_MRTG\Users` OU
- Existing test user Kevin Carter
- Delegated administration structure from Lab 07
- Administrative access to Group Policy Management
- Administrative access to object security settings in ADUC
- Access to the Security log on `MRTG-DC01`

---

## Scope

### Included

- Pre-change Hyper-V checkpoint
- Delegated administration baseline review
- Dedicated domain controller auditing GPO
- Advanced audit policy configuration
- Effective policy validation with `gpupdate` and `auditpol`
- Object-level auditing on `_MRTG\Users`
- Controlled password reset
- Controlled user attribute modification
- Event Viewer filtering and review
- Identity-related Security event validation

### Not Included

- Windows Event Forwarding
- Centralized SIEM ingestion
- Microsoft Defender for Identity
- Automated alerting
- Long-term log-retention architecture
- Splunk correlation
- Microsoft Entra ID auditing
- Cloud identity monitoring
- Incident-response automation

---

## Monitoring Architecture

The auditing policy was applied to the domain controller through Group Policy.

```text
Domain Controllers OU
`-- MRTG-DC-Identity-Auditing
    |-- Audit Logon
    |-- Audit User Account Management
    |-- Audit Directory Service Changes
    `-- Audit Security Group Management
```

Object-level auditing was applied to the user-management scope.

```text
mrtg.local
`-- _MRTG
    `-- Users
        `-- Descendant user objects
```

The validation flow was:

```text
Create temporary lab checkpoint
        |
        v
Review delegated administration baseline
        |
        v
Link and configure auditing GPO
        |
        v
Apply and validate effective audit policy
        |
        v
Configure object-level auditing
        |
        v
Perform controlled identity changes
        |
        v
Review generated Security events
```

---

## Audit Policy Strategy

| Audit Subcategory | Configuration | Purpose |
|---|---|---|
| Audit Logon | Success and Failure | Records sign-in activity occurring on the domain controller |
| Audit User Account Management | Success and Failure | Records user-management actions such as password resets and account changes |
| Audit Directory Service Changes | Success | Records directory changes when the target object has a matching audit entry |
| Audit Security Group Management | Success and Failure | Records security group management activity |

Audit Logon on a domain controller records logons to that system. Broader domain authentication monitoring may also require Kerberos and credential-validation audit subcategories.

---

## Object-Level Auditing Strategy

Advanced audit policy enables the event category, but detailed directory modification events also depend on a matching System Access Control List entry on the target object.

An audit entry was configured on `_MRTG\Users` with inheritance to descendant user objects.

| Setting | Value |
|---|---|
| Principal | `Everyone` |
| Audit Type | `Success` |
| Applies To | `Descendant User objects` |

This enabled qualifying modifications to user objects under `_MRTG\Users` to generate Event ID `5136`.

In a production environment, the audited properties and principals should be scoped carefully to avoid excessive event volume.

---

## Event Validation Strategy

| Event ID | Windows Description | Validation Purpose |
|---|---|---|
| `4724` | An attempt was made to reset an account's password | Confirms password-reset attempt auditing |
| `4738` | A user account was changed | Confirms user-account change auditing |
| `5136` | A directory service object was modified | Confirms detailed directory object auditing |
| `4719` | System audit policy was changed | Confirms visibility into audit-policy changes |

Event IDs should be reviewed with their Subject, Target Account, Attribute, timestamp, and status details. The event ID alone does not provide the full investigation context.

---

## Implementation and Validation

### 1. Created a Pre-Change Lab Checkpoint

A Hyper-V checkpoint was created before modifying the audit configuration.

![Pre-auditing checkpoint](screenshots/lab-08-01-pre-auditing-checkpoint.png)

The checkpoint served as a temporary recovery point for the controlled lab environment. It was not treated as a replacement for a supported backup.

---

### 2. Reviewed the Delegated Administration Baseline

The delegated administration structure from Lab 07 was reviewed.

`john.smith.admin` remained a member of:

```text
GG_IT_HelpDesk_Admins
```

The `_MRTG\Users` scope was also confirmed.

![Delegated admin baseline](screenshots/lab-08-02-delegated-admin-baseline.png)

This established the starting identity-administration model before auditing was enabled.

---

### 3. Linked the Identity Auditing GPO

A dedicated GPO was created and linked to the Domain Controllers OU.

```text
MRTG-DC-Identity-Auditing
```

![DC identity auditing GPO linked](screenshots/lab-08-03-dc-identity-auditing-gpo-linked.png)

Using a dedicated GPO keeps the audit configuration separate from the Default Domain Controllers Policy and makes the settings easier to review and maintain.

---

### 4. Configured Audit Logon

Audit Logon was enabled for:

```text
Success
Failure
```

![Audit Logon configured](screenshots/lab-08-04-audit-logon-configured.png)

This provides visibility into sign-in activity occurring on the domain controller.

---

### 5. Configured Audit User Account Management

Audit User Account Management was enabled for:

```text
Success
Failure
```

![Audit User Account Management configured](screenshots/lab-08-05-audit-user-account-management-configured.png)

This supports monitoring of password resets and other user-account management actions.

---

### 6. Configured Audit Directory Service Changes

Audit Directory Service Changes was enabled for:

```text
Success
```

![Audit Directory Service Changes configured](screenshots/lab-08-06-audit-directory-service-changes-configured.png)

This enables detailed directory modification events when the affected object has a matching audit entry.

---

### 7. Configured Audit Security Group Management

Audit Security Group Management was enabled for:

```text
Success
Failure
```

![Audit Security Group Management configured](screenshots/lab-08-07-audit-security-group-management-configured.png)

This improves visibility into changes that may affect authorization and privileged access.

---

### 8. Applied and Verified the Audit Policy

The Group Policy update was applied and the effective audit configuration was reviewed.

Commands used:

```cmd
gpupdate /force
auditpol /get /category:*
```

Validated areas included:

- Logon
- User Account Management
- Directory Service Changes
- Security Group Management

![GPUpdate and auditpol verification](screenshots/lab-08-08-gpupdate-and-auditpol-verification.png)

This confirmed that the intended audit settings were active on `MRTG-DC01`.

---

### 9. Opened the Users OU Auditing Configuration

Advanced Features were enabled in Active Directory Users and Computers.

The advanced security settings for `_MRTG\Users` were opened and the Auditing tab was reviewed.

![Users OU auditing tab](screenshots/lab-08-09-users-ou-auditing-tab.png)

This provided access to the object-level auditing configuration.

---

### 10. Added the Descendant User Audit Entry

An audit entry was created for user objects beneath `_MRTG\Users`.

| Setting | Value |
|---|---|
| Principal | `Everyone` |
| Type | `Success` |
| Applies To | `Descendant User objects` |

![Users OU auditing entry](screenshots/lab-08-10-users-ou-auditing-entry.png)

This completed the object-level configuration required for qualifying directory changes to generate Event ID `5136`.

---

### 11. Performed a Controlled Password Reset

Kevin Carter's password was reset as a controlled validation action.

Selected option:

```text
User must change password at next logon
```

![Password reset action](screenshots/lab-08-11-password-reset-action.png)

This generated user-account management activity for audit validation.

---

### 12. Performed a Controlled Attribute Change

Kevin Carter's Description attribute was updated.

```text
Lab 08 audit validation change
```

![User attribute change](screenshots/lab-08-12-user-attribute-change.png)

This generated a controlled directory object modification.

---

### 13. Validated Event ID 4724

The Security log was filtered for Event ID `4724`.

```text
An attempt was made to reset an account's password.
```

![Event 4724 password reset](screenshots/lab-08-13-event-4724-password-reset.png)

This confirmed that the password-reset attempt was recorded.

---

### 14. Validated Event ID 4738

The Security log was filtered for Event ID `4738`.

```text
A user account was changed.
```

![Event 4738 user account changed](screenshots/lab-08-14-event-4738-user-account-changed.png)

This confirmed that the user-account change was recorded.

---

### 15. Validated Event ID 5136

The Security log was filtered for Event ID `5136`.

```text
A directory service object was modified.
```

![Event 5136 directory object modified](screenshots/lab-08-15-event-5136-directory-object-modified.png)

This confirmed that advanced audit policy and object-level auditing worked together to record the directory modification.

---

### 16. Validated Event ID 4719

The Security log was filtered for Event ID `4719`.

```text
System audit policy was changed.
```

![Event 4719 audit policy changed](screenshots/lab-08-16-event-4719-audit-policy-changed.png)

This confirmed that changes to system audit policy were visible in the Security log.

---

## Security and IAM Relevance

Delegated administration and identity monitoring must work together.

Least privilege limits what an administrator can do. Auditing provides evidence of what occurred.

This lab supports:

- Identity activity monitoring
- Administrative accountability
- Password-reset visibility
- User-account change tracking
- Directory modification auditing
- Security group management visibility
- Audit-policy change detection
- Investigation readiness
- Evidence collection for regulated environments

Audit records do not prevent unauthorized activity by themselves. They support detection, investigation, review, and accountability.

---

## Risks Addressed

This lab reduces the risk of:

- Untracked password resets
- Unreviewed user-account changes
- Invisible directory modifications
- Weak accountability for identity actions
- Missing evidence during investigations
- Unvalidated audit configuration
- Unmonitored audit-policy changes
- Poor preparation for centralized security monitoring

---

## Control Mapping

| Control Area | Lab Contribution |
|---|---|
| Identity Monitoring | Enables auditing for identity-related activity |
| Administrative Accountability | Records account-management and directory changes |
| Password-Reset Monitoring | Validates Event ID `4724` |
| User-Change Tracking | Validates Event ID `4738` |
| Directory Auditing | Validates Event ID `5136` |
| Audit-Policy Monitoring | Validates Event ID `4719` |
| Least-Privilege Support | Adds visibility around delegated administrative capabilities |
| Audit Readiness | Captures configuration and event evidence |

---

## Validation Results

| Validation Item | Result |
|---|---|
| Temporary pre-change checkpoint created | Passed |
| Delegated administration baseline reviewed | Passed |
| `MRTG-DC-Identity-Auditing` linked to Domain Controllers OU | Passed |
| Audit Logon configured | Passed |
| Audit User Account Management configured | Passed |
| Audit Directory Service Changes configured | Passed |
| Audit Security Group Management configured | Passed |
| Group Policy update completed | Passed |
| Effective audit policy confirmed with `auditpol` | Passed |
| `_MRTG\Users` auditing configuration opened | Passed |
| Descendant user audit entry created | Passed |
| Controlled password reset performed | Passed |
| Controlled Description attribute change performed | Passed |
| Event ID `4724` reviewed | Passed |
| Event ID `4738` reviewed | Passed |
| Event ID `5136` reviewed | Passed |
| Event ID `4719` reviewed | Passed |

---

## Evidence Collected

| Evidence | File |
|---|---|
| Pre-auditing checkpoint | `screenshots/lab-08-01-pre-auditing-checkpoint.png` |
| Delegated administration baseline | `screenshots/lab-08-02-delegated-admin-baseline.png` |
| Identity auditing GPO link | `screenshots/lab-08-03-dc-identity-auditing-gpo-linked.png` |
| Audit Logon configuration | `screenshots/lab-08-04-audit-logon-configured.png` |
| Audit User Account Management configuration | `screenshots/lab-08-05-audit-user-account-management-configured.png` |
| Audit Directory Service Changes configuration | `screenshots/lab-08-06-audit-directory-service-changes-configured.png` |
| Audit Security Group Management configuration | `screenshots/lab-08-07-audit-security-group-management-configured.png` |
| Group Policy and `auditpol` verification | `screenshots/lab-08-08-gpupdate-and-auditpol-verification.png` |
| Users OU Auditing tab | `screenshots/lab-08-09-users-ou-auditing-tab.png` |
| Descendant user audit entry | `screenshots/lab-08-10-users-ou-auditing-entry.png` |
| Password-reset action | `screenshots/lab-08-11-password-reset-action.png` |
| User attribute change | `screenshots/lab-08-12-user-attribute-change.png` |
| Event ID `4724` | `screenshots/lab-08-13-event-4724-password-reset.png` |
| Event ID `4738` | `screenshots/lab-08-14-event-4738-user-account-changed.png` |
| Event ID `5136` | `screenshots/lab-08-15-event-5136-directory-object-modified.png` |
| Event ID `4719` | `screenshots/lab-08-16-event-4719-audit-policy-changed.png` |

---

## What I Would Improve in Production

In a production environment, I would:

- Forward domain controller Security logs to a centralized SIEM
- Define event-retention and archive requirements
- Increase Security log capacity based on expected event volume
- Alert on password resets and privileged group changes
- Monitor changes made by delegated administrators
- Monitor attempts to disable or modify auditing
- Restrict who can edit audit policy
- Enforce advanced audit subcategory settings over legacy category settings
- Scope object-level audit entries to necessary principals and properties
- Review audit volume before expanding SACL coverage
- Separate audit-policy administration from routine support roles
- Protect logs against unauthorized modification
- Synchronize time across all monitored systems
- Map important events to investigation and response playbooks
- Validate alerts with controlled test activity
- Use supported backups instead of relying on Hyper-V checkpoints

---

## Lessons Learned

This lab reinforced that delegation and auditing are complementary controls.

Delegation limits administrative authority. Auditing records security-relevant activity for review and investigation.

The primary technical lesson was that enabling Audit Directory Service Changes is not sufficient by itself. Detailed directory modification events also require object-level auditing on the relevant OU or object.

Effective identity monitoring requires policy configuration, appropriate audit scope, controlled event generation, event validation, retention, and centralized review.

---

## Outcome

Lab 08 successfully implemented identity-focused auditing in the MRTG Active Directory environment.

The lab confirmed that:

- A dedicated auditing GPO was linked to the Domain Controllers OU
- Identity-related advanced audit settings were configured
- Effective audit policy was validated with `auditpol`
- Object-level auditing was configured on `_MRTG\Users`
- Controlled password and attribute changes were performed
- Event ID `4724` recorded a password-reset attempt
- Event ID `4738` recorded a user-account change
- Event ID `5136` recorded a directory object modification
- Event ID `4719` recorded an audit-policy change

The environment now has a validated local auditing foundation for future event forwarding, centralized monitoring, and identity-focused investigation.

---

## Next Lab

[Lab 09: Password Policy and Account Lockout Hardening](../Lab-09-Password-Policy-and-Account-Lockout-Hardening/)

Lab 09 strengthens domain authentication by configuring and validating password and account-lockout controls.
