# Lab 04 — OU Design and Group Policy Implementation

---

### Overview

In this lab, I implemented Group Policy Objects (GPOs) within the Monroe Redstone Technology Group (MRTG) environment to enforce centralized security configurations across domain-joined systems.

This lab demonstrates how identity-driven policy enforcement is used to control user and device behavior, a critical component of Identity and Access Management (IAM) in enterprise and government environments.

This approach aligns with enterprise security frameworks by ensuring consistent, auditable control over user sessions and endpoint configurations.

---

## Objective

- Deploy and configure Group Policy Objects (GPOs)
- Enforce security controls on domain-joined endpoints
- Validate policy application using gpresult
- Understand how Group Policy supports IAM governance and compliance

---
##  Environment

| Component         | Details                              |
|------------------|--------------------------------------|
| Organization     | Monroe Redstone Technology Group     |
| Domain           | mrtg.local                           |
| Domain Controller| Windows Server 2019 (DC01)           |
| Client Machine   | Windows 11 (CLIENT01)                |
| Virtualization   | Hyper-V                              |

---

## Architecture 

- DC01 – Domain Controller (Active Directory, DNS, Group Policy Management)
- CLIENT01 – Domain-joined workstation

### Organizational Units (OU Structure)
- Users
- Groups
- Computers
- Departments (HR, IT, Finance, Operations, Engineering)
