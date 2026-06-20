# Lab 25 - Service Account Governance Foundation

![Platform](https://img.shields.io/badge/Platform-Windows%20Server-blue)
![Technology](https://img.shields.io/badge/Technology-Active%20Directory-blue)
![Focus](https://img.shields.io/badge/Focus-Service%20Account%20Governance-green)
![Security](https://img.shields.io/badge/Security-Least%20Privilege-red)
![Validation](https://img.shields.io/badge/Validation-Completed-brightgreen)
![Documentation](https://img.shields.io/badge/Documentation-Audit%20Ready-purple)

---

## Overview

In this lab, I established a service account governance foundation within the Monroe Redstone Technology Group Active Directory environment.

Service accounts are non-human identities used by applications, services, scripts, and scheduled tasks. When these accounts are not properly governed, they can become long-term security risks through excessive permissions, unclear ownership, unmanaged credentials, and limited monitoring.

This lab focused on creating, documenting, and validating a governed service account using Active Directory Users and Computers.

---

## Business Problem

MRTG needed a repeatable process for creating and managing service accounts in Active Directory.

Without a governance standard, service accounts may have:

- Unclear ownership
- Inconsistent naming
- Undocumented technical purposes
- Excessive permissions
- Unmanaged passwords
- No review schedule
- No documented dependencies
- No defined retirement process

These weaknesses can result in orphaned accounts, persistent access, audit findings, operational outages, and unauthorized privilege.

This lab addressed that problem by establishing a basic service account standard covering naming, placement, ownership, purpose, privilege, review frequency, and evidence.

---

## Lab Summary

I created a service account named `svc-audit-review` in the existing Service Accounts OU.

The account followed the MRTG service account naming convention and included a description identifying its owner and quarterly review requirement.

I reviewed the account configuration and confirmed that it belonged only to the default Domain Users group. No privileged group membership was assigned.

Finally, I validated the account in Active Directory Users and Computers and created a post-lab Hyper-V checkpoint.

---

## Objectives

- Create a pre-lab Hyper-V checkpoint
- Review the existing Service Accounts OU
- Create a governed service account
- Apply a consistent naming standard
- Document account ownership and review frequency
- Validate the service account logon name
- Review password and expiration settings
- Confirm group membership
- Verify that no privileged access was assigned
- Create a service account inventory entry
- Create a post-lab Hyper-V checkpoint

---

## Scope

### Included

- Service account naming
- Dedicated OU placement
- Ownership documentation
- Review frequency documentation
- Account configuration review
- Group membership review
- Least-privilege validation
- Inventory creation
- Screenshot evidence
- Hyper-V checkpoints

### Not Included

- Application or service integration
- Scheduled task configuration
- Group Managed Service Account deployment
- Automated password rotation
- Service account activity monitoring
- Privileged access assignment
- Logon restriction configuration
- Production request and approval workflow
- Credential vault integration
- Service dependency testing

---

## Environment

| Component | Details |
|---|---|
| Organization | Monroe Redstone Technology Group |
| Domain | `mrtg.local` |
| Domain Controller | `MRTG-DC01` |
| Management Tool | Active Directory Users and Computers |
| Target OU | `_MRTG\Service Accounts` |
| Service Account | `svc-audit-review` |
| Display Name | Service Audit Review |
| Account Type | Non-human identity |
| Owner | IT Operations |
| Review Frequency | Quarterly |
| Hypervisor | Hyper-V |

---

## Scenario

MRTG requires a service account for a future audit-review automation scenario.

Before the account is used by a script, service, or scheduled task, it must be created under a documented governance standard.

The governance model used in this lab was:

```text
Define Purpose → Assign Owner → Create Account → Restrict Privilege → Document Review → Validate Configuration
```

The account was intentionally created without privileged group membership.

---

## Service Account Governance Standard

MRTG service accounts should:

- Use a recognizable naming convention
- Be stored in the dedicated Service Accounts OU
- Have a documented business or technical purpose
- Have an assigned business or technical owner
- Avoid privileged membership unless formally approved
- Use only the permissions required for the assigned function
- Be reviewed on a recurring schedule
- Have documented system and application dependencies
- Be monitored for abnormal activity
- Be disabled when no longer required
- Use managed credentials where supported

Naming standard:

```text
svc-[function]-[purpose]
```

Account created:

```text
svc-audit-review
```

This naming convention identifies the account as a non-human identity and describes its intended function.

---

## Implementation Steps

### Step 1 - Created Pre-Lab Checkpoint

A Hyper-V checkpoint was created before making Lab 25 changes.

Checkpoint name:

```text
MRTG-DC01_Pre-Lab25-Service-Account-Governance
```

This provided a rollback point before creating the new service account.

![Pre-Lab Checkpoint](screenshots/lab-25-01-pre-lab-checkpoint.png)

---

### Step 2 - Reviewed the Service Account Inventory

The existing Service Accounts OU was reviewed in Active Directory Users and Computers.

OU path:

```text
mrtg.local\_MRTG\Service Accounts
```

The OU contained service-style accounts, including:

```text
Service App Deploy
Service Audit Review
Service Backup
```

A dedicated OU makes non-human identities easier to locate, review, govern, and report on.

![Service Account Inventory View](screenshots/lab-25-02-service-account-inventory-view.png)

---

### Step 3 - Documented the Service Account

The `svc-audit-review` account was created with a descriptive display name and governance information.

Account configuration:

| Field | Value |
|---|---|
| First Name | Service |
| Last Name | Audit Review |
| Display Name | Service Audit Review |
| User Logon Name | `svc-audit-review` |
| OU | `_MRTG\Service Accounts` |
| Account Type | Service account |
| Owner | IT Operations |
| Review Frequency | Quarterly |
| Purpose | Audit-review simulation and governance documentation |

Description:

```text
Lab 25 svc acct. Owner: IT Ops. Review: Qtrly.
```

The Description field provides administrators with immediate ownership and review information directly inside Active Directory.

![Service Account Description](screenshots/lab-25-03-service-account-description.png)

---

### Step 4 - Validated Group Membership

The service account's group membership was reviewed.

Validated membership:

```text
Domain Users
```

The account was not added to privileged groups such as:

```text
Domain Admins
Enterprise Admins
Schema Admins
Administrators
Account Operators
Server Operators
Backup Operators
```

This confirmed that the account started with standard domain user access and no unnecessary administrative privileges.

![Service Account Group Membership](screenshots/lab-25-04-service-account-group-membership.png)

---

### Step 5 - Reviewed Logon and Password Settings

The service account logon name was confirmed as:

```text
svc-audit-review@mrtg.local
```

Validated settings:

| Setting | Configuration |
|---|---|
| User must change password at next logon | Disabled |
| User cannot change password | Enabled |
| Password never expires | Enabled |
| Store password using reversible encryption | Disabled |
| Account expires | Never |

The account was configured for lab continuity. These settings are not a blanket production recommendation.

![Service Account Logon Settings](screenshots/lab-25-05-service-account-logon-settings.png)

---

### Step 6 - Validated the Service Account

The Service Accounts OU was reviewed after configuration.

Validation confirmed that Service Audit Review was present alongside the existing service accounts.

Final account state:

| Validation Item | Result |
|---|---|
| Account exists | Passed |
| Account is in the Service Accounts OU | Passed |
| Naming standard followed | Passed |
| Ownership documented | Passed |
| Review frequency documented | Passed |
| Logon name confirmed | Passed |
| Group membership reviewed | Passed |
| No privileged group membership assigned | Passed |

![Service Account Validation](screenshots/lab-25-06-service-account-validation.png)

---

### Step 7 - Created Post-Lab Checkpoint

A post-lab Hyper-V checkpoint was created after validating the service account.

Checkpoint name:

```text
MRTG-DC01_Post-Lab25-Service-Account-Governance-Validated
```

This preserved the completed Lab 25 state before beginning the least-privilege scheduled task configuration in Lab 26.

![Post-Lab Checkpoint](screenshots/lab-25-07-post-lab-checkpoint.png)

---

## Service Account Inventory

| Account Name | Display Name | Purpose | Owner | Privilege Level | Review Frequency | Status |
|---|---|---|---|---|---|---|
| `svc-audit-review` | Service Audit Review | Audit-review simulation and governance documentation | IT Operations | Standard domain user | Quarterly | Active |

A production inventory should also include:

- Business owner
- Technical owner
- Supported application or service
- Creation date
- Approval record
- Credential rotation method
- Last password rotation date
- Last successful logon
- Assigned permissions
- System dependencies
- Review date
- Retirement date
- Current status

---

## Password Management Consideration

The `Password never expires` option was enabled for lab continuity.

This setting creates risk in production because a compromised password could remain valid indefinitely.

Production alternatives should include:

- Group Managed Service Accounts
- Automated password rotation
- Privileged access management platforms
- Enterprise password vaults
- Long, randomly generated credentials
- Credential access auditing
- Documented rotation procedures
- Dependency testing before rotation

A Group Managed Service Account is generally preferable when the application or service supports it because Active Directory manages the password automatically.

---

## Risk Addressed

Unmanaged service accounts create long-term security risk because they may have unclear ownership, excessive permissions, weak documentation, or no recurring review process.

This lab addressed risks including:

- Orphaned service accounts
- Excessive privilege
- Unclear ownership
- Poor account documentation
- Unmanaged credentials
- Inconsistent naming
- Missing review schedules
- Difficult audit reporting
- Undocumented non-human identities
- Accounts remaining active after their purpose ends

---

## Control Mapping

| Control Area | Lab Implementation |
|---|---|
| Non-human identity governance | Created and documented a service account |
| Naming standard | Applied the `svc-[function]-[purpose]` format |
| Organizational control | Placed the account in a dedicated OU |
| Accountability | Assigned IT Operations as the owner |
| Access review | Established a quarterly review frequency |
| Least privilege | Confirmed no privileged group membership |
| Credential governance | Documented the lab password configuration and production risk |
| Identity lifecycle | Added the account to a service account inventory |
| Audit readiness | Captured configuration and validation evidence |
| Change protection | Created pre-lab and post-lab checkpoints |

---

## Validation Summary

| Test | Expected Result | Actual Result | Status |
|---|---|---|---|
| Pre-lab checkpoint created | Rollback point exists | Checkpoint created | Passed |
| Service Accounts OU reviewed | Dedicated OU exists | OU confirmed | Passed |
| Service account created | `svc-audit-review` exists | Account created | Passed |
| Correct OU placement | Account stored under Service Accounts | Placement confirmed | Passed |
| Naming standard followed | Account begins with `svc-` | Standard followed | Passed |
| Purpose documented | Description explains account use | Purpose documented | Passed |
| Owner documented | Responsible team identified | IT Operations documented | Passed |
| Review schedule documented | Review frequency recorded | Quarterly review documented | Passed |
| Logon name validated | Correct UPN and username configured | Logon name confirmed | Passed |
| Group membership reviewed | Membership visible | Domain Users only | Passed |
| Least privilege validated | No privileged groups assigned | No privileged membership found | Passed |
| Post-lab checkpoint created | Validated rollback point exists | Checkpoint created | Passed |

---

## Evidence Collected

| Evidence | Screenshot |
|---|---|
| Pre-lab checkpoint | `screenshots/lab-25-01-pre-lab-checkpoint.png` |
| Service account inventory | `screenshots/lab-25-02-service-account-inventory-view.png` |
| Service account description | `screenshots/lab-25-03-service-account-description.png` |
| Group membership validation | `screenshots/lab-25-04-service-account-group-membership.png` |
| Logon and password settings | `screenshots/lab-25-05-service-account-logon-settings.png` |
| Final account validation | `screenshots/lab-25-06-service-account-validation.png` |
| Post-lab checkpoint | `screenshots/lab-25-07-post-lab-checkpoint.png` |

---

## Troubleshooting Notes

No major technical errors occurred during this lab.

The main design consideration was distinguishing a lab-compatible service account configuration from a production-ready credential strategy.

The account was intentionally kept at the Domain Users privilege level. Any permissions required by a future task should be granted narrowly to the resource or operation rather than through broad administrative group membership.

---

## Security Concepts Reinforced

- Non-human identity governance
- Service account ownership
- Least privilege
- Group-based access control
- Credential governance
- Identity inventory
- Access reviews
- Account lifecycle management
- Audit evidence
- Administrative accountability
- Separation of standard and privileged identities

---

## Real-World Relevance

Service accounts are common in enterprise and government-regulated environments.

They may support:

- Windows services
- Scheduled tasks
- Application pools
- Database connections
- Backup systems
- Monitoring platforms
- Deployment tools
- Integration services
- Automation scripts

Because service accounts often run without direct user interaction, they can remain unnoticed for long periods.

IAM administrators must be able to identify:

- Why the account exists
- Who owns it
- What system uses it
- What permissions it has
- How its credential is protected
- When it was last reviewed
- Whether it is still required

This lab established the foundation for answering those questions.

---

## What I Would Do Differently in Production

In a production or government-regulated environment, I would implement:

- A formal service account request workflow
- Business and technical owners
- Manager or system-owner approval
- Group Managed Service Accounts where supported
- Automated password rotation
- Credential vault integration
- Logon restrictions
- Deny interactive logon controls
- Documented service dependencies
- Centralized activity monitoring
- Alerts for abnormal authentication
- Periodic entitlement reviews
- Service account usage reporting
- Change control for permission modifications
- Immediate disablement of unused accounts
- Formal account retirement procedures
- Centralized inventory and evidence retention

---

## Lessons Learned

- Service accounts should be treated as governed identities
- Non-human accounts require clear ownership
- Naming standards improve identification and reporting
- Dedicated OUs simplify management and review
- New service accounts should begin without privileged access
- Permissions should be granted only for the required function
- Password settings must be evaluated as security controls
- Lab continuity settings may not be appropriate for production
- Group Managed Service Accounts can reduce credential-management risk
- Service account inventories improve audit readiness
- Recurring reviews help identify unused or overprivileged accounts

---

## Skills Demonstrated

- Service account governance
- Active Directory account creation
- Dedicated OU management
- Non-human identity documentation
- Naming standard implementation
- Account description management
- Group membership validation
- Least-privilege validation
- Password setting review
- Service account inventory creation
- Audit evidence collection
- Hyper-V checkpoint management
- Production security risk analysis

---

## Outcome

Lab 25 successfully established a service account governance foundation for the MRTG Active Directory environment.

The lab demonstrated:

- A dedicated location for service accounts
- A consistent naming standard
- Creation of a governed non-human identity
- Ownership and review documentation
- Least-privilege validation
- Service account inventory tracking
- Audit-ready evidence
- Pre-lab and post-lab rollback points

The `svc-audit-review` account is now prepared for use in a controlled least-privilege automation scenario.

---

## Next Lab

[Lab 26 - Scheduled Task with Least-Privilege Service Account](../Lab-26-Scheduled-Task-with-Least-Privilege-Service-Account/)

Lab 26 will build on this governance foundation by using the service account in a controlled scheduled task while limiting its permissions to only what the task requires.
