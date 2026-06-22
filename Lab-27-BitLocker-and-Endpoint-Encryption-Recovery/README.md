# Lab 27 - BitLocker and Endpoint Encryption Recovery

![Platform](https://img.shields.io/badge/Platform-Windows%2011-blue)
![Technology](https://img.shields.io/badge/Technology-BitLocker-blue)
![Focus](https://img.shields.io/badge/Focus-Endpoint%20Encryption-green)
![Security](https://img.shields.io/badge/Security-Recovery%20Key%20Governance-red)
![Validation](https://img.shields.io/badge/Validation-Completed-brightgreen)
![Documentation](https://img.shields.io/badge/Documentation-Audit%20Ready-purple)

---

## Overview

In this lab, I enabled and validated BitLocker protection on `MRTG-CLIENT-01` while documenting secure recovery key handling.

The endpoint drive was already encrypted, but BitLocker protection was off because no key protectors were configured.

After activation, the protection status changed to `Protection On`, and the operating system drive showed both TPM and numerical password protectors.

The recovery key value was intentionally excluded from screenshots and public documentation.

---

## Business Problem

MRTG needed endpoint encryption to protect workstation data if a device was lost, stolen, removed, or accessed while powered off.

Enabling encryption alone is not enough. The organization must also confirm that protection is active and that valid key protectors are configured.

Recovery keys create an additional governance requirement because anyone with the recovery password may be able to unlock the protected drive.

This lab addressed the need to:

- Validate endpoint encryption status
- Confirm TPM readiness
- Enable BitLocker protection
- Create a numerical recovery password
- Document secure recovery key handling
- Prevent recovery material from appearing in public evidence
- Validate the final protection state
- Explain BitLocker limitations within a layered security model

---

## Lab Summary

I created pre-lab checkpoints for `MRTG-DC01` and `MRTG-CLIENT-01`.

On the client, I used `manage-bde` and `Get-Tpm` to review the initial state. The drive was fully encrypted, but protection was off and no key protectors were present.

I enabled BitLocker protection and selected the Microsoft Print to PDF workflow for recovery key backup without exposing the key in the documentation.

The BitLocker Control Panel showed `C: BitLocker on`, and final command-line validation confirmed `Protection On` with TPM and numerical password protectors.

Finally, I created post-lab checkpoints for the client and domain controller.

---

## Objectives

- Create pre-lab checkpoints for the client and domain controller
- Review the initial BitLocker state
- Validate TPM readiness
- Identify the difference between encryption and active protection
- Enable BitLocker protection
- Configure TPM and numerical password protectors
- Use a recovery key backup workflow
- Exclude recovery key material from public documentation
- Validate BitLocker through Control Panel
- Validate the final state with `manage-bde`
- Document BitLocker limitations
- Create post-lab checkpoints

---

## Scope

### Included

- Hyper-V checkpoints
- TPM readiness validation
- Initial BitLocker status review
- BitLocker protection activation
- Recovery key backup workflow
- Recovery key handling documentation
- Control Panel validation
- Command-line protection validation
- Key protector validation
- Layered endpoint security analysis
- Audit evidence collection

### Not Included

- Forced BitLocker recovery testing
- Recovery password entry testing
- Recovery key escrow to Active Directory
- Recovery key escrow to Microsoft Entra ID
- Group Policy deployment
- Microsoft Intune deployment
- Organization-wide compliance reporting
- BitLocker suspension testing
- Removable drive encryption
- Secure Boot validation
- Production recovery key access auditing

---

## Environment

| Component | Details |
|---|---|
| Organization | Monroe Redstone Technology Group |
| Domain | `mrtg.local` |
| Domain Controller | `MRTG-DC01` |
| Endpoint | `MRTG-CLIENT-01` |
| Operating System | Windows 11 |
| Protected Volume | `C:` |
| Encryption Tool | BitLocker Drive Encryption |
| Status Tool | `manage-bde` |
| TPM Tool | `Get-Tpm` |
| Encryption Method | `XTS-AES 128` |
| Encryption Scope | Used space only |
| Hypervisor | Hyper-V |

---

## Scenario

MRTG needs to protect data stored on a Windows endpoint.

The drive must remain inaccessible to unauthorized users if the virtual disk or device is removed, stolen, or accessed while powered off.

The endpoint must use its TPM for normal startup and retain a numerical recovery password for authorized recovery scenarios.

The security model used in this lab was:

```text
Validate TPM → Review Encryption State → Protect Recovery Material → Enable Protection → Validate Key Protectors
```

---

## Encryption and Protection Model

BitLocker encryption status and protection status are related but separate.

| State | Meaning |
|---|---|
| Drive encrypted | Data on the volume has been encrypted |
| Protection off | BitLocker protectors are not actively enforcing access protection |
| Protection on | BitLocker protectors are actively protecting access to the encrypted volume |
| TPM protector | The TPM releases key material when startup integrity checks succeed |
| Numerical password | A recovery password can unlock the volume when normal protection cannot |

The initial drive was encrypted but unprotected.

The final goal was:

```text
Encrypted Volume + TPM Protector + Numerical Recovery Password + Protection On
```

---

## Implementation Steps

### Step 1 - Created DC01 Pre-Lab Checkpoint

A checkpoint was created for `MRTG-DC01` before beginning the lab.

Checkpoint name:

```text
MRTG-DC01_Pre-Lab27-BitLocker-Endpoint-Recovery
```

![DC01 Pre-Lab Checkpoint](screenshots/lab-27-01-dc01-pre-lab-checkpoint.png)

---

### Step 2 - Created CLIENT-01 Pre-Lab Checkpoint

A checkpoint was created for `MRTG-CLIENT-01` before changing its BitLocker configuration.

Checkpoint name:

```text
MRTG-CLIENT-01_Pre-Lab27-BitLocker-Endpoint-Recovery
```

This provided a rollback point for the client before protection was activated.

![CLIENT-01 Pre-Lab Checkpoint](screenshots/lab-27-02-client01-pre-lab-checkpoint.png)

---

### Step 3 - Validated Initial BitLocker and TPM Status

The initial BitLocker and TPM states were reviewed.

Commands used:

```powershell
manage-bde -status
Get-Tpm
```

Initial BitLocker results:

| Setting | Initial State |
|---|---|
| Volume | `C:` |
| BitLocker Version | `2.0` |
| Conversion Status | Used Space Only Encrypted |
| Percentage Encrypted | `100.0%` |
| Encryption Method | `XTS-AES 128` |
| Protection Status | `Protection Off` |
| Lock Status | `Unlocked` |
| Identification Field | Unknown |
| Key Protectors | None Found |

TPM validation:

| TPM Setting | Result |
|---|---|
| `TpmPresent` | `True` |
| `TpmReady` | `True` |
| `TpmEnabled` | `True` |
| `TpmActivated` | `True` |
| `TpmOwned` | `True` |
| `RestartPending` | `False` |

The endpoint was encrypted, but BitLocker was not actively protected because no key protectors were configured.

![TPM and BitLocker Status Before Protection](screenshots/lab-27-03-tpm-and-bitlocker-status-before-protection.png)

---

### Step 4 - Selected the Recovery Key Print Workflow

During BitLocker activation, Windows presented options for backing up the recovery key.

Microsoft Print to PDF was selected as the lab recovery key backup workflow.

The actual recovery password was not captured in any public screenshot or README content.

![BitLocker Recovery Key Print Option](screenshots/lab-27-04-bitlocker-recovery-key-print-option.png)

---

## Recovery Key Handling

A BitLocker recovery password is sensitive authentication material.

It can bypass the normal TPM-based startup process and unlock the protected volume during a recovery event.

Recovery keys should not be stored in:

- Public GitHub repositories
- LinkedIn posts
- Unprotected screenshots
- Public documentation
- Unencrypted email
- General-purpose shared folders
- Unapproved personal storage

Approved production storage may include:

- Active Directory Domain Services
- Microsoft Entra ID
- Microsoft Intune
- A privileged access management platform
- An approved enterprise recovery repository

Access should be restricted, logged, reviewed, and tied to a valid support or recovery request.

---

### Step 5 - Confirmed BitLocker Through Control Panel

The BitLocker Control Panel confirmed that protection was active on the operating system drive.

Displayed status:

```text
C: BitLocker on
```

The interface also provided options to:

- Suspend protection
- Back up the recovery key
- Turn off BitLocker

![BitLocker Control Panel Enabled](screenshots/lab-27-05-bitlocker-control-panel-enabled.png)

---

### Step 6 - Validated Protection and Key Protectors

The final BitLocker state was validated from the command line.

Command used:

```powershell
manage-bde -status C:
```

Final results:

| Setting | Final State |
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

The presence of both protectors confirmed that the endpoint could use TPM-based startup protection and an authorized recovery password.

![BitLocker Protection On Validation](screenshots/lab-27-06-bitlocker-protection-on-validation.png)

---

### Step 7 - Created CLIENT-01 Post-Lab Checkpoint

A post-lab checkpoint was created for the protected client.

Checkpoint name:

```text
MRTG-CLIENT-01_Post-Lab27-BitLocker-Endpoint-Recovery-Validated
```

![CLIENT-01 Post-Lab Checkpoint](screenshots/lab-27-07-client01-post-lab-checkpoint.png)

---

### Step 8 - Created DC01 Post-Lab Checkpoint

A post-lab checkpoint was created for the domain controller.

Checkpoint name:

```text
MRTG-DC01_Post-Lab27-BitLocker-Endpoint-Recovery-Validated
```

![DC01 Post-Lab Checkpoint](screenshots/lab-27-08-dc01-post-lab-checkpoint.png)

---

## Before and After Comparison

| Control | Before | After |
|---|---|---|
| Drive encryption | 100% encrypted | 100% encrypted |
| Encryption method | XTS-AES 128 | XTS-AES 128 |
| Protection status | Off | On |
| TPM readiness | Ready | Ready |
| TPM protector | Not present | Present |
| Numerical password | Not present | Present |
| Control Panel status | Not validated | BitLocker on |
| Recovery handling | Not documented | Documented without exposing the key |

---

## BitLocker Limitations

BitLocker protects data at rest.

It is most effective when a device is:

- Powered off
- Lost or stolen
- Accessed through removed storage
- Booted outside its trusted configuration

BitLocker does not independently protect against:

- Attackers using an already unlocked session
- Compromised user credentials
- Malware running after authentication
- Excessive local administrator access
- Weak account security
- Missing patches
- Unmonitored endpoint activity
- Recovery key exposure
- Data copied from an unlocked system

BitLocker must be combined with authentication, least privilege, endpoint hardening, monitoring, patching, and controlled recovery procedures.

---

## IAM and Security Relevance

BitLocker is an endpoint security technology, but recovery key access is an IAM responsibility.

| IAM Area | Relevance |
|---|---|
| Authentication | TPM-based protection supports trusted startup |
| Authorization | Only approved personnel should retrieve recovery keys |
| Least privilege | Recovery key access should be narrowly assigned |
| Privileged access | A recovery password can unlock a protected device |
| Audit readiness | Recovery requests and key retrieval should be logged |
| Identity governance | Recovery access requires ownership and periodic review |
| Operational resilience | Authorized recovery prevents permanent loss of access |
| Device identity | TPM-backed protection ties access to trusted hardware state |

---

## Risk Addressed

This lab addressed risks including:

- Data exposure from lost or stolen endpoints
- Mistaking encryption for active protection
- Missing key protectors
- Unprotected recovery passwords
- Public exposure of recovery material
- Lack of post-configuration validation
- Incomplete endpoint security documentation
- Treating encryption as a standalone security solution

---

## Control Mapping

| Control Area | Lab Implementation |
|---|---|
| Endpoint encryption | Confirmed the operating system volume was encrypted |
| Data-at-rest protection | Enabled active BitLocker protection |
| Hardware-backed security | Validated TPM readiness and the TPM protector |
| Recovery capability | Configured a numerical recovery password |
| Recovery governance | Excluded recovery material from public evidence |
| Security validation | Used Control Panel and `manage-bde` |
| Least privilege | Treated recovery access as privileged |
| Layered security | Documented controls BitLocker does not replace |
| Change protection | Created pre-lab and post-lab checkpoints |
| Audit readiness | Preserved non-sensitive validation evidence |

---

## Validation Summary

| Test | Expected Result | Actual Result | Status |
|---|---|---|---|
| DC01 pre-lab checkpoint created | Rollback point exists | Checkpoint created | Passed |
| CLIENT-01 pre-lab checkpoint created | Client rollback point exists | Checkpoint created | Passed |
| Initial encryption reviewed | Drive state documented | 100% encrypted | Passed |
| Initial protection reviewed | Protection state documented | Protection Off | Passed |
| TPM validated | TPM present and ready | TPM ready | Passed |
| Initial protectors reviewed | Missing protectors identified | None found | Passed |
| Recovery workflow selected | Backup method available | Print workflow selected | Passed |
| Recovery key protected | Key absent from public evidence | Key not exposed | Passed |
| Control Panel validated | BitLocker shown as enabled | BitLocker on | Passed |
| Protection activated | Final protection status on | Protection On | Passed |
| TPM protector validated | TPM listed | TPM present | Passed |
| Numerical protector validated | Recovery protector listed | Numerical password present | Passed |
| CLIENT-01 post-lab checkpoint created | Validated client state preserved | Checkpoint created | Passed |
| DC01 post-lab checkpoint created | Validated DC state preserved | Checkpoint created | Passed |

---

## Evidence Collected

| Evidence | Screenshot |
|---|---|
| DC01 pre-lab checkpoint | `screenshots/lab-27-01-dc01-pre-lab-checkpoint.png` |
| CLIENT-01 pre-lab checkpoint | `screenshots/lab-27-02-client01-pre-lab-checkpoint.png` |
| Initial TPM and BitLocker status | `screenshots/lab-27-03-tpm-and-bitlocker-status-before-protection.png` |
| Recovery key print workflow | `screenshots/lab-27-04-bitlocker-recovery-key-print-option.png` |
| BitLocker Control Panel validation | `screenshots/lab-27-05-bitlocker-control-panel-enabled.png` |
| Final protection and key protectors | `screenshots/lab-27-06-bitlocker-protection-on-validation.png` |
| CLIENT-01 post-lab checkpoint | `screenshots/lab-27-07-client01-post-lab-checkpoint.png` |
| DC01 post-lab checkpoint | `screenshots/lab-27-08-dc01-post-lab-checkpoint.png` |

---

## Troubleshooting Notes

The initial status created an important distinction:

```text
Percentage Encrypted: 100.0%
Protection Status: Protection Off
Key Protectors: None Found
```

The drive contained encrypted data, but the protection mechanism was not actively enforced.

The issue was resolved by activating BitLocker and configuring TPM and numerical password protectors.

Final validation showed:

```text
Protection Status: Protection On
Key Protectors:
- TPM
- Numerical Password
```

---

## Security Considerations

The recovery password was intentionally excluded from the evidence.

The print-to-PDF workflow was acceptable for demonstrating the backup option in a lab, but storing a recovery key in a local PDF is not an appropriate production escrow strategy unless the file is transferred immediately to an approved protected repository and securely removed from local storage.

Hyper-V checkpoints were used as lab rollback points. They are not substitutes for:

- BitLocker recovery passwords
- System backups
- Recovery key escrow
- Disaster recovery procedures
- Restore testing

---

## What I Would Do Differently in Production

In a production or government-regulated environment, I would implement:

- Automatic recovery key escrow to Active Directory or Microsoft Entra ID
- Microsoft Intune or Group Policy enforcement
- Restricted recovery key retrieval permissions
- Logged recovery key access
- Help desk identity verification procedures
- Ticket requirements for key retrieval
- Encryption compliance reporting
- Alerts when BitLocker is suspended
- Alerts when protection is disabled
- Recovery testing under controlled conditions
- Secure Boot validation
- TPM health monitoring
- Standard encryption algorithms defined by policy
- Removable media encryption controls
- Recovery key rotation after disclosure
- Centralized endpoint monitoring
- Documented device retirement procedures

---

## Lessons Learned

- Encryption percentage and protection status are separate measurements
- A fully encrypted drive can still show protection off
- BitLocker requires active key protectors
- TPM readiness should be validated before activation
- Numerical recovery passwords are privileged security material
- Recovery keys should never appear in public documentation
- Control Panel and command-line validation provide complementary evidence
- BitLocker protects data at rest, not an already unlocked session
- Encryption must be combined with identity and endpoint controls
- Hyper-V checkpoints do not replace recovery key escrow
- A complete recovery test would require a separate controlled validation

---

## Skills Demonstrated

- BitLocker administration
- Endpoint encryption validation
- TPM readiness assessment
- `manage-bde` usage
- PowerShell TPM validation
- Recovery key governance
- Key protector validation
- Data-at-rest security analysis
- Sensitive evidence handling
- Layered endpoint security analysis
- Hyper-V checkpoint management
- Audit documentation
- Production control planning

---

## Outcome

Lab 27 successfully enabled and validated BitLocker protection on `MRTG-CLIENT-01`.

The lab demonstrated:

- Pre-change rollback planning
- TPM readiness validation
- Identification of an encrypted but unprotected drive
- BitLocker activation
- TPM and numerical password protection
- Secure recovery key documentation
- Control Panel and command-line validation
- Layered endpoint security analysis
- Post-change rollback planning
- Audit-ready evidence without exposing recovery material

The endpoint finished the lab with `Protection On`, a TPM protector, and a numerical recovery password.

---

## Next Lab

[Lab 28 - Local Administrator Access Review and Remediation](../Lab-28-Local-Administrator-Access-Review-and-Remediation/)

Lab 28 will review local administrator exposure, validate privileged endpoint access, remove unnecessary membership, and document the remediation process.
