# Lab 10: Fine-Grained Password Policies for Tiered Identity Control

![Platform](https://img.shields.io/badge/Platform-Windows%20Server%202022-blue)
![Technology](https://img.shields.io/badge/Technology-Active%20Directory-blue)
![Tooling](https://img.shields.io/badge/Tooling-ADAC%20%26%20ADUC-purple)
![Focus](https://img.shields.io/badge/Focus-Tiered%20Identity%20Control-orange)
![Security](https://img.shields.io/badge/Security-Fine--Grained%20Password%20Policy-red)
![Validation](https://img.shields.io/badge/Validation-Resultant%20PSO-brightgreen)

---

## Objective

Implement Fine-Grained Password Policies in the `mrtg.local` Active Directory domain.

This lab builds on the default domain authentication policy from Lab 09 by applying different password and lockout requirements to standard users, privileged administrators, and service accounts.

The goal is to use Password Settings Objects and group-based targeting to apply controls according to identity risk.

---

## Business Scenario

Monroe Redstone Technology Group requires different authentication controls for different account categories.

The default domain policy provides a baseline, but standard users, privileged administrators, and service accounts have different security and operational requirements.

This lab addresses the need to:

- Apply different password controls within one domain
- Strengthen requirements for privileged identities
- Separate service account requirements from workforce-user requirements
- Assign policies through security groups
- Validate the resultant policy for representative identities
- Document precedence and policy-assignment behavior

---

## Lab Summary

In this lab, I created three global security groups for Fine-Grained Password Policy targeting:

- `GG_PSO_Standard_Users`
- `GG_PSO_Privileged_Admins`
- `GG_PSO_Service_Accounts`

I then created three Password Settings Objects in Active Directory Administrative Center:

- `PSO-Standard-Users`
- `PSO-Privileged-Admins`
- `PSO-Service-Accounts`

Each PSO was associated with the appropriate global security group. Representative standard, privileged, and service identities were then reviewed to confirm that the intended resultant policy applied.

This extended the MRTG environment from one default domain policy to risk-based password and lockout controls.

---

## Environment

| Component | Details |
|---|---|
| Domain | `mrtg.local` |
| Domain Controller | `MRTG-DC01` |
| Directory Service | Active Directory Domain Services |
| Management Tools | Active Directory Users and Computers and Active Directory Administrative Center |
| FGPP Location | Password Settings Container |
| Standard User | `kevin.carter` |
| Privileged Administrator | `john.smith.admin` |
| Service Account Display Name | `Service App Deploy` |
| Virtualization Platform | Hyper-V |
| Organization | Monroe Redstone Technology Group |

---

## Prerequisites

- Operational `mrtg.local` Active Directory domain
- Domain functional level that supports Fine-Grained Password Policies
- Default domain password policy configured
- Existing standard, privileged, and service identities
- Administrative access to ADUC and ADAC
- Global security groups for PSO targeting
- Documented password and lockout requirements for each account category

---

## Scope

### Included

- Pre-change Hyper-V checkpoint
- PSO targeting-group creation
- Representative identity assignment
- Password Settings Container review
- Standard-user PSO creation
- Privileged-administrator PSO creation
- Service-account PSO creation
- PSO precedence configuration
- Password Settings Object review
- Resultant policy validation
- Policy comparison across account categories

### Not Included

- Password-reset behavior testing
- Password-change behavior testing
- Multiple-PSO conflict testing
- Direct user-level PSO assignment
- Authentication policy silos
- Microsoft Entra Password Protection
- Hybrid password writeback
- Multifactor authentication
- Conditional Access
- Privileged Identity Management
- Group Managed Service Account deployment

---

## FGPP Architecture

Fine-Grained Password Policies are stored as Password Settings Objects in the Password Settings Container.

```text
mrtg.local
`-- System
    `-- Password Settings Container
        |-- PSO-Service-Accounts
        |-- PSO-Privileged-Admins
        `-- PSO-Standard-Users
```

Group-based targeting was used.

```text
GG_PSO_Standard_Users
`-- kevin.carter

GG_PSO_Privileged_Admins
`-- john.smith.admin

GG_PSO_Service_Accounts
`-- Service App Deploy
```

This design supports:

- Risk-based authentication controls
- Group-based policy assignment
- Stronger privileged-account requirements
- Separate service-account requirements
- Centralized policy administration
- Repeatable policy review

---

## Fine-Grained Password Policy Model

Fine-Grained Password Policies are not normal OU-linked Group Policy settings.

They use Password Settings Objects stored in Active Directory.

| Component | Purpose |
|---|---|
| Password Settings Object | Defines password and account-lockout requirements |
| Precedence | Determines which applicable PSO wins when more than one applies |
| Global Security Group | Provides scalable PSO targeting |
| Resultant Password Policy | Identifies the effective PSO for a user |

A PSO can apply directly to a user or to a global security group. This lab uses global security groups.

Lower numeric precedence values have higher priority when multiple PSOs apply.

---

## Account Categories

| Account Category | Representative Identity | Targeting Group | Password Settings Object |
|---|---|---|---|
| Standard User | `kevin.carter` | `GG_PSO_Standard_Users` | `PSO-Standard-Users` |
| Privileged Administrator | `john.smith.admin` | `GG_PSO_Privileged_Admins` | `PSO-Privileged-Admins` |
| Service Account | `Service App Deploy` | `GG_PSO_Service_Accounts` | `PSO-Service-Accounts` |

These categories provide risk-based password controls. They do not, by themselves, establish a complete enterprise administrative tiering model.

---

## Password Settings Object Design

| PSO | Precedence | Minimum Length | History | Maximum Age | Lockout Threshold |
|---|---:|---:|---:|---|---:|
| `PSO-Service-Accounts` | `10` | `20` | `5` | `365 days` | `5` |
| `PSO-Privileged-Admins` | `20` | `14` | `10` | `60 days` | `3` |
| `PSO-Standard-Users` | `30` | `12` | `5` | `90 days` | `5` |

The values above represent this lab's design. Longer password age for a service account is not a substitute for automated credential rotation or a Group Managed Service Account.

---

## Implementation and Validation

### 1. Created a Pre-Change Lab Checkpoint

A Hyper-V checkpoint was created before the FGPP configuration changes.

![Pre-FGPP checkpoint](screenshots/lab-10-01-pre-fgpp-checkpoint.png)

The checkpoint served as a temporary lab recovery point and was not treated as a supported Active Directory backup.

---

### 2. Created the PSO Targeting Groups

The following global security groups were created in `_MRTG\Groups`:

- `GG_PSO_Standard_Users`
- `GG_PSO_Privileged_Admins`
- `GG_PSO_Service_Accounts`

![PSO groups created](screenshots/lab-10-02-pso-groups-created.png)

Global security groups were used because PSOs can be assigned to users and global security groups.

---

### 3. Assigned Representative Identities

Representative identities were added to the PSO targeting groups.

| Identity | Group |
|---|---|
| `kevin.carter` | `GG_PSO_Standard_Users` |
| `john.smith.admin` | `GG_PSO_Privileged_Admins` |
| `Service App Deploy` | `GG_PSO_Service_Accounts` |

![PSO group membership](screenshots/lab-10-03-pso-group-membership.png)

This connected each identity category to its intended Password Settings Object.

---

### 4. Opened the Password Settings Container

Active Directory Administrative Center was used to open:

```text
mrtg.local
`-- System
    `-- Password Settings Container
```

![Password Settings Container opened](screenshots/lab-10-04-password-settings-container-opened.png)

This container stores the domain's Password Settings Objects.

---

### 5. Created the Standard Users PSO

`PSO-Standard-Users` was created and associated with:

```text
GG_PSO_Standard_Users
```

| Setting | Value |
|---|---|
| Precedence | `30` |
| Minimum password length | `12` |
| Password history | `5` |
| Maximum password age | `90 days` |
| Minimum password age | `1 day` |
| Complexity | `Enabled` |
| Reversible encryption | `Disabled` |
| Lockout threshold | `5` |
| Reset lockout counter after | `15 minutes` |
| Lockout duration | `15 minutes` |

![Standard users PSO configured](screenshots/lab-10-05-standard-users-pso-configured.png)

This PSO represents the standard workforce-user authentication baseline.

---

### 6. Created the Privileged Administrators PSO

`PSO-Privileged-Admins` was created and associated with:

```text
GG_PSO_Privileged_Admins
```

| Setting | Value |
|---|---|
| Precedence | `20` |
| Minimum password length | `14` |
| Password history | `10` |
| Maximum password age | `60 days` |
| Minimum password age | `1 day` |
| Complexity | `Enabled` |
| Reversible encryption | `Disabled` |
| Lockout threshold | `3` |
| Reset lockout counter after | `30 minutes` |
| Lockout duration | `30 minutes` |

![Privileged administrators PSO configured](screenshots/lab-10-06-privileged-admins-pso-configured.png)

This PSO applies different requirements to higher-risk administrative identities.

A low privileged-account lockout threshold can increase denial-of-service risk, so production values require careful evaluation.

---

### 7. Created the Service Accounts PSO

`PSO-Service-Accounts` was created and associated with:

```text
GG_PSO_Service_Accounts
```

| Setting | Value |
|---|---|
| Precedence | `10` |
| Minimum password length | `20` |
| Password history | `5` |
| Maximum password age | `365 days` |
| Minimum password age | `1 day` |
| Complexity | `Enabled` |
| Reversible encryption | `Disabled` |
| Lockout threshold | `5` |
| Reset lockout counter after | `15 minutes` |
| Lockout duration | `15 minutes` |

![Service accounts PSO configured](screenshots/lab-10-07-service-accounts-pso-configured.png)

This separated the service-account password model from the workforce-user baseline.

In production, supported services should use gMSAs or another managed credential solution instead of long-lived manually managed passwords.

---

### 8. Confirmed the PSO Objects

The Password Settings Container was reviewed after creating the policies.

| Password Settings Object | Precedence |
|---|---:|
| `PSO-Service-Accounts` | `10` |
| `PSO-Privileged-Admins` | `20` |
| `PSO-Standard-Users` | `30` |

![PSO objects created](screenshots/lab-10-08-pso-objects-created.png)

This confirmed that all three Password Settings Objects existed.

---

### 9. Validated Kevin Carter's Resultant Policy

The `kevin.carter` account was reviewed in Active Directory Administrative Center.

The resultant password settings showed:

```text
PSO-Standard-Users
```

Associated precedence:

```text
30
```

![Resultant PSO for Kevin Carter](screenshots/lab-10-09-resultant-pso-kevin-carter.png)

This confirmed that the standard-user PSO applied through group membership.

---

### 10. Validated the Administrative Account Resultant Policy

The `john.smith.admin` account was reviewed in Active Directory Administrative Center.

The resultant password settings showed:

```text
PSO-Privileged-Admins
```

Associated precedence:

```text
20
```

![Resultant PSO for John Smith Admin](screenshots/lab-10-10-resultant-pso-john-smith-admin.png)

This confirmed that the privileged-administrator PSO was effective for the test administrator.

---

### 11. Validated the Service Account Resultant Policy

The `Service App Deploy` account was reviewed in Active Directory Administrative Center.

The resultant password settings showed:

```text
PSO-Service-Accounts
```

Associated precedence:

```text
10
```

![Resultant PSO for service account](screenshots/lab-10-11-resultant-pso-service-account.png)

This confirmed that the service-account PSO applied through the targeting group.

---

### 12. Compared the Policy Design

The Password Settings Container was reviewed with all three policies visible.

![Password policy tier validation](screenshots/lab-10-12-ad-user-policy-tier-validation.png)

The final policy structure was:

- Service Accounts with precedence `10`
- Privileged Administrators with precedence `20`
- Standard Users with precedence `30`

Precedence determines the winner only when an identity is affected by more than one PSO.

---

## Security and IAM Relevance

Different identity categories can require different authentication controls.

This lab supports:

- Risk-based password policy
- Stronger privileged-account requirements
- Separate service-account controls
- Group-based policy assignment
- Centralized password governance
- Resultant policy validation
- Reduced direct user-level policy assignment
- Improved non-human identity awareness

Fine-Grained Password Policies extend the default domain policy, but they do not replace multifactor authentication, privileged access controls, credential monitoring, or managed service accounts.

---

## Risks Addressed

This lab reduces the risk of:

- Applying one policy to every account regardless of risk
- Privileged accounts receiving only the standard baseline
- Service accounts receiving unsuitable workforce-user settings
- Direct per-user PSO assignment
- Unclear policy precedence
- Unvalidated effective password settings
- Poor visibility into policy targeting groups
- Weak documentation of differentiated authentication controls

---

## Control Mapping

| Control Area | Lab Contribution |
|---|---|
| Risk-Based Authentication | Applies different controls by account category |
| Privileged Account Protection | Assigns a separate PSO to named administrators |
| Service Account Governance | Assigns a separate PSO to non-human identities |
| Group-Based Targeting | Uses global security groups for PSO assignment |
| Authentication Hardening | Extends the default domain policy from Lab 09 |
| Effective Policy Validation | Reviews the resultant PSO for each representative identity |
| Audit Readiness | Captures policy creation, assignment, and validation evidence |

---

## Validation Results

| Validation Item | Result |
|---|---|
| Temporary pre-change checkpoint created | Passed |
| PSO targeting groups created | Passed |
| Representative identities assigned to targeting groups | Passed |
| Password Settings Container opened | Passed |
| `PSO-Standard-Users` created | Passed |
| `PSO-Privileged-Admins` created | Passed |
| `PSO-Service-Accounts` created | Passed |
| PSO precedence values configured | Passed |
| All PSO objects visible | Passed |
| Kevin Carter's resultant PSO reviewed | Passed |
| `john.smith.admin` resultant PSO reviewed | Passed |
| `Service App Deploy` resultant PSO reviewed | Passed |
| Three-category policy design documented | Passed |
| Password-change behavior tested | Not tested |
| Multiple-PSO conflict tested | Not tested |

---

## Evidence Collected

| Evidence | File |
|---|---|
| Pre-FGPP checkpoint | `screenshots/lab-10-01-pre-fgpp-checkpoint.png` |
| PSO targeting groups | `screenshots/lab-10-02-pso-groups-created.png` |
| PSO group membership | `screenshots/lab-10-03-pso-group-membership.png` |
| Password Settings Container | `screenshots/lab-10-04-password-settings-container-opened.png` |
| Standard users PSO | `screenshots/lab-10-05-standard-users-pso-configured.png` |
| Privileged administrators PSO | `screenshots/lab-10-06-privileged-admins-pso-configured.png` |
| Service accounts PSO | `screenshots/lab-10-07-service-accounts-pso-configured.png` |
| Created PSO objects | `screenshots/lab-10-08-pso-objects-created.png` |
| Kevin Carter resultant PSO | `screenshots/lab-10-09-resultant-pso-kevin-carter.png` |
| John Smith Admin resultant PSO | `screenshots/lab-10-10-resultant-pso-john-smith-admin.png` |
| Service account resultant PSO | `screenshots/lab-10-11-resultant-pso-service-account.png` |
| Password policy comparison | `screenshots/lab-10-12-ad-user-policy-tier-validation.png` |

---

## What I Would Improve in Production

In a production environment, I would:

- Define account categories through formal policy and risk assessment
- Align password requirements with current organizational and regulatory guidance
- Avoid arbitrary periodic password changes unless required or compromise is suspected
- Use password screening for common and compromised passwords
- Validate resultant PSOs with `Get-ADUserResultantPasswordPolicy`
- Test identities affected by multiple PSOs
- Document precedence decisions
- Monitor changes to PSO targeting groups
- Review PSO assignments regularly
- Evaluate lockout thresholds against denial-of-service risk
- Protect privileged accounts with multifactor authentication where supported
- Use privileged access workstations and separate admin credentials
- Replace eligible service accounts with gMSAs
- Document service account ownership and dependencies
- Test password changes before enforcing new PSOs broadly
- Use supported backups rather than Hyper-V checkpoints

---

## Lessons Learned

This lab reinforced that Fine-Grained Password Policies are separate from normal GPO-based password policy.

FGPP uses Password Settings Objects and applies to users or global security groups rather than OUs.

The primary takeaway is that group-based targeting is easier to manage than direct user assignment, but it must be validated through the resultant password policy.

This lab also demonstrated that different settings do not automatically make a policy better. Password age, length, lockout, automation, and operational impact must be evaluated together.

---

## Outcome

Lab 10 successfully implemented Fine-Grained Password Policies in the MRTG Active Directory environment.

The lab confirmed that:

- Global PSO targeting groups were created
- Standard, privileged, and service identities were assigned to separate groups
- Three Password Settings Objects were created
- PSOs were associated through group-based targeting
- Precedence values were documented
- Representative identities received the intended resultant policy
- The environment supports differentiated password and lockout controls

The MRTG domain now extends its default authentication baseline with risk-based Fine-Grained Password Policies.

---

## Next Lab

[Lab 11: DHCP Services for Enterprise Identity Infrastructure](../Lab-11-DHCP-Services-for-Enterprise-Identity-Infrastructure/)

Lab 11 deploys DHCP services to provide controlled IP address configuration for domain-joined systems in the MRTG environment.
