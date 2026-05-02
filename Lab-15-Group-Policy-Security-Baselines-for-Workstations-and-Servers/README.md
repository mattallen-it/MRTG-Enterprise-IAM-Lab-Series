# Lab-15 — Group Policy Security Baselines for Workstations and Servers

![Status](https://img.shields.io/badge/Status-Complete-brightgreen)
![Platform](https://img.shields.io/badge/Platform-Windows%20Server-blue)
![Technology](https://img.shields.io/badge/Technology-Active%20Directory-0A66C2)
![Tooling](https://img.shields.io/badge/Tooling-Group%20Policy-5C2D91)
![Focus](https://img.shields.io/badge/Focus-Endpoint%20Security%20Control-orange)
![Validation](https://img.shields.io/badge/Validation-gpupdate%20%7C%20gpresult-success)

---

## Objective

The objective of this lab was to create and validate **role-based Group Policy security baselines** for workstation and server assets in the MRTG Active Directory environment.

This lab focused on using Group Policy to centrally enforce baseline security controls through properly scoped Organizational Units.

---

## Lab Summary

In this lab, dedicated **Servers** and **Workstations** OUs were created under the existing MRTG computer structure. Separate Group Policy Objects were then created and linked to each OU.

The workstation baseline was configured with endpoint-focused security controls, while the server baseline was configured with stronger auditing and firewall enforcement for server assets.

The available domain-joined member server, `MRTG-LOG01`, was moved into the Servers OU and used to validate Group Policy application with `gpupdate`, `gpresult`, and an HTML Group Policy report.

---

## Environment

| Component | Value |
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
| Platform | Windows Server / Active Directory Domain Services / Group Policy |

---

## Lab Architecture

```text
mrtg.local
│
└── _MRTG
    │
    ├── Admin Accounts
    ├── Groups
    ├── Service Accounts
    ├── Users
    │
    └── Computers
        │
        ├── Servers
        │   ├── MRTG-LOG01
        │   └── MRTG-GPO-Server-Security-Baseline
        │
        └── Workstations
            └── MRTG-GPO-Workstation-Security-Baseline
```

---

## Security and IAM Relevance

Group Policy is a core control mechanism in Active Directory environments. It allows administrators to centrally enforce security settings across domain-joined systems instead of relying on manual local configuration.

From an IAM and security operations perspective, this lab demonstrates:

- Centralized security control enforcement
- Role-based policy targeting
- Separation between workstation and server security requirements
- Reduced username exposure at sign-in
- Firewall enforcement for domain-connected systems
- Auditing of logon, account management, and policy change activity
- Validation of applied policy using native Windows tools

This supports least privilege, identity monitoring, endpoint hardening, and compliance-oriented administration in enterprise and government-regulated environments.

---

## Group Policy Objects Created

| GPO Name | Linked OU | Purpose |
|---|---|---|
| `MRTG-GPO-Workstation-Security-Baseline` | `_MRTG > Computers > Workstations` | Applies baseline security controls to workstation assets |
| `MRTG-GPO-Server-Security-Baseline` | `_MRTG > Computers > Servers` | Applies baseline security controls to server assets |

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

## Steps Performed

### 1. Reviewed the Existing MRTG OU Structure

The existing MRTG Active Directory OU structure was reviewed before creating dedicated endpoint OUs.

![Existing MRTG OU structure](./screenshots/01-aduc-ou-structure-before-gpo-baselines.png)

**Figure 1:** Existing MRTG Active Directory OU structure before creating dedicated Servers and Workstations OUs for Group Policy security baselines.

---

### 2. Created Servers and Workstations OUs

Dedicated `Servers` and `Workstations` OUs were created under the existing `_MRTG > Computers` OU.

![Servers and Workstations OUs created](./screenshots/02-aduc-workstations-and-servers-ous-created.png)

**Figure 2:** Dedicated Servers and Workstations organizational units were created under the MRTG Computers OU to support separate Group Policy security baselines by endpoint role.

---

### 3. Reviewed Endpoint OU Structure in Group Policy Management

Group Policy Management was opened to confirm the endpoint OU structure before creating baseline GPOs.

![GPMC endpoint OU structure before baselines](./screenshots/03-gpmc-endpoint-ou-structure-before-baselines.png)

**Figure 3:** Group Policy Management Console showing the dedicated Servers and Workstations OUs before applying role-based security baseline GPOs.

---

### 4. Created and Linked the Workstation Security Baseline GPO

A new workstation baseline GPO was created and linked to the `Workstations` OU.

GPO created:

```text
MRTG-GPO-Workstation-Security-Baseline
```

![Workstation baseline GPO linked](./screenshots/04-gpmc-workstation-security-baseline-gpo-linked.png)

**Figure 4:** The MRTG workstation security baseline GPO was created and linked to the Workstations OU to apply endpoint security settings to domain-joined workstations.

---

### 5. Configured Workstation Logon Privacy Control

The workstation baseline GPO was configured to hide the last signed-in user.

Policy path:

```text
Computer Configuration
 > Policies
   > Windows Settings
     > Security Settings
       > Local Policies
         > Security Options
```

Configured setting:

```text
Interactive logon: Don't display last signed-in = Enabled
```

![Workstation hide last signed-in user enabled](./screenshots/05-workstation-gpo-hide-last-signed-in-user-enabled.png)

**Figure 5:** The workstation security baseline GPO was configured to hide the last signed-in user, reducing username exposure on domain-joined workstations.

---

### 6. Configured Workstation Domain Firewall Policy

The workstation baseline GPO was configured to enforce Windows Defender Firewall on the Domain Profile.

Policy path:

```text
Computer Configuration
 > Policies
   > Windows Settings
     > Security Settings
       > Windows Defender Firewall with Advanced Security
```

Configured values:

```text
Firewall state: On
Inbound connections: Block
Outbound connections: Allow
```

![Workstation domain firewall enabled](./screenshots/06-workstation-gpo-domain-firewall-enabled.png)

**Figure 6:** The workstation security baseline GPO was configured to enforce Windows Defender Firewall on the Domain Profile for domain-joined workstations.

---

### 7. Configured Workstation Audit Logon Events

The workstation baseline GPO was configured to audit successful and failed logon events.

Policy path:

```text
Computer Configuration
 > Policies
   > Windows Settings
     > Security Settings
       > Local Policies
         > Audit Policy
```

Configured setting:

```text
Audit logon events = Success, Failure
```

![Workstation audit logon events enabled](./screenshots/07-workstation-gpo-audit-logon-events-enabled.png)

**Figure 7:** The workstation security baseline GPO was configured to audit successful and failed logon events for improved identity activity monitoring.

---

### 8. Created and Linked the Server Security Baseline GPO

A new server baseline GPO was created and linked to the `Servers` OU.

GPO created:

```text
MRTG-GPO-Server-Security-Baseline
```

![Server baseline GPO linked](./screenshots/08-gpmc-server-security-baseline-gpo-linked.png)

**Figure 8:** The MRTG server security baseline GPO was created and linked to the Servers OU to apply role-based security settings to domain-joined servers.

---

### 9. Configured Server Logon Privacy Control

The server baseline GPO was configured to hide the last signed-in user.

Configured setting:

```text
Interactive logon: Don't display last signed-in = Enabled
```

![Server hide last signed-in user enabled](./screenshots/09-server-gpo-hide-last-signed-in-user-enabled.png)

**Figure 9:** The server security baseline GPO was configured to hide the last signed-in user, reducing username exposure on domain-joined servers.

---

### 10. Configured Server Domain Firewall Policy

The server baseline GPO was configured to enforce Windows Defender Firewall on the Domain Profile.

Configured values:

```text
Firewall state: On
Inbound connections: Block
Outbound connections: Allow
```

![Server domain firewall enabled](./screenshots/10-server-gpo-domain-firewall-enabled.png)

**Figure 10:** The server security baseline GPO was configured to enforce Windows Defender Firewall on the Domain Profile for domain-joined servers.

---

### 11. Configured Server Audit Policy Baseline

The server baseline GPO was configured with stronger auditing than the workstation baseline.

Configured audit settings:

```text
Audit account management = Success, Failure
Audit logon events = Success, Failure
Audit policy change = Success, Failure
```

![Server audit policy baseline enabled](./screenshots/11-server-gpo-audit-policy-baseline-enabled.png)

**Figure 11:** The server security baseline GPO was configured to audit logon events, account management activity, and policy changes for improved monitoring of privileged server activity.

---

### 12. Moved MRTG-LOG01 into the Servers OU

The `MRTG-LOG01` computer object was moved from the default Computers container into the role-based Servers OU.

Target OU:

```text
_MRTG
└── Computers
    └── Servers
```

![MRTG-LOG01 moved into Servers OU](./screenshots/12-aduc-server-object-moved-to-servers-ou.png)

**Figure 12:** The MRTG-LOG01 domain-joined server object was moved into the Servers OU so the server security baseline GPO could apply through OU-based targeting.

---

### 13. Refreshed Group Policy on MRTG-LOG01

Group Policy was manually refreshed on `MRTG-LOG01`.

Command used:

```powershell
gpupdate /force
```

![MRTG-LOG01 gpupdate success](./screenshots/13-mrtg-log01-gpupdate-force-success.png)

**Figure 13:** Group Policy was manually refreshed on MRTG-LOG01 to apply the newly linked server security baseline GPO.

---

### 14. Verified Applied GPO with gpresult

The applied computer policies were reviewed using `gpresult`.

Command used:

```powershell
gpresult /r /scope computer
```

Confirmed applied GPO:

```text
MRTG-GPO-Server-Security-Baseline
```

![MRTG-LOG01 gpresult server baseline applied](./screenshots/14-mrtg-log01-gpresult-server-baseline-applied.png)

**Figure 14:** The gpresult output confirmed that MRTG-LOG01 successfully applied the MRTG server security baseline GPO.

---

### 15. Generated an HTML Group Policy Report

An HTML Group Policy report was generated for deeper validation evidence.

Commands used:

```powershell
mkdir C:\Temp
gpresult /h C:\Temp\mrtg-log01-gpresult.html
start C:\Temp\mrtg-log01-gpresult.html
```

![MRTG-LOG01 gpresult HTML report](./screenshots/15-mrtg-log01-gpresult-html-report.png)

**Figure 15:** An HTML gpresult report was generated for MRTG-LOG01 to provide detailed evidence of applied Group Policy settings.

---

### 16. Reviewed Final Role-Based GPO Structure

The final GPMC structure was reviewed to confirm that both security baseline GPOs were linked to their correct role-based OUs.

![Final role-based GPO structure](./screenshots/16-gpmc-final-role-based-security-baseline-structure.png)

**Figure 16:** Final Group Policy Management view showing separate workstation and server security baseline GPOs linked to their respective role-based OUs.

---

### 17. Created a Post-Lab Checkpoint for MRTG-DC01

A Hyper-V checkpoint was created for `MRTG-DC01` after completing the Group Policy configuration.

![MRTG-DC01 post Lab 15 checkpoint](./screenshots/17a-hyperv-mrtg-dc01-post-lab15-checkpoint.png)

**Figure 17:** A post-lab Hyper-V checkpoint was created for MRTG-DC01 after completing the Lab 15 Group Policy security baseline configuration.

---

### 18. Created a Post-Lab Checkpoint for MRTG-LOG01

A Hyper-V checkpoint was created for `MRTG-LOG01` after validating the server baseline GPO.

![MRTG-LOG01 post Lab 15 checkpoint](./screenshots/17b-hyperv-mrtg-log01-post-lab15-checkpoint.png)

**Figure 18:** A post-lab Hyper-V checkpoint was created for MRTG-LOG01 after validating the server security baseline GPO with gpupdate and gpresult.

---

## Validation Commands Used

### Refresh Group Policy

```powershell
gpupdate /force
```

### Verify Applied Computer Policies

```powershell
gpresult /r /scope computer
```

### Create HTML Group Policy Report

```powershell
mkdir C:\Temp
gpresult /h C:\Temp\mrtg-log01-gpresult.html
start C:\Temp\mrtg-log01-gpresult.html
```

---

## Key Results

| Validation Area | Result |
|---|---|
| Servers OU created | Successful |
| Workstations OU created | Successful |
| Workstation baseline GPO created | Successful |
| Workstation baseline GPO linked | Successful |
| Workstation logon privacy control configured | Successful |
| Workstation firewall baseline configured | Successful |
| Workstation audit logon policy configured | Successful |
| Server baseline GPO created | Successful |
| Server baseline GPO linked | Successful |
| Server logon privacy control configured | Successful |
| Server firewall baseline configured | Successful |
| Server audit policy baseline configured | Successful |
| `MRTG-LOG01` moved into Servers OU | Successful |
| `gpupdate /force` completed on `MRTG-LOG01` | Successful |
| `gpresult` confirmed server baseline applied | Successful |
| HTML Group Policy report generated | Successful |
| Hyper-V checkpoints created | Successful |

---

## Validation Note

At the time of validation, `MRTG-LOG01` was the available domain-joined member server object in the lab environment.

The workstation security baseline GPO was created, linked, and configured for future domain-joined workstation assets. Live policy validation was performed against the server baseline because `MRTG-LOG01` was the available role-based endpoint object.

This keeps the lab accurate: the workstation baseline exists and is ready for workstation assets, while the server baseline was fully validated against an actual server object.

---

## Skills Demonstrated

- Group Policy Object creation
- GPO linking to Organizational Units
- OU-based policy targeting
- Role-based workstation and server policy separation
- Windows Defender Firewall policy enforcement
- Security Options configuration
- Audit Policy configuration
- Computer object movement within Active Directory
- Manual Group Policy refresh using `gpupdate`
- Applied policy validation using `gpresult`
- HTML Group Policy reporting
- Hyper-V checkpoint management
- Enterprise-style documentation and evidence capture

---

## Outcome

By the end of this lab, the MRTG Active Directory environment had separate workstation and server security baseline GPOs linked to dedicated role-based OUs.

The workstation baseline was configured with endpoint security controls, including sign-in privacy protection, Domain Profile firewall enforcement, and logon event auditing.

The server baseline was configured with sign-in privacy protection, Domain Profile firewall enforcement, and expanded auditing for logon events, account management activity, and policy changes.

The `MRTG-LOG01` server object was moved into the Servers OU, Group Policy was refreshed, and `gpresult` confirmed that the server security baseline GPO successfully applied.

This lab strengthened the MRTG identity infrastructure by demonstrating centralized, role-based security enforcement through Active Directory Group Policy.

---

## Next Lab

**Lab-16 — Advanced Group Policy Validation and Operational Troubleshooting**

The next lab will build on this baseline by focusing on deeper Group Policy troubleshooting, policy precedence, inheritance behavior, and operational validation techniques used in real enterprise environments.
