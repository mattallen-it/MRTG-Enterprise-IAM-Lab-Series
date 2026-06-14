# Lab 15 — Group Policy Security Baselines for Workstations and Servers

![Platform](https://img.shields.io/badge/Platform-Windows%20Server%202022-blue)
![Technology](https://img.shields.io/badge/Technology-Active%20Directory-blue)
![Tooling](https://img.shields.io/badge/Tooling-GPMC%20%26%20gpresult-purple)
![Focus](https://img.shields.io/badge/Focus-Group%20Policy%20Security%20Baselines-orange)
![Security](https://img.shields.io/badge/Security-Endpoint%20Hardening-red)
![Validation](https://img.shields.io/badge/Validation-gpupdate%20%26%20gpresult-brightgreen)

---

## Objective

The objective of this lab is to create and validate role-based Group Policy security baselines for workstation and server assets in the `mrtg.local` Active Directory environment.

This lab uses Group Policy to centrally enforce baseline security controls through properly scoped Organizational Units.

The focus is on workstation and server policy separation, OU-based targeting, endpoint hardening, and Group Policy validation.

---

## Business Problem

Monroe Redstone Technology Group needs a consistent way to apply baseline security settings to domain-joined systems.

Without role-based Group Policy baselines, workstations and servers may rely on manual configuration. That can lead to inconsistent settings, configuration drift, weak auditing, and unnecessary endpoint exposure.

This lab addresses the need to:

- Separate workstation and server policy targeting
- Create dedicated endpoint OUs
- Apply security baselines through Group Policy
- Enforce sign-in privacy controls
- Enforce Windows Defender Firewall settings
- Configure audit policy settings
- Validate applied policy using native Windows tools
- Preserve final validated states with Hyper-V checkpoints

---

## Lab Summary

In this lab, I created dedicated `Servers` and `Workstations` OUs under the existing MRTG computer structure.

I created separate Group Policy Objects for workstation and server security baselines and linked each GPO to the correct role-based OU.

The workstation baseline was configured with endpoint-focused security controls, while the server baseline was configured with stronger auditing and firewall enforcement for server assets.

The available domain-joined member server, `MRTG-LOG01`, was moved into the `Servers` OU and used to validate Group Policy application with `gpupdate`, `gpresult`, and an HTML Group Policy report.

---

## Environment

| Component | Details |
|---|---|
| Domain | `mrtg.local` |
| Primary Domain Controller | `MRTG-DC01` |
| Validated Member Server | `MRTG-LOG01` |
| Parent OU | `_MRTG` |
| Computer OU | `_MRTG > Computers` |
| Server OU | `_MRTG > Computers > Servers` |
| Workstation OU | `_MRTG > Computers > Workstations` |
| Server Baseline GPO | `MRTG-GPO-Server-Security-Baseline` |
| Workstation Baseline GPO | `MRTG-GPO-Workstation-Security-Baseline` |
| Platform | Windows Server, Active Directory Domain Services, Group Policy |
| Lab Organization | Monroe Redstone Technology Group |

---

## Scope

### Included

- Existing OU structure review
- Dedicated `Servers` OU creation
- Dedicated `Workstations` OU creation
- Workstation security baseline GPO creation
- Server security baseline GPO creation
- GPO linking to role-based OUs
- Workstation logon privacy configuration
- Workstation firewall baseline configuration
- Workstation audit logon configuration
- Server logon privacy configuration
- Server firewall baseline configuration
- Server audit policy baseline configuration
- Computer object movement into role-based OU
- Group Policy refresh with `gpupdate`
- Applied policy validation with `gpresult`
- HTML Group Policy report generation
- Final Hyper-V checkpoints

### Not Included

- Microsoft Security Compliance Toolkit import
- CIS benchmark import
- Intune security baselines
- Microsoft Defender for Endpoint integration
- Advanced firewall rule design
- Local administrator password management
- Windows LAPS configuration
- Security configuration drift monitoring
- SIEM-based GPO change alerting

---

## Architecture

The MRTG environment uses dedicated computer OUs to separate workstation and server policy targeting.

```text
mrtg.local
└── _MRTG
    ├── Admin Accounts
    ├── Groups
    ├── Service Accounts
    ├── Users
    └── Computers
        ├── Servers
        │   ├── MRTG-LOG01
        │   └── MRTG-GPO-Server-Security-Baseline
        └── Workstations
            └── MRTG-GPO-Workstation-Security-Baseline
```

This structure supports:

- Role-based policy targeting
- Separate workstation and server controls
- Centralized endpoint hardening
- Reduced manual configuration drift
- Better validation and audit evidence
- Cleaner future baseline expansion

---

## Group Policy Baseline Model

This lab uses separate GPOs for workstation and server assets.

| GPO Name | Linked OU | Purpose |
|---|---|---|
| `MRTG-GPO-Workstation-Security-Baseline` | `_MRTG > Computers > Workstations` | Applies baseline security controls to workstation assets |
| `MRTG-GPO-Server-Security-Baseline` | `_MRTG > Computers > Servers` | Applies baseline security controls to server assets |

This model avoids applying the same baseline blindly to every system.

Workstations and servers have different security needs, so their baseline controls should be separated, scoped, and validated independently.

---

## Baseline Controls Configured

### Workstation Security Baseline

| Control Area | Setting | Value |
|---|---|---|
| Interactive Logon | Do not display last signed-in user | Enabled |
| Windows Defender Firewall | Domain Profile firewall state | On |
| Windows Defender Firewall | Inbound connections | Block |
| Windows Defender Firewall | Outbound connections | Allow |
| Audit Policy | Audit logon events | Success, Failure |

### Server Security Baseline

| Control Area | Setting | Value |
|---|---|---|
| Interactive Logon | Do not display last signed-in user | Enabled |
| Windows Defender Firewall | Domain Profile firewall state | On |
| Windows Defender Firewall | Inbound connections | Block |
| Windows Defender Firewall | Outbound connections | Allow |
| Audit Policy | Audit logon events | Success, Failure |
| Audit Policy | Audit account management | Success, Failure |
| Audit Policy | Audit policy change | Success, Failure |

---

## Implementation and Validation

### 1. Existing MRTG OU Structure Reviewed

The existing MRTG Active Directory OU structure was reviewed before creating dedicated endpoint OUs.

![Existing MRTG OU structure](screenshots/lab-15-01-aduc-ou-structure-before-gpo-baselines.png)

This confirmed the starting OU structure before role-based computer segmentation.

---

### 2. Servers and Workstations OUs Created

Dedicated `Servers` and `Workstations` OUs were created under the existing `_MRTG > Computers` OU.

![Servers and Workstations OUs created](screenshots/lab-15-02-workstations-and-servers-ous-created.png)

This created separate policy targets for workstation and server security baselines.

---

### 3. Endpoint OU Structure Reviewed in Group Policy Management

Group Policy Management was opened to confirm the endpoint OU structure before creating baseline GPOs.

![GPMC endpoint OU structure before baselines](screenshots/lab-15-03-gpmc-endpoint-ou-structure-before-baselines.png)

This confirmed that both endpoint OUs were visible and ready for GPO linking.

---

### 4. Workstation Security Baseline GPO Created and Linked

A new workstation baseline GPO was created and linked to the `Workstations` OU.

GPO created:

```text
MRTG-GPO-Workstation-Security-Baseline
```

![Workstation baseline GPO linked](screenshots/lab-15-04-workstation-security-baseline-gpo-linked.png)

This prepared workstation-specific baseline enforcement for future domain-joined workstation assets.

---

### 5. Workstation Logon Privacy Control Configured

The workstation baseline GPO was configured to hide the last signed-in user.

Policy path:

```text
Computer Configuration
└── Policies
    └── Windows Settings
        └── Security Settings
            └── Local Policies
                └── Security Options
```

Configured setting:

```text
Interactive logon: Don't display last signed-in = Enabled
```

![Workstation hide last signed-in user enabled](screenshots/lab-15-05-workstation-hide-last-signed-in-enabled.png)

This reduces username exposure on domain-joined workstations.

---

### 6. Workstation Domain Firewall Policy Configured

The workstation baseline GPO was configured to enforce Windows Defender Firewall on the Domain Profile.

Configured values:

```text
Firewall state: On
Inbound connections: Block
Outbound connections: Allow
```

![Workstation domain firewall enabled](screenshots/lab-15-06-workstation-domain-firewall-enabled.png)

This provides baseline firewall enforcement for workstation assets.

---

### 7. Workstation Audit Logon Events Configured

The workstation baseline GPO was configured to audit successful and failed logon events.

Configured setting:

```text
Audit logon events = Success, Failure
```

![Workstation audit logon events enabled](screenshots/lab-15-07-workstation-audit-logon-events-enabled.png)

This improves visibility into authentication activity on workstation assets.

---

### 8. Server Security Baseline GPO Created and Linked

A new server baseline GPO was created and linked to the `Servers` OU.

GPO created:

```text
MRTG-GPO-Server-Security-Baseline
```

![Server baseline GPO linked](screenshots/lab-15-08-server-security-baseline-gpo-linked.png)

This prepared server-specific baseline enforcement for domain-joined server assets.

---

### 9. Server Logon Privacy Control Configured

The server baseline GPO was configured to hide the last signed-in user.

Configured setting:

```text
Interactive logon: Don't display last signed-in = Enabled
```

![Server hide last signed-in user enabled](screenshots/lab-15-09-server-hide-last-signed-in-enabled.png)

This reduces username exposure on domain-joined servers.

---

### 10. Server Domain Firewall Policy Configured

The server baseline GPO was configured to enforce Windows Defender Firewall on the Domain Profile.

Configured values:

```text
Firewall state: On
Inbound connections: Block
Outbound connections: Allow
```

![Server domain firewall enabled](screenshots/lab-15-10-server-domain-firewall-enabled.png)

This provides baseline firewall enforcement for server assets.

---

### 11. Server Audit Policy Baseline Configured

The server baseline GPO was configured with stronger auditing than the workstation baseline.

Configured audit settings:

```text
Audit account management = Success, Failure
Audit logon events = Success, Failure
Audit policy change = Success, Failure
```

![Server audit policy baseline enabled](screenshots/lab-15-11-server-audit-policy-baseline-enabled.png)

This improves monitoring of privileged and security-relevant activity on server assets.

---

### 12. MRTG-LOG01 Moved into Servers OU

The `MRTG-LOG01` computer object was moved from the default Computers container into the role-based `Servers` OU.

Target OU:

```text
_MRTG
└── Computers
    └── Servers
```

![MRTG-LOG01 moved into Servers OU](screenshots/lab-15-12-log01-moved-to-servers-ou.png)

This allowed the server security baseline GPO to apply through OU-based targeting.

---

### 13. Group Policy Refreshed on MRTG-LOG01

Group Policy was manually refreshed on `MRTG-LOG01`.

Command used:

```powershell
gpupdate /force
```

![MRTG-LOG01 gpupdate success](screenshots/lab-15-13-log01-gpupdate-success.png)

This forced the server to process the newly linked server security baseline GPO.

---

### 14. Applied GPO Verified with gpresult

Applied computer policies were reviewed using `gpresult`.

Command used:

```powershell
gpresult /r /scope computer
```

Confirmed applied GPO:

```text
MRTG-GPO-Server-Security-Baseline
```

![MRTG-LOG01 gpresult server baseline applied](screenshots/lab-15-14-log01-gpresult-server-baseline-applied.png)

This confirmed that `MRTG-LOG01` successfully applied the server security baseline GPO.

---

### 15. HTML Group Policy Report Generated

An HTML Group Policy report was generated for deeper validation evidence.

Commands used:

```powershell
mkdir C:\Temp
gpresult /h C:\Temp\mrtg-log01-gpresult.html
start C:\Temp\mrtg-log01-gpresult.html
```

![MRTG-LOG01 gpresult HTML report](screenshots/lab-15-15-log01-gpresult-html-report.png)

This provided detailed evidence of applied Group Policy settings.

---

### 16. Final Role-Based GPO Structure Reviewed

The final GPMC structure was reviewed to confirm that both security baseline GPOs were linked to their correct role-based OUs.

![Final role-based GPO structure](screenshots/lab-15-16-final-role-based-security-baseline-structure.png)

This confirmed the final workstation and server baseline structure.

---

### 17. Post-Lab Checkpoint Created for MRTG-DC01

A Hyper-V checkpoint was created for `MRTG-DC01` after completing the Group Policy configuration.

![MRTG-DC01 post Lab 15 checkpoint](screenshots/lab-15-17-final-lab15-checkpoint-dc01-created.png)

This preserved the domain controller state after the baseline GPO configuration was completed.

---

### 18. Post-Lab Checkpoint Created for MRTG-LOG01

A Hyper-V checkpoint was created for `MRTG-LOG01` after validating the server baseline GPO.

![MRTG-LOG01 post Lab 15 checkpoint](screenshots/lab-15-18-final-lab15-checkpoint-log01-created.png)

This preserved the member server state after successful baseline policy validation.

---

## Validation Note

At the time of validation, `MRTG-LOG01` was the available domain-joined member server object in the lab environment.

The workstation security baseline GPO was created, linked, and configured for future domain-joined workstation assets.

Live policy validation was performed against the server baseline because `MRTG-LOG01` was the available role-based endpoint object.

This keeps the lab accurate: the workstation baseline exists and is ready for workstation assets, while the server baseline was fully validated against an actual server object.

---

## Security Perspective

Group Policy is a core control mechanism in Active Directory environments.

It allows administrators to centrally enforce security settings across domain-joined systems instead of relying on manual local configuration.

From a security and IAM perspective, this lab supports:

- Centralized security control enforcement
- Role-based policy targeting
- Separation between workstation and server security requirements
- Reduced username exposure at sign-in
- Firewall enforcement for domain-connected systems
- Auditing of logon, account management, and policy change activity
- Validation of applied policy using native Windows tools
- Compliance-oriented administration

---

## Risk Addressed

Without role-based security baselines, endpoint configuration can become inconsistent and difficult to validate.

This lab reduces the risk of:

- Manual local security configuration
- Workstations and servers receiving the wrong policy set
- Weak or inconsistent firewall enforcement
- Username exposure at interactive sign-in
- Limited endpoint audit visibility
- Configuration drift across domain-joined systems
- Poor documentation of applied endpoint controls
- Unclear validation of Group Policy enforcement

---

## Control Mapping

| Control Area | How This Lab Supports It |
|---|---|
| Endpoint hardening | Applies workstation and server security baselines through Group Policy |
| Policy enforcement | Uses GPO links to apply settings centrally |
| OU-based targeting | Separates workstation and server policy scope |
| Least privilege administration | Avoids manual local configuration where centralized policy is appropriate |
| Audit readiness | Captures evidence of policy configuration and application |
| Identity security | Reduces exposure of signed-in usernames |
| Network security | Enforces Windows Defender Firewall baseline settings |
| Monitoring readiness | Enables audit settings for logon, account management, and policy change activity |
| Operational consistency | Creates repeatable baseline configuration for future endpoints |

---

## Validation

The following validation checks were completed:

| Validation Item | Result |
|---|---|
| Existing MRTG OU structure reviewed | Passed |
| `Servers` OU created | Passed |
| `Workstations` OU created | Passed |
| Workstation security baseline GPO created | Passed |
| Workstation security baseline linked to `Workstations` OU | Passed |
| Workstation logon privacy policy configured | Passed |
| Workstation firewall baseline configured | Passed |
| Workstation audit logon policy configured | Passed |
| Server security baseline GPO created | Passed |
| Server security baseline linked to `Servers` OU | Passed |
| Server logon privacy policy configured | Passed |
| Server firewall baseline configured | Passed |
| Server audit policy baseline configured | Passed |
| `MRTG-LOG01` moved into `Servers` OU | Passed |
| `gpupdate /force` completed on `MRTG-LOG01` | Passed |
| `gpresult` confirmed server baseline applied | Passed |
| HTML Group Policy report generated | Passed |
| Final GPO structure reviewed | Passed |
| Final Hyper-V checkpoints created | Passed |

---

## Evidence Collected

The following evidence was collected during the lab:

| Evidence | File |
|---|---|
| Existing MRTG OU structure | `screenshots/lab-15-01-aduc-ou-structure-before-gpo-baselines.png` |
| Servers and Workstations OUs created | `screenshots/lab-15-02-workstations-and-servers-ous-created.png` |
| GPMC endpoint OU structure before baselines | `screenshots/lab-15-03-gpmc-endpoint-ou-structure-before-baselines.png` |
| Workstation baseline GPO linked | `screenshots/lab-15-04-workstation-security-baseline-gpo-linked.png` |
| Workstation hide last signed-in user enabled | `screenshots/lab-15-05-workstation-hide-last-signed-in-enabled.png` |
| Workstation domain firewall enabled | `screenshots/lab-15-06-workstation-domain-firewall-enabled.png` |
| Workstation audit logon events enabled | `screenshots/lab-15-07-workstation-audit-logon-events-enabled.png` |
| Server baseline GPO linked | `screenshots/lab-15-08-server-security-baseline-gpo-linked.png` |
| Server hide last signed-in user enabled | `screenshots/lab-15-09-server-hide-last-signed-in-enabled.png` |
| Server domain firewall enabled | `screenshots/lab-15-10-server-domain-firewall-enabled.png` |
| Server audit policy baseline enabled | `screenshots/lab-15-11-server-audit-policy-baseline-enabled.png` |
| MRTG-LOG01 moved into Servers OU | `screenshots/lab-15-12-log01-moved-to-servers-ou.png` |
| MRTG-LOG01 gpupdate success | `screenshots/lab-15-13-log01-gpupdate-success.png` |
| MRTG-LOG01 gpresult server baseline applied | `screenshots/lab-15-14-log01-gpresult-server-baseline-applied.png` |
| MRTG-LOG01 gpresult HTML report | `screenshots/lab-15-15-log01-gpresult-html-report.png` |
| Final role-based security baseline structure | `screenshots/lab-15-16-final-role-based-security-baseline-structure.png` |
| MRTG-DC01 final Lab 15 checkpoint created | `screenshots/lab-15-17-final-lab15-checkpoint-dc01-created.png` |
| MRTG-LOG01 final Lab 15 checkpoint created | `screenshots/lab-15-18-final-lab15-checkpoint-log01-created.png` |

---

## What I Would Improve in Production

In a production environment, I would improve this process by:

- Starting from an approved baseline such as Microsoft Security Baselines or CIS Benchmarks
- Testing baseline GPOs in a pilot OU before broad deployment
- Using change control before modifying production Group Policy
- Documenting every configured setting and the business reason for it
- Using separate test, pilot, and production GPO rollout phases
- Reviewing exceptions for servers that require inbound firewall rules
- Monitoring Group Policy changes through event forwarding or SIEM alerts
- Using Group Policy backup before major changes
- Defining GPO ownership and review frequency
- Validating endpoint compliance on a recurring schedule

---

## Lessons Learned

This lab reinforced that Group Policy baselines should be role-based, not one-size-fits-all.

Workstations and servers have different security needs, so separating their baselines makes the environment easier to manage, troubleshoot, and expand.

The most important lesson is that configuration is not enough. Security baselines need validation. `gpupdate`, `gpresult`, and HTML reports provide evidence that policy was actually applied.

This lab also reinforced the value of clean OU design. Group Policy becomes much easier to manage when computer objects are placed into the correct organizational containers.

---

## Outcome

Lab 15 successfully implemented role-based Group Policy security baselines for workstation and server assets in the MRTG Active Directory environment.

The lab confirmed:

- Dedicated endpoint OUs were created
- Workstation and server baselines were separated
- GPOs were linked to the correct OUs
- Security settings were configured through centralized policy
- `MRTG-LOG01` was moved into the correct server OU
- Group Policy was refreshed successfully
- Applied server policy was validated with `gpresult`
- HTML reporting was generated for evidence
- Final checkpoints were created after validation

The environment now has a stronger foundation for centralized endpoint hardening and future policy-based security control enforcement.

---

## Next Lab

[Lab 16 — Delegation of Control and Tiered Administrative Boundaries](../Lab-16-Delegation-of-Control-and-Tiered-Administrative-Boundaries/)

Lab 16 will build on this role-based Group Policy foundation by focusing on delegated administration, least privilege access, and tiered administrative boundaries within the MRTG Active Directory environment.
