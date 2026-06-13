# Lab 12 — Additional Domain Controller and AD Replication

![Platform](https://img.shields.io/badge/Platform-Windows%20Server%202022-blue)
![Technology](https://img.shields.io/badge/Technology-Active%20Directory-blue)
![Service](https://img.shields.io/badge/Service-DNS%20%26%20Global%20Catalog-lightgrey)
![Tooling](https://img.shields.io/badge/Tooling-REPADMIN%20%26%20DCDIAG-purple)
![Focus](https://img.shields.io/badge/Focus-Directory%20Resilience-orange)
![Validation](https://img.shields.io/badge/Validation-AD%20Replication-brightgreen)

---

## Objective

The objective of this lab is to add a second domain controller to the `mrtg.local` Active Directory domain.

This lab improves directory resilience by preparing `MRTG-DC02`, joining it to the existing domain, installing Active Directory Domain Services, promoting it as an additional domain controller, enabling DNS and Global Catalog services, and validating Active Directory replication health.

The focus is on domain controller redundancy, DNS registration, Global Catalog availability, replication validation, DNS redundancy, and operational rollback readiness.

---

## Business Problem

Monroe Redstone Technology Group needs to reduce dependency on a single domain controller.

A single-domain-controller environment creates an availability risk. If the only domain controller fails, authentication, DNS resolution, Group Policy processing, and directory-backed access can be disrupted.

This lab addresses the need to:

- Add a second domain controller
- Improve identity infrastructure resilience
- Enable DNS and Global Catalog services on the second domain controller
- Validate domain controller registration
- Validate DNS records for both domain controllers
- Validate Active Directory replication health
- Prove two-way object replication
- Configure DNS redundancy between domain controllers
- Preserve validated rollback points with Hyper-V checkpoints

---

## Lab Summary

In this lab, I prepared `MRTG-DC02` as an additional domain controller for the `mrtg.local` domain.

I configured the server with a static IP address, pointed DNS to the existing domain controller, validated connectivity to `MRTG-DC01`, joined `MRTG-DC02` to the domain, created a pre-AD DS checkpoint, installed Active Directory Domain Services, and promoted the server as an additional domain controller.

After promotion, I validated that both domain controllers appeared in Active Directory Users and Computers, Active Directory Sites and Services, DNS Manager, PowerShell, `repadmin`, and `dcdiag`.

I also proved two-way replication by creating an OU on `MRTG-DC01`, confirming it replicated to `MRTG-DC02`, creating a user on `MRTG-DC02`, and confirming it replicated back to `MRTG-DC01`.

Finally, I configured DNS redundancy between both domain controllers, validated replication again, and created final Hyper-V checkpoints.

---

## Environment

| Component | Details |
|---|---|
| Domain | `mrtg.local` |
| Primary Domain Controller | `MRTG-DC01` |
| Additional Domain Controller | `MRTG-DC02` |
| Client Workstation | `MRTG-CLIENT-01` |
| Hypervisor | Hyper-V |
| Network | `MRTG-Internal` |
| Operating System | Windows Server 2022 Standard Evaluation |
| Directory Service | Active Directory Domain Services |
| Supporting Services | DNS, Global Catalog |
| Lab Organization | Monroe Redstone Technology Group |

---

## Scope

### Included

- Second domain controller VM preparation
- Static IP and DNS configuration for `MRTG-DC02`
- Domain connectivity validation
- Domain join for `MRTG-DC02`
- Pre-AD DS Hyper-V checkpoint
- AD DS role installation
- Additional domain controller promotion
- DNS and Global Catalog configuration
- Replication source selection
- Prerequisite validation
- Domain controller registration validation
- DNS record validation
- Replication validation with `repadmin`
- Replication health validation with `dcdiag`
- Two-way object replication testing
- DNS redundancy configuration
- Final replication validation
- Final Hyper-V checkpoints

### Not Included

- Multi-site Active Directory topology
- Site link cost tuning
- Read-only domain controller deployment
- DHCP failover
- FSMO role transfer
- Domain controller backup automation
- Disaster recovery testing
- Azure or hybrid identity replication
- Production hardening baselines

---

## IP Addressing

| System | IP Address | Role |
|---|---:|---|
| `MRTG-DC01` | `192.168.10.10` | Existing domain controller, DNS, Global Catalog |
| `MRTG-DC02` | `192.168.10.11` | Additional domain controller, DNS, Global Catalog |
| `MRTG-CLIENT-01` | `192.168.10.101` | Domain client |

---

## Final DNS Client Configuration

### MRTG-DC01

| Setting | Value |
|---|---|
| Preferred DNS | `192.168.10.10` |
| Alternate DNS | `192.168.10.11` |

### MRTG-DC02

| Setting | Value |
|---|---|
| Preferred DNS | `192.168.10.11` |
| Alternate DNS | `192.168.10.10` |

This configuration allows each domain controller to use itself for DNS while retaining the other domain controller as a secondary resolver.

---

## Architecture

Before this lab, the environment used one domain controller.

```text
mrtg.local
└── MRTG-DC01
    ├── Active Directory Domain Services
    ├── DNS
    └── Global Catalog
```

After this lab, the environment includes two domain controllers.

```text
mrtg.local
├── MRTG-DC01
│   ├── Active Directory Domain Services
│   ├── DNS
│   └── Global Catalog
│
└── MRTG-DC02
    ├── Active Directory Domain Services
    ├── DNS
    └── Global Catalog
```

This improves identity infrastructure resilience by reducing dependency on a single domain controller.

---

## Directory Resilience Model

This lab supports the following resilience model:

| Component | Purpose |
|---|---|
| Additional Domain Controller | Provides another authentication and directory services endpoint |
| DNS on DC02 | Provides additional name resolution capability |
| Global Catalog on DC02 | Supports directory searches and logon operations |
| AD Replication | Keeps identity data synchronized between domain controllers |
| DNS Redundancy | Allows each DC to use the other as a secondary resolver |
| Hyper-V Checkpoints | Preserve validated rollback points |
| Replication Validation | Confirms directory synchronization health |

---

## Implementation and Validation

### 1. Second Domain Controller VM Prepared

`MRTG-DC02` was created in Hyper-V and connected to the same internal lab network as `MRTG-DC01`.

![Hyper-V showing DC01 and DC02 running](screenshots/lab-12-01-hyperv-dc01-dc02-running.png)

---

### 2. DC02 Renamed and Initial IP Configuration Reviewed

`MRTG-DC02` was renamed and reviewed in Server Manager.

The server showed the expected hostname and initial network configuration.

![DC02 renamed and IP configured](screenshots/lab-12-02-dc02-server-renamed-and-ip-configured.png)

---

### 3. DC02 Static IP and DNS Configured

`MRTG-DC02` was configured with a static IP address and pointed to `MRTG-DC01` for DNS before domain join.

Configured values:

| Setting | Value |
|---|---|
| IP Address | `192.168.10.11` |
| Subnet Mask | `255.255.255.0` |
| Preferred DNS Server | `192.168.10.10` |

![DC02 static IP and DNS configured](screenshots/lab-12-03-dc02-static-ip-dns-configured.png)

This allowed `MRTG-DC02` to locate the existing domain controller and domain DNS records.

---

### 4. DC02 Connectivity to DC01 Validated

Initial connectivity validation confirmed that `MRTG-DC02` could reach `MRTG-DC01`, resolve the domain, and locate a domain controller.

Commands used:

```cmd
ping 192.168.10.10
ping MRTG-DC01
nslookup mrtg.local
nltest /dsgetdc:mrtg.local
```

![DC02 connectivity to DC01 validated](screenshots/lab-12-04-dc02-connectivity-to-dc01-validated.png)

Validated result:

- `MRTG-DC02` could reach `192.168.10.10`
- `MRTG-DC02` could resolve `mrtg.local`
- `MRTG-DC02` could locate `MRTG-DC01` as a domain controller

---

### 5. DC02 Joined to the Domain

`MRTG-DC02` was joined to the existing `mrtg.local` domain.

![DC02 domain membership confirmed](screenshots/lab-12-05-dc02-domain-membership-confirmed.png)

This confirmed that `MRTG-DC02` was no longer only a workgroup server and was now a domain-joined server.

---

### 6. Pre-AD DS Checkpoint Created

Before installing Active Directory Domain Services, a Hyper-V checkpoint was created for rollback.

Checkpoint created:

```text
MRTG-DC02_Pre-ADDS-Install
```

![DC02 pre-ADDS checkpoint created](screenshots/lab-12-06-dc02-pre-adds-checkpoint-created.png)

This created a safe recovery point before promoting the server to a domain controller.

---

### 7. AD DS Role Selected on DC02

The Active Directory Domain Services role was selected on `MRTG-DC02`.

![AD DS role selected on DC02](screenshots/lab-12-07-adds-role-selected-on-dc02.png)

This prepared the server for domain controller promotion.

---

### 8. AD DS Role Installed on DC02

The AD DS role installed successfully.

Additional configuration was still required before the server could become a domain controller.

![AD DS role installed on DC02](screenshots/lab-12-08-adds-role-installed-on-dc02.png)

---

### 9. DC02 Promotion Started

`MRTG-DC02` was promoted using the option:

```text
Add a domain controller to an existing domain
```

Domain:

```text
mrtg.local
```

![Add domain controller to existing domain](screenshots/lab-12-09-add-domain-controller-to-existing-domain.png)

This ensured `MRTG-DC02` joined the existing forest and domain instead of creating a new forest.

---

### 10. DNS and Global Catalog Selected

`MRTG-DC02` was configured as a DNS server and Global Catalog.

Selected options:

```text
Domain Name System (DNS) server
Global Catalog (GC)
```

![DC02 DNS and Global Catalog selected](screenshots/lab-12-10-dc02-dns-global-catalog-selected.png)

This allows the second domain controller to support name resolution and directory search operations.

---

### 11. Replication Source Selected

The replication source was set to the existing domain controller:

```text
MRTG-DC01.mrtg.local
```

![DC02 replication source DC01](screenshots/lab-12-11-dc02-replication-source-dc01.png)

This confirmed that `MRTG-DC02` would receive initial Active Directory data from `MRTG-DC01`.

---

### 12. Prerequisite Check Passed

The prerequisite check completed successfully before promotion.

![DC02 prerequisite check passed](screenshots/lab-12-12-dc02-prerequisite-check-passed.png)

The expected DNS delegation warning was reviewed. In this isolated internal lab domain, no external parent DNS delegation was required.

---

### 13. DC02 Promoted and Rebooted

After installation, `MRTG-DC02` rebooted and showed AD DS and DNS roles in Server Manager.

![DC02 promoted and rebooted](screenshots/lab-12-13-dc02-promoted-and-rebooted.png)

This confirmed that `MRTG-DC02` was promoted and operating as a domain controller.

---

### 14. Domain Controllers Validated in ADUC

Active Directory Users and Computers confirmed that both domain controllers were present in the Domain Controllers OU.

Validated domain controllers:

```text
MRTG-DC01
MRTG-DC02
```

![Both domain controllers visible in ADUC](screenshots/lab-12-14-both-domain-controllers-visible-in-aduc.png)

---

### 15. Domain Controllers Validated in Sites and Services

Active Directory Sites and Services confirmed that both domain controllers were registered under the default site.

![Both DCs visible in Sites and Services](screenshots/lab-12-15-both-dcs-visible-in-sites-and-services.png)

This confirmed that both domain controllers were visible in the AD site topology.

---

### 16. DNS Records Validated

DNS Manager confirmed DNS records for both domain controllers.

Validated records included:

| Host | IP Address |
|---|---:|
| `mrtg-dc01` | `192.168.10.10` |
| `mrtg-dc02` | `192.168.10.11` |
| `CLIENT01` | `192.168.10.101` |

![DNS records for both domain controllers](screenshots/lab-12-16-dns-records-for-both-domain-controllers.png)

This confirmed that both domain controllers were registered in DNS.

---

### 17. Domain Controllers Validated with PowerShell

PowerShell confirmed that both domain controllers were discoverable and configured as Global Catalog servers.

Command used:

```powershell
Get-ADDomainController -Filter * | Select-Object HostName, Site, IPv4Address, IsGlobalCatalog
```

![Get-ADDomainController shows both DCs](screenshots/lab-12-17-get-addomaincontroller-shows-both-dcs.png)

Validated result:

| Domain Controller | IP Address | Global Catalog |
|---|---:|---|
| `MRTG-DC01.mrtg.local` | `192.168.10.10` | `True` |
| `MRTG-DC02.mrtg.local` | `192.168.10.11` | `True` |

---

### 18. Replication Summary Validated

Replication was validated using `repadmin`.

Command used:

```cmd
repadmin /replsummary
```

![Repadmin replication summary successful](screenshots/lab-12-18-repadmin-replsummary-successful.png)

The output showed zero replication failures for both domain controllers.

Validated result:

| Domain Controller | Replication Failures |
|---|---:|
| `MRTG-DC01` | `0` |
| `MRTG-DC02` | `0` |

This confirmed successful replication health between both domain controllers.

---

### 19. Detailed Replication Status Validated

Detailed replication status was validated using `repadmin /showrepl`.

Command used:

```cmd
repadmin /showrepl
```

![Repadmin showrepl successful](screenshots/lab-12-19-repadmin-showrepl-successful.png)

The output showed successful inbound replication from `MRTG-DC02` to `MRTG-DC01` across the major naming contexts.

Validated naming contexts included:

- `DC=mrtg,DC=local`
- `CN=Configuration,DC=mrtg,DC=local`
- `CN=Schema,CN=Configuration,DC=mrtg,DC=local`
- `DC=DomainDnsZones,DC=mrtg,DC=local`
- `DC=ForestDnsZones,DC=mrtg,DC=local`

---

### 20. Replication Health Checked with DCDIAG

Replication-specific domain controller diagnostics were checked with `dcdiag`.

Command used:

```cmd
dcdiag /test:replications /q
```

![DCDIAG replication health check](screenshots/lab-12-20-dcdiag-replication-health-check.png)

No output was returned after running the command, which indicates no replication errors were detected by the replication-specific diagnostic test.

---

### 21. Test OU Created on DC01

To prove replication beyond command output, a test OU was created on `MRTG-DC01`.

Test OU:

```text
Lab12-Replication-Test
```

![Test OU created on DC01](screenshots/lab-12-21-test-ou-created-on-dc01.png)

This created a visible directory object for replication testing.

---

### 22. Test OU Replicated to DC02

The same OU appeared on `MRTG-DC02`, proving replication from `MRTG-DC01` to `MRTG-DC02`.

![Test OU replicated to DC02](screenshots/lab-12-22-test-ou-replicated-to-dc02.png)

This validated object replication from the original domain controller to the new domain controller.

---

### 23. Test User Created on DC02

A test user was created inside the replicated OU on `MRTG-DC02`.

Test user:

```text
Replication Test
```

![Test user created on DC02](screenshots/lab-12-23-test-user-created-on-dc02.png)

This created a second object from the new domain controller to validate replication in the opposite direction.

---

### 24. Test User Replicated to DC01

The test user appeared on `MRTG-DC01`, proving replication from `MRTG-DC02` back to `MRTG-DC01`.

![Test user replicated to DC01](screenshots/lab-12-24-test-user-replicated-to-dc01.png)

This confirmed two-way Active Directory object replication between both domain controllers.

---

### 25. DC01 DNS Redundancy Configured

After `MRTG-DC02` was promoted and DNS was installed, DNS client settings were updated on `MRTG-DC01`.

Configured DNS settings:

| Setting | Value |
|---|---|
| Preferred DNS Server | `192.168.10.10` |
| Alternate DNS Server | `192.168.10.11` |

![DC01 DNS redundancy configured](screenshots/lab-12-25-dc01-dns-redundancy-configured.png)

This allows `MRTG-DC01` to use itself first and `MRTG-DC02` as a secondary DNS resolver.

---

### 26. DC02 DNS Redundancy Configured

DNS client settings were updated on `MRTG-DC02`.

Configured DNS settings:

| Setting | Value |
|---|---|
| Preferred DNS Server | `192.168.10.11` |
| Alternate DNS Server | `192.168.10.10` |

![DC02 DNS redundancy configured](screenshots/lab-12-26-dc02-dns-redundancy-configured.png)

This allows `MRTG-DC02` to use itself first and `MRTG-DC01` as a secondary DNS resolver.

---

### 27. Final Replication Health Validated

Final replication validation was performed after configuring DNS redundancy.

Commands used:

```cmd
repadmin /replsummary
dcdiag /test:replications /q
```

![Final replication health validation](screenshots/lab-12-27-final-replication-health-validation.png)

The replication summary showed zero failures, and the replication diagnostic test returned no errors.

Validated result:

| Validation | Result |
|---|---|
| `repadmin /replsummary` | Zero failures |
| `dcdiag /test:replications /q` | No replication errors returned |

---

### 28. DC02 Final Checkpoint Created

A final checkpoint was created for `MRTG-DC02` after successful replication validation.

Checkpoint created:

```text
MRTG-DC02_Post-Lab12_AD-Replication-Validated
```

![DC02 final Lab 12 checkpoint created](screenshots/lab-12-28-dc02-final-lab12-checkpoint-created.png)

This preserved the validated state of the new domain controller.

---

### 29. DC01 Final Checkpoint Created

A final checkpoint was created for `MRTG-DC01` after successful replication validation.

Checkpoint created:

```text
MRTG-DC01_Post-Lab12_AD-Replication-Validated
```

![DC01 final Lab 12 checkpoint created](screenshots/lab-12-29-dc01-final-lab12-checkpoint-created.png)

This preserved the validated state of the original domain controller after replication and DNS redundancy were confirmed.

---

## Troubleshooting Note

During replication validation, replication health was tested with multiple tools rather than relying on a single console view.

The following tools were used:

```cmd
repadmin /replsummary
repadmin /showrepl
dcdiag /test:replications /q
```

This reinforced that Active Directory replication should be validated from multiple angles:

- Replication summary
- Detailed replication partner status
- Replication-specific diagnostic testing
- Object replication between domain controllers
- DNS record visibility

The key lesson is that DNS and replication are tightly connected in Active Directory. A second domain controller is only useful when DNS, Global Catalog, and replication health are all validated.

---

## Security Perspective

Adding a second domain controller improves identity infrastructure resilience.

Active Directory is a core dependency for authentication, authorization, Group Policy, DNS, and access to domain resources.

From a security and IAM perspective, this lab supports:

- Directory service redundancy
- Improved authentication availability
- Global Catalog availability
- DNS redundancy
- Replication health validation
- Two-way identity object replication
- Reduced single-domain-controller dependency
- Operational rollback through checkpoints
- Resilient identity infrastructure

A single domain controller creates a major availability risk. If the only domain controller fails, users may lose access to authentication services, name resolution, policy processing, and directory-backed resources.

---

## Risk Addressed

Without a second domain controller, the environment depends entirely on one identity system.

This lab reduces the risk of:

- Single domain controller dependency
- Authentication service outage
- DNS service outage
- Group Policy processing disruption
- Directory-backed resource access disruption
- No replication partner for identity data
- Reduced operational resilience
- Weak rollback readiness after infrastructure changes
- Poor visibility into replication health

---

## Control Mapping

| Control Area | How This Lab Supports It |
|---|---|
| Directory resilience | Adds a second domain controller |
| Authentication availability | Provides another DC for domain services |
| DNS resilience | Configures DNS on both domain controllers |
| Global Catalog availability | Enables Global Catalog on `MRTG-DC02` |
| Replication validation | Uses `repadmin` and `dcdiag` |
| Identity data consistency | Proves OU and user replication both directions |
| Operational resilience | Creates checkpoints before and after validated configuration |
| Troubleshooting readiness | Validates DNS, connectivity, DC discovery, and replication health |

---

## Validation

The following validation checks were completed:

| Validation Item | Result |
|---|---|
| `MRTG-DC02` VM prepared | Passed |
| `MRTG-DC02` renamed | Passed |
| `MRTG-DC02` static IP configured | Passed |
| `MRTG-DC02` DNS pointed to `MRTG-DC01` before promotion | Passed |
| Connectivity to `MRTG-DC01` validated | Passed |
| `mrtg.local` DNS resolution validated | Passed |
| Domain controller discovery validated with `nltest` | Passed |
| `MRTG-DC02` joined to `mrtg.local` | Passed |
| Pre-AD DS checkpoint created | Passed |
| AD DS role installed | Passed |
| `MRTG-DC02` promoted as additional domain controller | Passed |
| DNS role selected for `MRTG-DC02` | Passed |
| Global Catalog enabled on `MRTG-DC02` | Passed |
| Replication source set to `MRTG-DC01` | Passed |
| Prerequisite checks passed | Passed |
| Both DCs visible in ADUC | Passed |
| Both DCs visible in Sites and Services | Passed |
| DNS records present for both DCs | Passed |
| `Get-ADDomainController` showed both DCs | Passed |
| `repadmin /replsummary` showed zero failures | Passed |
| `repadmin /showrepl` showed successful replication | Passed |
| `dcdiag /test:replications /q` returned no errors | Passed |
| Test OU replicated from `MRTG-DC01` to `MRTG-DC02` | Passed |
| Test user replicated from `MRTG-DC02` to `MRTG-DC01` | Passed |
| DNS redundancy configured on `MRTG-DC01` | Passed |
| DNS redundancy configured on `MRTG-DC02` | Passed |
| Final replication validation passed | Passed |
| Final checkpoint created for `MRTG-DC02` | Passed |
| Final checkpoint created for `MRTG-DC01` | Passed |

---

## Evidence Collected

The following evidence was collected during the lab:

| Evidence | File |
|---|---|
| Hyper-V showing DC01 and DC02 running | `screenshots/lab-12-01-hyperv-dc01-dc02-running.png` |
| DC02 renamed and IP configured | `screenshots/lab-12-02-dc02-server-renamed-and-ip-configured.png` |
| DC02 static IP and DNS configured | `screenshots/lab-12-03-dc02-static-ip-dns-configured.png` |
| DC02 connectivity to DC01 validated | `screenshots/lab-12-04-dc02-connectivity-to-dc01-validated.png` |
| DC02 domain membership confirmed | `screenshots/lab-12-05-dc02-domain-membership-confirmed.png` |
| DC02 pre-ADDS checkpoint created | `screenshots/lab-12-06-dc02-pre-adds-checkpoint-created.png` |
| AD DS role selected on DC02 | `screenshots/lab-12-07-adds-role-selected-on-dc02.png` |
| AD DS role installed on DC02 | `screenshots/lab-12-08-adds-role-installed-on-dc02.png` |
| Add domain controller to existing domain | `screenshots/lab-12-09-add-domain-controller-to-existing-domain.png` |
| DC02 DNS and Global Catalog selected | `screenshots/lab-12-10-dc02-dns-global-catalog-selected.png` |
| DC02 replication source DC01 | `screenshots/lab-12-11-dc02-replication-source-dc01.png` |
| DC02 prerequisite check passed | `screenshots/lab-12-12-dc02-prerequisite-check-passed.png` |
| DC02 promoted and rebooted | `screenshots/lab-12-13-dc02-promoted-and-rebooted.png` |
| Both domain controllers visible in ADUC | `screenshots/lab-12-14-both-domain-controllers-visible-in-aduc.png` |
| Both DCs visible in Sites and Services | `screenshots/lab-12-15-both-dcs-visible-in-sites-and-services.png` |
| DNS records for both domain controllers | `screenshots/lab-12-16-dns-records-for-both-domain-controllers.png` |
| Get-ADDomainController shows both DCs | `screenshots/lab-12-17-get-addomaincontroller-shows-both-dcs.png` |
| Repadmin replication summary successful | `screenshots/lab-12-18-repadmin-replsummary-successful.png` |
| Repadmin showrepl successful | `screenshots/lab-12-19-repadmin-showrepl-successful.png` |
| DCDIAG replication health check | `screenshots/lab-12-20-dcdiag-replication-health-check.png` |
| Test OU created on DC01 | `screenshots/lab-12-21-test-ou-created-on-dc01.png` |
| Test OU replicated to DC02 | `screenshots/lab-12-22-test-ou-replicated-to-dc02.png` |
| Test user created on DC02 | `screenshots/lab-12-23-test-user-created-on-dc02.png` |
| Test user replicated to DC01 | `screenshots/lab-12-24-test-user-replicated-to-dc01.png` |
| DC01 DNS redundancy configured | `screenshots/lab-12-25-dc01-dns-redundancy-configured.png` |
| DC02 DNS redundancy configured | `screenshots/lab-12-26-dc02-dns-redundancy-configured.png` |
| Final replication health validation | `screenshots/lab-12-27-final-replication-health-validation.png` |
| DC02 final Lab 12 checkpoint created | `screenshots/lab-12-28-dc02-final-lab12-checkpoint-created.png` |
| DC01 final Lab 12 checkpoint created | `screenshots/lab-12-29-dc01-final-lab12-checkpoint-created.png` |

---

## What I Would Improve in Production

In a production environment, I would improve this process by:

- Using a formal domain controller deployment checklist
- Placing domain controllers on separate hosts
- Using separate physical or logical locations where possible
- Documenting FSMO role ownership
- Validating backup and restore procedures for both domain controllers
- Monitoring AD replication continuously
- Configuring alerting for replication failures
- Reviewing DNS zone health regularly
- Avoiding unnecessary services on domain controllers
- Applying domain controller hardening baselines
- Documenting site topology and subnet mappings
- Testing domain controller failure scenarios
- Using formal change management for domain controller promotion

---

## Lessons Learned

This lab reinforced that adding a second domain controller is not just a role installation task.

The server must be prepared with the correct network settings, joined to the domain, promoted correctly, registered in DNS, visible in Active Directory management tools, and validated for replication health.

The biggest takeaway is that directory resilience must be proven, not assumed.

A second domain controller only adds value if authentication, DNS, Global Catalog functionality, object replication, and replication health are validated.

---

## Outcome

Lab 12 successfully added `MRTG-DC02` as an additional domain controller for the `mrtg.local` domain.

The lab confirmed:

- `MRTG-DC02` was prepared with static IP and DNS settings
- `MRTG-DC02` joined the existing domain
- AD DS was installed
- `MRTG-DC02` was promoted as an additional domain controller
- DNS and Global Catalog were enabled
- Both domain controllers were visible in ADUC
- Both domain controllers were visible in Sites and Services
- DNS records existed for both domain controllers
- PowerShell confirmed both DCs as Global Catalog servers
- `repadmin /replsummary` confirmed zero replication failures
- `repadmin /showrepl` confirmed successful inbound replication
- `dcdiag /test:replications /q` returned no replication errors
- A test OU replicated from `MRTG-DC01` to `MRTG-DC02`
- A test user replicated from `MRTG-DC02` to `MRTG-DC01`
- DNS redundancy was configured on both domain controllers
- Final replication validation passed
- Final checkpoints were created for both domain controllers

The environment now supports improved identity infrastructure resilience with two healthy domain controllers.

---

## Next Lab

[Lab 13 — Centralized Logging and Event Forwarding for Identity Events](../Lab-13-Centralized-Logging-and-Event-Forwarding-for-Identity-Events/)

Lab 13 will build on this directory resilience foundation by centralizing identity-related Windows security events and improving visibility into authentication, directory, and account lifecycle activity across the MRTG environment.
