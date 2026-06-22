# Lab 09: Password Policy and Account Lockout Hardening

![Platform](https://img.shields.io/badge/Platform-Windows%20Server%202022-blue)
![Technology](https://img.shields.io/badge/Technology-Active%20Directory-blue)
![Tooling](https://img.shields.io/badge/Tooling-GPMC%20%26%20PowerShell-purple)
![Focus](https://img.shields.io/badge/Focus-Authentication%20Hardening-orange)
![Security](https://img.shields.io/badge/Security-Password%20%26%20Lockout%20Policy-red)
![Validation](https://img.shields.io/badge/Validation-Account%20Lockout-brightgreen)

---

## Objective

Harden domain authentication in the `mrtg.local` Active Directory domain by configuring password and account-lockout policy.

This lab applies baseline authentication controls at the domain level, validates the effective settings with PowerShell, and tests account lockout from both the user and administrator perspectives.

---

## Business Scenario

Monroe Redstone Technology Group requires centralized authentication controls to reduce weak password practices and limit repeated failed sign-in attempts against domain accounts.

Without an enforced domain policy, users may select weak passwords, reuse previous passwords, or experience inconsistent lockout behavior.

This lab addresses the need to:

- Configure centralized password requirements
- Configure account-lockout controls
- Apply account policy at the correct domain scope
- Validate the effective domain policy
- Trigger an account lockout from a domain-joined workstation
- Confirm the locked state from an administrative session
- Document authentication-control behavior

---

## Lab Summary

In this lab, I configured password and account-lockout settings in the Default Domain Policy.

The updated policy was applied, and the effective values were validated with `Get-ADDefaultDomainPasswordPolicy`.

Kevin Carter's standard domain account was then used to generate repeated failed sign-in attempts from `MRTG-CLIENT-01`. The account reached the configured lockout threshold, and the locked state was confirmed from `MRTG-DC01` with `Search-ADAccount -LockedOut`.

This lab demonstrated that authentication controls must be configured, applied, and tested through actual account behavior.

---

## Environment

| Component | Details |
|---|---|
| Domain | `mrtg.local` |
| Domain Controller | `MRTG-DC01` |
| Client Workstation | `MRTG-CLIENT-01` |
| Directory Service | Active Directory Domain Services |
| Policy | Default Domain Policy |
| Policy Tool | Group Policy Management |
| Validation Tool | PowerShell with the Active Directory module |
| Test User | Kevin Carter |
| Test User UPN | `kevin.carter@mrtg.local` |
| Virtualization Platform | Hyper-V |
| Organization | Monroe Redstone Technology Group |

---

## Prerequisites

- Operational `mrtg.local` Active Directory domain
- `MRTG-DC01` functioning as the domain controller
- `MRTG-CLIENT-01` joined to the domain
- Standard test account for Kevin Carter
- Administrative access to Group Policy Management
- Active Directory PowerShell module
- Network connectivity between the client and domain controller

---

## Scope

### Included

- Domain Group Policy structure review
- Password policy configuration
- Account-lockout policy configuration
- Domain-root GPO link confirmation
- Policy update with `gpupdate /force`
- Effective policy validation with PowerShell
- Failed sign-in testing
- User-side lockout validation
- Administrator-side lockout verification

### Not Included

- Fine-grained password policies
- Manual account unlock testing
- Microsoft Entra ID authentication controls
- Multifactor authentication
- Self-service password reset
- Conditional Access
- Privileged Identity Management
- Centralized SIEM monitoring
- Advanced authentication-event correlation

---

## Policy Architecture

Classic Active Directory domain account policy is processed from GPOs linked at the domain level.

```text
mrtg.local
`-- Default Domain Policy
    `-- Computer Configuration
        `-- Policies
            `-- Windows Settings
                `-- Security Settings
                    `-- Account Policies
                        |-- Password Policy
                        `-- Account Lockout Policy
```

This differs from workstation or user-session policy because the settings govern domain account authentication rather than a specific workstation OU.

For a single-domain baseline, maintaining these settings in the Default Domain Policy provides a clear and predictable design.

---

## Authentication Hardening Model

| Control | Purpose |
|---|---|
| Password Policy | Defines password length, history, complexity, and age requirements |
| Account Lockout Policy | Temporarily locks an account after repeated invalid authentication attempts |
| Effective Policy Validation | Confirms the settings Active Directory is enforcing |
| User-Side Testing | Confirms the control affects an actual domain sign-in |
| Administrator Verification | Confirms the resulting account state in Active Directory |

Account lockout can slow repeated password guessing, but aggressive thresholds can also create denial-of-service and Help Desk impact. Production values must balance security, usability, and operational support.

---

## Password Policy Settings

| Setting | Value |
|---|---|
| Enforce password history | `5 passwords remembered` |
| Maximum password age | `90 days` |
| Minimum password age | `1 day` |
| Minimum password length | `12 characters` |
| Password must meet complexity requirements | `Enabled` |
| Store passwords using reversible encryption | `Disabled` |

These values represent the settings used in this lab. Production password policy should follow current organizational, regulatory, and threat-based requirements.

---

## Account Lockout Policy Settings

| Setting | Value |
|---|---|
| Account lockout threshold | `5 invalid sign-in attempts` |
| Account lockout duration | `15 minutes` |
| Reset account lockout counter after | `15 minutes` |

---

## Implementation and Validation

### 1. Reviewed the Domain GPO Structure

Group Policy Management was opened on `MRTG-DC01`.

The `mrtg.local` domain and its Group Policy Objects were reviewed.

![Domain GPO structure](screenshots/lab-09-01-domain-gpo-structure.png)

This confirmed that the Default Domain Policy was available for domain-level account-policy configuration.

---

### 2. Configured the Password Policy

The Default Domain Policy was edited at:

```text
Computer Configuration
`-- Policies
    `-- Windows Settings
        `-- Security Settings
            `-- Account Policies
                `-- Password Policy
```

![Password policy settings](screenshots/lab-09-02-password-policy-settings.png)

The settings established the domain's baseline password requirements.

---

### 3. Configured the Account Lockout Policy

Account-lockout settings were configured at:

```text
Computer Configuration
`-- Policies
    `-- Windows Settings
        `-- Security Settings
            `-- Account Policies
                `-- Account Lockout Policy
```

![Account lockout policy settings](screenshots/lab-09-03-lockout-policy-settings.png)

This defined how Active Directory would respond to repeated invalid authentication attempts.

---

### 4. Confirmed the Domain-Level Scope

The Default Domain Policy link was reviewed at the `mrtg.local` domain root.

![Domain root link confirmation](screenshots/lab-09-04-domain-root-link-confirmation.png)

This confirmed that the account policy was configured at the domain scope rather than on a workstation or user OU.

---

### 5. Applied the Updated Policy

The following command was run on `MRTG-DC01`:

```powershell
gpupdate /force
```

![Group Policy update success](screenshots/lab-09-05-gpupdate-success.png)

The command completed successfully.

---

### 6. Validated the Effective Domain Policy

The effective domain password and lockout policy was reviewed with:

```powershell
Get-ADDefaultDomainPasswordPolicy
```

![Password policy validation](screenshots/lab-09-06-password-policy-validation.png)

Validated values included:

- Complexity enabled
- Minimum password length of `12`
- Password history count of `5`
- Maximum password age of `90 days`
- Minimum password age of `1 day`
- Lockout threshold of `5`
- Lockout duration of `15 minutes`
- Lockout observation window of `15 minutes`
- Reversible encryption disabled

This confirmed that Active Directory was reporting the intended default domain policy values.

---

### 7. Prepared the Test User

Kevin Carter's account was reviewed in Active Directory Users and Computers before testing.

![Test user ready](screenshots/lab-09-07-test-user-ready.png)

This confirmed that the standard user account was available for the lockout test.

---

### 8. Triggered the Account Lockout

Repeated incorrect passwords were entered for Kevin Carter on `MRTG-CLIENT-01`.

Testing continued until the configured threshold was reached.

![Account lockout triggered](screenshots/lab-09-08-account-lockout-triggered.png)

The client displayed an account-lockout message, confirming enforcement from the user's perspective.

---

### 9. Verified the Locked Account

The locked state was reviewed from an administrative PowerShell session on `MRTG-DC01`.

Command used:

```powershell
Search-ADAccount -LockedOut
```

![Locked account verification](screenshots/lab-09-09-locked-account-verification.png)

Kevin Carter appeared in the results, confirming that Active Directory recorded the account as locked.

---

## Security and IAM Relevance

Password and account-lockout controls are foundational identity protections.

This lab supports:

- Centralized authentication governance
- Minimum password requirements
- Password-history enforcement
- Protection against reversible password storage
- Response to repeated invalid authentication attempts
- User-side enforcement validation
- Administrator-side account-state verification
- Evidence-based policy review

Password policy is only one layer of authentication security. Stronger environments also use multifactor authentication, compromised-password screening, monitoring, and risk-based access controls where supported.

---

## Risks Addressed

This lab reduces the risk of:

- Weak password selection
- Immediate password reuse
- Unrestricted failed sign-in attempts
- Incorrectly scoped domain policy
- Inconsistent authentication controls
- Unvalidated account-lockout behavior
- Authentication controls existing only in documentation
- Weak administrator visibility into locked accounts

---

## Control Mapping

| Control Area | Lab Contribution |
|---|---|
| Authentication Hardening | Configures password and account-lockout policy |
| Password Governance | Applies length, history, complexity, age, and storage requirements |
| Password-Guessing Resistance | Locks accounts after repeated invalid attempts |
| Domain Policy | Applies classic account policy at the domain root |
| Effective Policy Validation | Uses `Get-ADDefaultDomainPasswordPolicy` |
| User-Side Enforcement | Tests account lockout from `MRTG-CLIENT-01` |
| Administrator Verification | Uses `Search-ADAccount -LockedOut` |
| Audit Readiness | Captures configuration and validation evidence |

---

## Validation Results

| Validation Item | Result |
|---|---|
| Domain GPO structure reviewed | Passed |
| Default Domain Policy identified | Passed |
| Password policy configured | Passed |
| Account-lockout policy configured | Passed |
| Domain-root GPO link confirmed | Passed |
| Group Policy update completed | Passed |
| Effective domain policy validated with PowerShell | Passed |
| Kevin Carter prepared as the test account | Passed |
| Account lockout triggered from `MRTG-CLIENT-01` | Passed |
| Locked account confirmed from `MRTG-DC01` | Passed |

---

## Evidence Collected

| Evidence | File |
|---|---|
| Domain GPO structure | `screenshots/lab-09-01-domain-gpo-structure.png` |
| Password policy settings | `screenshots/lab-09-02-password-policy-settings.png` |
| Account-lockout policy settings | `screenshots/lab-09-03-lockout-policy-settings.png` |
| Domain-root link confirmation | `screenshots/lab-09-04-domain-root-link-confirmation.png` |
| Group Policy update | `screenshots/lab-09-05-gpupdate-success.png` |
| Effective password policy | `screenshots/lab-09-06-password-policy-validation.png` |
| Test user readiness | `screenshots/lab-09-07-test-user-ready.png` |
| Client-side account lockout | `screenshots/lab-09-08-account-lockout-triggered.png` |
| Administrator-side lockout verification | `screenshots/lab-09-09-locked-account-verification.png` |

---

## What I Would Improve in Production

In a production environment, I would:

- Align password policy with current organizational and regulatory requirements
- Favor longer passwords or passphrases over frequent arbitrary changes where policy permits
- Avoid forced periodic password changes unless required or a compromise is suspected
- Use password screening to block common and compromised passwords
- Evaluate the lockout threshold against denial-of-service risk
- Monitor failed authentication and lockout events
- Alert on abnormal lockout patterns
- Document Help Desk unlock and identity-verification procedures
- Use fine-grained password policies where separate requirements are justified
- Implement multifactor authentication where supported
- Integrate authentication events with a SIEM
- Define a policy owner and recurring review schedule
- Test authentication-policy changes before broad deployment
- Document exceptions and compensating controls

---

## Lessons Learned

This lab reinforced that classic Active Directory password and account-lockout policy must be applied at the correct domain scope.

The main technical lesson was that configuration in Group Policy is not sufficient proof. The effective values must be queried from Active Directory, and actual sign-in behavior must be tested.

The lockout test also demonstrated that authentication policy directly affects Help Desk workload and user availability. Security values must therefore be selected deliberately rather than copied without considering operational impact.

---

## Outcome

Lab 09 successfully implemented domain password and account-lockout hardening in the MRTG environment.

The lab confirmed that:

- Password policy was configured in the Default Domain Policy
- Account-lockout policy was configured in the Default Domain Policy
- The policy was linked at the domain root
- Group Policy updated successfully
- Effective values were validated with PowerShell
- Kevin Carter's account locked after repeated failed sign-in attempts
- The locked state was confirmed from the administrator side

The environment now enforces and validates centralized domain authentication controls.

---

## Next Lab

[Lab 10: Fine-Grained Password Policies for Tiered Identity Control](../Lab-10-Fine-Grained-Password-Policies-for-Tiered-Identity-Control/)

Lab 10 applies fine-grained password policies to identities that require controls different from the default domain policy.
