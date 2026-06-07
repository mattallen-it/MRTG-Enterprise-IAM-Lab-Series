# Lab 02 — AD DS Deployment Preparation

![Platform](https://img.shields.io/badge/Platform-Windows%20Server%202022-blue)
![Technology](https://img.shields.io/badge/Technology-Active%20Directory-blue)
![Focus](https://img.shields.io/badge/Focus-AD%20DS%20Deployment%20Preparation-orange)
![Domain](https://img.shields.io/badge/Domain-mrtg.local-purple)
![Authentication](https://img.shields.io/badge/Authentication-Kerberos-brightgreen)
![Validation](https://img.shields.io/badge/Validation-Role%20Installed-lightgrey)

---

## Overview

This lab prepares `MRTG-DC01` for Active Directory Domain Services deployment by installing the AD DS role on Windows Server 2022.

This lab does not complete the domain controller promotion. Instead, it installs the required AD DS components and validates that the server is ready for the next phase of identity infrastructure deployment.

The actual domain controller promotion and creation of the `mrtg.local` forest will be completed in Lab 03.

---

## Objective

Install the Active Directory Domain Services role on `MRTG-DC01` and prepare the server for domain controller promotion.

---

## Why This Matters

Active Directory Domain Services is the foundation of centralized identity management in many enterprise and government environments.

Before a server can become a domain controller, the AD DS role must be installed.

This lab prepares the server for:

- Centralized authentication
- Kerberos-based identity services
- Domain controller promotion
- AD-integrated DNS
- Group Policy enforcement
- Directory-based access control
- Future IAM governance and auditability

---

## Scope

### Included

- AD DS role installation
- Required feature installation
- Server Manager validation
- Promotion readiness confirmation
- Pre-promotion infrastructure checkpoint

### Not Included

- Domain controller promotion
- New forest creation
- DNS zone validation
- SRV record validation
- Kerberos validation
- Organizational Unit design
- Group Policy configuration
- Domain-joined workstation configuration

---

## Environment

| Component | Value |
|---|---|
| VM Name | `MRTG-DC01` |
| Operating System | Windows Server 2022 |
| Role Installed | Active Directory Domain Services |
| Promotion Status | Pending — completed in Lab 03 |
| Virtualization | Hyper-V |
| Planned Domain | `mrtg.local` |

---

## Architecture

`MRTG-DC01` is being prepared to become the first domain controller for the MRTG environment.

At this stage, the server has the AD DS role installed, but it has not yet been promoted into a domain controller.

The server will later provide:

- Active Directory Domain Services
- AD-integrated DNS
- Kerberos-based authentication
- Centralized identity authority
- Group Policy support

---

## Implementation and Validation

### 1. Server Manager Role Installation

The AD DS role installation was started through Server Manager using the Add Roles and Features wizard.

![AD DS Role Installation](images/01-ad-ds-role-installation.png)

---

### 2. AD DS Role Selection

The Active Directory Domain Services role was selected for installation.

Required supporting features were included as part of the role installation process.

![AD DS Role Selection](images/02-ad-ds-role-selection.png)

---

### 3. Installation Confirmation

The installation wizard confirmed that the AD DS role and required features were ready to install.

![AD DS Installation Confirmation](images/03-ad-ds-installation-confirmation.png)

---

### 4. Installation Progress

The AD DS role installation completed successfully on `MRTG-DC01`.

![AD DS Installation Progress](images/04-ad-ds-installation-progress.png)

---

### 5. Promotion Required Notification

After installation, Server Manager displayed a notification indicating that post-deployment configuration was required.

This confirms that the AD DS role was installed and that the next step is domain controller promotion.

![AD DS Promotion Required](images/05-ad-ds-promotion-required.png)

---

### 6. Pre-Promotion Checkpoint

A Hyper-V checkpoint was created after installing the AD DS role and before completing domain controller promotion.

This provides a rollback point before the identity activation step in Lab 03.

![Pre-Promotion Checkpoint](images/06-pre-promotion-checkpoint.png)

---

## Security Considerations

The AD DS role was installed in an isolated lab environment to prepare for centralized identity services.

Security considerations include:

- AD DS should be installed only on designated domain controller systems
- Domain controllers should be treated as Tier 0 identity infrastructure
- Administrative access should be limited to authorized privileged accounts
- Server configuration should be documented before promotion
- A rollback point should be created before major identity infrastructure changes

---

## Risk Addressed

Without the AD DS role installed, `MRTG-DC01` cannot be promoted into a domain controller or provide centralized identity services.

This lab reduces that risk by preparing the server for domain controller promotion in a controlled and documented way.

The main risks addressed include:

- No AD DS role installed
- No preparation for centralized authentication
- No baseline before domain controller promotion
- No rollback point before identity activation
- Unstructured deployment of identity infrastructure

---

## Control Mapping

| Control Area | How This Lab Supports It |
|---|---|
| Identity infrastructure preparation | Installs the AD DS role on the planned domain controller |
| Change control readiness | Creates a checkpoint before domain controller promotion |
| Operational consistency | Uses a controlled installation process through Server Manager |
| Security planning | Prepares a designated Tier 0 identity server |
| Audit readiness | Captures role installation and validation evidence |

---

## Validation

| Validation Item | Result |
|---|---|
| Server Manager used for role installation | Passed |
| AD DS role selected | Passed |
| Required features included | Passed |
| AD DS role installed successfully | Passed |
| Promotion required notification appeared | Passed |
| Pre-promotion checkpoint created | Passed |

---

## Evidence Collected

| Evidence | File |
|---|---|
| AD DS role installation | `images/01-ad-ds-role-installation.png` |
| AD DS role selection | `images/02-ad-ds-role-selection.png` |
| Installation confirmation | `images/03-ad-ds-installation-confirmation.png` |
| Installation progress | `images/04-ad-ds-installation-progress.png` |
| Promotion required notification | `images/05-ad-ds-promotion-required.png` |
| Pre-promotion checkpoint | `images/06-pre-promotion-checkpoint.png` |

---

## Lessons Learned

This lab reinforced that installing the AD DS role and promoting a domain controller are separate phases.

Installing the role prepares the server, but it does not activate the domain or create the forest.

Separating these steps makes the lab cleaner because the role installation, promotion process, DNS validation, and Kerberos validation can each be documented clearly.

---

## Outcome

Lab 02 successfully installed the Active Directory Domain Services role on `MRTG-DC01`.

The server is now prepared for domain controller promotion in Lab 03.

The environment is ready for the next phase:

- Promoting `MRTG-DC01` to domain controller
- Creating the `mrtg.local` forest
- Configuring AD-integrated DNS
- Validating Kerberos authentication
- Establishing the centralized identity boundary

---

## Next Lab

[Lab 03 — Domain Controller Promotion](../Lab-03-Domain-Controller-Promotion/)

Lab 03 will promote `MRTG-DC01` to the first domain controller, create the `mrtg.local` forest, configure AD-integrated DNS, and validate core domain services.
