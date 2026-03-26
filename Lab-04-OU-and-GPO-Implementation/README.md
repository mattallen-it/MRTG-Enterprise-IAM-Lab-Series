# Lab-04 — OU and GPO Implementation

---

## Overview

This lab focuses on implementing policy-based identity control within the MRTG Active Directory environment using Organizational Units (OUs) and Group Policy Objects (GPOs).

This phase introduces centralized policy enforcement, enabling standardized configuration, security controls, and scalable identity management across the domain.

---

## Why This Matters

In enterprise and government environments, identity alone is not enough — it must be controlled.

Group Policy enables:

- Centralized configuration management
- Enforcement of security baselines
- Standardization across systems and users
- Scalable policy application through OU structure

Without proper policy enforcement, identity systems become inconsistent, insecure, and difficult to manage.

---

## Environment

| Component           | Value              |
|--------------------|-------------------|
| Domain Name        | mrtg.local        |
| Domain Controller  | MRTG-DC01         |
| Tools Used         | Group Policy Management Console (GPMC) |
| Platform           | Windows Server 2022 |

---

## Architecture

### Organizational Unit Structure

mrtg.local
│
├── IT
├── Security
├── HR
├── Finance
├── Operations
├── Engineering
├── Executives

---

### Policy Model

- Policies linked at the OU level
- Inheritance used to propagate configurations
- Targeting based on identity (users and groups)

---

## Security Considerations

- Policies applied using least privilege principles
- Separation of OUs enables scoped policy enforcement
- Reduces risk of domain-wide misconfiguration
- Establishes baseline for compliance and auditing

---

## Lab Steps and Evidence

### 1. Created Organizational Unit (OU) Structure
OUs were structured to align with MRTG business departments and support targeted policy application.

![OU Structure](./images/step01_ou_structure.png)

---

### 2. Opened Group Policy Management Console (GPMC)
The GPMC tool was used to manage and deploy Group Policy across the domain.

![GPMC Console](./images/step02_gpmc_console.png)

---

### 3. Created Group Policy Object (GPO)
A new GPO was created to enforce baseline system configurations.

![GPO Creation](./images/step03_gpo_creation.png)

---

### 4. Linked GPO to Organizational Unit
The GPO was linked to a specific OU to apply policy to targeted users and systems.

![GPO Link](./images/step04_gpo_link.png)

---

### 5. Configured Policy Settings
Baseline security configurations were applied, such as:

- Password policy
- Account lockout policy
- System configuration settings

![GPO Settings](./images/step05_gpo_settings.png)

---

### 6. Applied and Verified Group Policy
Group Policy was applied and validated to ensure correct enforcement.

- gpupdate executed
- gpresult used to confirm policy application

![GPO Verification](./images/step06_gpo_verification.png)

---

## Outcome

Policy-based identity control was successfully implemented within the MRTG domain.

- Organizational Units structured for targeted policy application
- Group Policy Objects created and linked to OUs
- Security baseline configurations enforced
- Policy application validated through system tools

This environment now supports centralized configuration management and scalable access control.

---

## IAM / Security Perspective

This lab demonstrates how identity systems are controlled through policy enforcement.

By applying Group Policy at the OU level:

- Identity is no longer static — it is governed
- Access control becomes scalable and repeatable
- Security baselines can be enforced across the environment

This mirrors real-world enterprise IAM practices, where policy drives consistency, compliance, and security.

---

## Next Lab

[Lab-05 — Identity Lifecycle Management](../Lab-05-Identity-Lifecycle-Management)

The next lab will cover:

- User provisioning (Joiner process)  
- Role changes (Mover process)  
- Account deactivation (Leaver process)  
- Identity lifecycle governance  

---
