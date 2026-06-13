# Lab 08 — Identity Monitoring and Auditing

![Platform](https://img.shields.io/badge/Platform-Windows%20Server%202022-blue)
![Technology](https://img.shields.io/badge/Technology-Active%20Directory-blue)
![Tooling](https://img.shields.io/badge/Tooling-GPMC%20%26%20Event%20Viewer-purple)
![Focus](https://img.shields.io/badge/Focus-Identity%20Monitoring-orange)
![Security](https://img.shields.io/badge/Security-Audit%20Policy-red)
![Validation](https://img.shields.io/badge/Validation-Security%20Events-brightgreen)

---

## Objective

The objective of this lab is to implement identity-focused monitoring and auditing in the `mrtg.local` Active Directory domain.

This lab builds on the delegated administration model from Lab 07 by configuring audit policy, applying object-level auditing, performing controlled identity changes, and validating the resulting security events.

The focus is on making identity activity visible, traceable, and reviewable.

---

## Business Problem

Monroe Redstone Technology Group needs visibility into administrative identity actions.

Delegated access is useful, but it must also be monitored. If password resets, user account changes, directory object changes, security group changes, or audit policy changes are not logged, the organization has weak accountability and limited investigation capability.

This lab addresses the need to:

- Configure identity-focused audit policy
- Apply auditing to domain controllers
- Validate audit policy application
- Configure object-level auditing for user objects
- Perform controlled identity changes
- Validate security events in Event Viewer
- Improve visibility into administrative identity changes
- Support audit readiness for regulated environments

---

## Lab Summary

In this lab, I created a dedicated identity auditing GPO and linked it to the Domain Controllers OU.

I configured advanced audit policy settings for logon activity, user account management, directory service changes, and security group management.

I validated the applied audit policy using `gpupdate /force` and `auditpol /get /category:*`.

I then configured object-level auditing on the `_MRTG/Users` OU so changes to descendant user objects could generate detailed directory service change events.

Finally, I performed controlled identity changes against Kevin Carter by resetting the user password and modifying the Description field. The resulting events were validated in Event Viewer using Event IDs `4724`, `4738`, `5136`, and `4719`.

---

## Environment

| Component | Details |
|---|---|
| Domain | `mrtg.local` |
| Domain Controller | `MRTG-DC01` |
| Directory Service | Active Directory Domain Services |
| Management Tools | Group Policy Management Console, ADUC, Event Viewer |
| Auditing GPO | `MRTG-DC-Identity-Auditing` |
| Delegated Admin Account | `john.smith.admin` |
| Delegated Admin Group | `GG_IT_HelpDesk_Admins` |
| Auditing Target | `_MRTG/Users` |
| Validation User | `Kevin Carter` |
| Virtualization Platform | Hyper-V |
| Lab Organization | Monroe Redstone Technology Group |

---

## Scope

### Included

- Pre-lab Hyper-V checkpoint
- Delegated administration baseline review
- Dedicated domain controller auditing GPO
- Advanced audit policy configuration
- Audit policy validation with `gpupdate` and `auditpol`
- Object-level auditing on `_MRTG/Users`
- Controlled password reset action
- Controlled user attribute change
- Event Viewer validation
- Security event review

### Not Included

- SIEM integration
- Windows Event Forwarding
- Microsoft Defender for Identity
- Centralized alerting
- Long-term log retention architecture
- Splunk correlation
- Entra ID auditing
- Cloud identity monitoring

---

## Architecture

This lab used a single-domain Active Directory environment with audit policy applied at the domain controller level.

```text
mrtg.local
└── _MRTG
    ├── Users
    │   ├── Engineering
    │   ├── Executives
    │   ├── Finance
    │   ├── HR
    │   │   └── Kevin Carter
    │   ├── IT
    │   ├── Operations
    │   └── Security
    ├── Admin Accounts
    │   └── john.smith.admin
    └── Groups
        └── GG_IT_HelpDesk_Admins
```

Audit policy was applied through:

```text
Domain Controllers OU
└── MRTG-DC-Identity-Auditing GPO
```

Object-level auditing was applied to:

```text
_MRTG/Users
└── Descendant User objects
```

The high-level flow was:

```text
Pre-lab checkpoint created
→ Delegated admin baseline confirmed
→ Audit GPO linked to Domain Controllers OU
→ Advanced audit policy configured
→ Audit policy applied and validated
→ Object-level auditing configured
→ Controlled identity changes performed
→ Security events validated
```

---

## Audit Policy Strategy

The auditing strategy focused on identity-related activity that matters for IAM operations.

| Audit Area | Purpose |
|---|---|
| Logon Events | Tracks successful and failed authentication activity |
| User Account Management | Tracks user account changes such as password resets |
| Directory Service Changes | Tracks Active Directory object modifications |
| Security Group Management | Tracks changes to security groups |

This provides visibility into actions that affect authentication, access, and identity state.

---

## Object-Level Auditing Strategy

Advanced audit policy alone is not enough to capture detailed directory object changes.

To capture meaningful changes to users under `_MRTG/Users`, object-level auditing was configured on the OU.

The auditing entry used:

| Setting | Value |
|---|---|
| Principal | `Everyone` |
| Type | `Success` |
| Applies To | `Descendant User objects` |

This allows changes to user objects under `_MRTG/Users` to generate directory service change events.

---

## Event Validation Strategy

The following security events were validated in Event Viewer.

| Event ID | Event Meaning | Lab Purpose |
|---|---|---|
| `4724` | An attempt was made to reset an account password | Validates password reset auditing |
| `4738` | A user account was changed | Validates user account change auditing |
| `5136` | A directory service object was modified | Validates object-level directory auditing |
| `4719` | System audit policy was changed | Validates audit policy change visibility |

---

## Implementation and Validation

### 1. Pre-Lab Checkpoint Created

A Hyper-V checkpoint was created before changing audit policy.

This preserved the clean post-Lab-07 environment.

![Pre-auditing checkpoint](screenshots/lab-08-01-pre-auditing-checkpoint.png)

---

### 2. Delegated Administration Baseline Confirmed

The delegated administration baseline was reviewed.

`john.smith.admin` remained a member of:

```text
GG_IT_HelpDesk_Admins
```

The `_MRTG/Users` OU structure was also confirmed.

![Delegated admin baseline](screenshots/lab-08-02-delegated-admin-baseline.png)

This confirmed that the lab started from the intended delegated-access model.

---

### 3. Auditing GPO Linked to Domain Controllers OU

A dedicated GPO named `MRTG-DC-Identity-Auditing` was linked to the Domain Controllers OU.

![DC identity auditing GPO linked](screenshots/lab-08-03-dc-identity-auditing-gpo-linked.png)

A dedicated GPO was used instead of modifying the default policy so the auditing configuration would remain easier to manage, explain, and validate.

---

### 4. Audit Logon Configured

Audit Logon was enabled for both success and failure events.

![Audit logon configured](screenshots/lab-08-04-audit-logon-configured.png)

This provides visibility into authentication activity on the domain controller.

---

### 5. Audit User Account Management Configured

Audit User Account Management was enabled for both success and failure events.

![Audit user account management configured](screenshots/lab-08-05-audit-user-account-management-configured.png)

This supports monitoring of identity actions such as password resets and user account changes.

---

### 6. Audit Directory Service Changes Configured

Audit Directory Service Changes was enabled for success events.

![Audit directory service changes configured](screenshots/lab-08-06-audit-directory-service-changes-configured.png)

This supports tracking of Active Directory object changes when paired with object-level auditing.

---

### 7. Audit Security Group Management Configured

Audit Security Group Management was enabled for both success and failure events.

![Audit security group management configured](screenshots/lab-08-07-audit-security-group-management-configured.png)

This improves visibility into changes affecting access-control groups.

---

### 8. Audit Policy Applied and Verified

The updated policy was applied and validated.

Commands used:

```cmd
gpupdate /force
auditpol /get /category:*
```

The output confirmed that the required audit subcategories were enabled.

Validated audit areas included:

- Logon
- Security Group Management
- User Account Management
- Directory Service Changes

![GPUpdate and auditpol verification](screenshots/lab-08-08-gpupdate-and-auditpol-verification.png)

---

### 9. Users OU Auditing Configuration Opened

Advanced Features were enabled in Active Directory Users and Computers.

The security settings for `_MRTG/Users` were opened and the Auditing tab was reviewed.

![Users OU auditing tab](screenshots/lab-08-09-users-ou-auditing-tab.png)

This prepared the OU for object-level auditing.

---

### 10. Auditing Entry Added for Descendant User Objects

An auditing entry was created for descendant user objects under `_MRTG/Users`.

Configuration used:

| Setting | Value |
|---|---|
| Principal | `Everyone` |
| Type | `Success` |
| Applies To | `Descendant User objects` |

![Users OU auditing entry](screenshots/lab-08-10-users-ou-auditing-entry.png)

This step was critical because directory service change auditing requires both audit policy and an object-level auditing entry.

---

### 11. Controlled Password Reset Performed

A controlled password reset was performed for Kevin Carter.

The option selected was:

```text
User must change password at next logon
```

![Password reset action](screenshots/lab-08-11-password-reset-action.png)

This created a controlled identity event for user account management auditing.

---

### 12. Controlled User Attribute Change Performed

Kevin Carter’s Description field was updated with a lab validation value.

Description value used:

```text
Lab 08 audit validation change
```

![User attribute change](screenshots/lab-08-12-user-attribute-change.png)

This created a controlled directory object modification for directory service change auditing.

---

### 13. Event ID 4724 Password Reset Validated

Event Viewer was used on `MRTG-DC01` to filter the Security log for Event ID `4724`.

The event showed:

```text
An attempt was made to reset an account's password.
```

![Event 4724 password reset](screenshots/lab-08-13-event-4724-password-reset.png)

This confirmed that password reset activity was being captured by the configured audit policy.

---

### 14. Event ID 4738 User Account Change Validated

Event Viewer was used to filter the Security log for Event ID `4738`.

The event showed:

```text
A user account was changed.
```

![Event 4738 user account changed](screenshots/lab-08-14-event-4738-user-account-changed.png)

This confirmed that user account modification activity was being captured.

---

### 15. Event ID 5136 Directory Object Modification Validated

Event Viewer was used to filter the Security log for Event ID `5136`.

The event showed:

```text
A directory service object was modified.
```

![Event 5136 directory object modified](screenshots/lab-08-15-event-5136-directory-object-modified.png)

This confirmed that object-level auditing on `_MRTG/Users` was working as intended.

---

### 16. Event ID 4719 Audit Policy Change Validated

Event Viewer was used to filter the Security log for Event ID `4719`.

The event showed:

```text
System audit policy was changed.
```

![Event 4719 audit policy changed](screenshots/lab-08-16-event-4719-audit-policy-changed.png)

This confirmed that audit policy changes were also being logged.

---

## Security Perspective

This lab demonstrates that delegated access must be monitored.

Granting scoped administrative rights is only part of the control. The organization also needs visibility into what delegated administrators do.

From a security perspective, this lab supports:

- Identity monitoring
- Administrative accountability
- Delegated action visibility
- Password reset tracking
- User account change tracking
- Directory service change monitoring
- Audit policy change visibility
- Regulated environment audit readiness

---

## Risk Addressed

Without identity monitoring and auditing, administrative actions can occur without enough visibility.

This lab reduces the risk of:

- Untracked password resets
- Unreviewed user account changes
- Invisible delegated admin activity
- Weak accountability for identity changes
- Poor incident investigation capability
- Missing evidence for audit reviews
- Unvalidated audit policy configuration
- Unmonitored audit policy changes

---

## Control Mapping

| Control Area | How This Lab Supports It |
|---|---|
| Identity monitoring | Enables auditing for identity-related activity |
| Delegated administration review | Prepares monitoring for delegated admin actions |
| Password reset accountability | Validates Event ID `4724` |
| User change tracking | Validates Event ID `4738` |
| Directory object auditing | Validates Event ID `5136` |
| Audit policy monitoring | Validates Event ID `4719` |
| Audit readiness | Captures configuration and validation evidence |
| Least privilege support | Adds visibility around scoped delegated administrative activity |

---

## Validation

The following validation checks were completed:

| Validation Item | Result |
|---|---|
| Pre-lab checkpoint created | Passed |
| Delegated admin baseline reviewed | Passed |
| `MRTG-DC-Identity-Auditing` GPO linked to Domain Controllers OU | Passed |
| Audit Logon configured | Passed |
| Audit User Account Management configured | Passed |
| Audit Directory Service Changes configured | Passed |
| Audit Security Group Management configured | Passed |
| `gpupdate /force` completed | Passed |
| `auditpol` confirmed audit settings | Passed |
| `_MRTG/Users` auditing configuration opened | Passed |
| Object-level auditing entry created | Passed |
| Kevin Carter password reset performed | Passed |
| Kevin Carter Description field updated | Passed |
| Event ID `4724` validated | Passed |
| Event ID `4738` validated | Passed |
| Event ID `5136` validated | Passed |
| Event ID `4719` validated | Passed |

---

## Evidence Collected

The following evidence was collected during the lab:

| Evidence | File |
|---|---|
| Pre-auditing checkpoint | `screenshots/lab-08-01-pre-auditing-checkpoint.png` |
| Delegated admin baseline | `screenshots/lab-08-02-delegated-admin-baseline.png` |
| DC identity auditing GPO linked | `screenshots/lab-08-03-dc-identity-auditing-gpo-linked.png` |
| Audit Logon configured | `screenshots/lab-08-04-audit-logon-configured.png` |
| Audit User Account Management configured | `screenshots/lab-08-05-audit-user-account-management-configured.png` |
| Audit Directory Service Changes configured | `screenshots/lab-08-06-audit-directory-service-changes-configured.png` |
| Audit Security Group Management configured | `screenshots/lab-08-07-audit-security-group-management-configured.png` |
| GPUpdate and auditpol verification | `screenshots/lab-08-08-gpupdate-and-auditpol-verification.png` |
| Users OU auditing tab | `screenshots/lab-08-09-users-ou-auditing-tab.png` |
| Users OU auditing entry | `screenshots/lab-08-10-users-ou-auditing-entry.png` |
| Password reset action | `screenshots/lab-08-11-password-reset-action.png` |
| User attribute change | `screenshots/lab-08-12-user-attribute-change.png` |
| Event 4724 password reset | `screenshots/lab-08-13-event-4724-password-reset.png` |
| Event 4738 user account changed | `screenshots/lab-08-14-event-4738-user-account-changed.png` |
| Event 5136 directory object modified | `screenshots/lab-08-15-event-5136-directory-object-modified.png` |
| Event 4719 audit policy changed | `screenshots/lab-08-16-event-4719-audit-policy-changed.png` |

---

## What I Would Improve in Production

In a production environment, I would improve this process by:

- Forwarding security logs to a centralized SIEM
- Defining audit log retention requirements
- Creating alerts for password resets
- Creating alerts for privileged group membership changes
- Monitoring delegated admin activity
- Separating audit policy administration from routine support activity
- Restricting who can modify audit policy
- Reviewing object-level auditing scope regularly
- Using least-privilege admin workstations
- Documenting audit policy ownership
- Mapping audit events to incident response playbooks
- Testing event generation with controlled identity actions
- Reviewing audit noise before expanding object-level auditing broadly

---

## Lessons Learned

This lab reinforced that delegation and auditing must work together.

Delegating password reset permissions helps reduce overuse of Domain Admin rights, but those actions still need to be visible.

The biggest takeaway is that audit policy alone is not always enough. For directory object changes, object-level auditing must also be configured on the target OU or object.

Identity monitoring becomes much stronger when policy configuration, scoped permissions, controlled identity actions, and event validation are all documented together.

---

## Outcome

Lab 08 successfully implemented identity monitoring and auditing controls in the MRTG Active Directory environment.

The lab confirmed:

- A pre-lab checkpoint was created
- The delegated administration baseline was reviewed
- A dedicated auditing GPO was linked to the Domain Controllers OU
- Advanced audit policy was configured for identity activity
- Audit policy was applied and validated with `auditpol`
- Object-level auditing was configured on `_MRTG/Users`
- A controlled password reset was performed
- A controlled user attribute change was performed
- Event ID `4724` confirmed password reset auditing
- Event ID `4738` confirmed user account change auditing
- Event ID `5136` confirmed directory service object modification auditing
- Event ID `4719` confirmed audit policy change auditing

The environment now has a stronger auditing foundation for delegated identity operations and future SIEM integration.

---

## Next Lab

[Lab 09 — Password Policy and Account Lockout Hardening](../Lab-09-Password-Policy-and-Account-Lockout-Hardening/)

Lab 09 will build on the auditing foundation by implementing password policy and account lockout controls to strengthen authentication security across the MRTG domain.
