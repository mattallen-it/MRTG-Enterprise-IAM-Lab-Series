# Lab-12 — Additional Domain Controller and AD Replication

![Active Directory](https://img.shields.io/badge/Active%20Directory-Domain%20Services-blue)
![Windows Server](https://img.shields.io/badge/Windows%20Server-2022-blue)
![DNS](https://img.shields.io/badge/DNS-Redundancy-blue)
![Replication](https://img.shields.io/badge/AD%20Replication-Validated-brightgreen)
![Hyper--V](https://img.shields.io/badge/Hyper--V-Virtualization-lightgrey)
![IAM](https://img.shields.io/badge/IAM-Directory%20Resilience-purple)

## Lab Overview

This lab adds a second domain controller, `MRTG-DC02`, to the existing `mrtg.local` Active Directory domain.

The purpose of this lab is to improve directory resilience by adding another domain controller, enabling DNS and Global Catalog services, validating Active Directory replication, and proving that identity objects replicate between both domain controllers.

This lab builds on the previous DHCP and identity infrastructure work by moving the environment from a single-domain-controller design toward a more resilient enterprise identity architecture.

---

## Objectives

- Prepare a second Windows Server virtual machine for domain controller promotion
- Configure static IP and DNS settings for `MRTG-DC02`
- Join `MRTG-DC02` to the existing `mrtg.local` domain
- Install the Active Directory Domain Services role
- Promote `MRTG-DC02` as an additional domain controller
- Enable DNS and Global Catalog services on the second domain controller
- Validate domain controller registration in Active Directory
- Validate DNS records for both domain controllers
- Validate Active Directory replication using `repadmin`
- Validate replication health using `dcdiag`
- Prove two-way object replication between `MRTG-DC01` and `MRTG-DC02`
- Configure DNS redundancy between both domain controllers
- Create final Hyper-V checkpoints for rollback and operational resilience

---

## Environment

| Component | Value |
|---|---|
| Hypervisor | Hyper-V |
| Domain | `mrtg.local` |
| Primary Domain Controller | `MRTG-DC01` |
| Additional Domain Controller | `MRTG-DC02` |
| Client Workstation | `MRTG-CLIENT-01` |
| Operating System | Windows Server 2022 Standard Evaluation |
| Network | `MRTG-Internal` |

---

## IP Addressing

| System | IP Address | Role |
|---|---:|---|
| `MRTG-DC01` | `192.168.10.10` | Existing domain controller / DNS / Global Catalog |
| `MRTG-DC02` | `192.168.10.11` | Additional domain controller / DNS / Global Catalog |
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

This configuration allows each domain controller to use itself for DNS while retaining the other domain controller as a secondary DNS resolver.

---

## Architecture

Before this lab, the environment used one domain controller:

```text
MRTG-DC01
├── Active Directory Domain Services
├── DNS
└── Global Catalog
```

After this lab, the environment includes two domain controllers:

```text
mrtg.local
│
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

## Phase 1 — Prepare the Second Domain Controller VM

`MRTG-DC02` was created in Hyper-V and connected to the same internal lab network as `MRTG-DC01`.

![Hyper-V showing DC01 and DC02 running](images/01-hyperv-dc01-dc02-running.png)

`MRTG-DC02` was renamed and configured with the correct IP address.

![DC02 renamed and IP configured](images/02-dc02-server-renamed-and-ip-configured.png)

The server was configured with a static IP address and pointed to `MRTG-DC01` for DNS before domain join.

![DC02 static IP and DNS configured](images/03-dc02-static-ip-dns-configured.png)

Initial validation confirmed that `MRTG-DC02` could reach `MRTG-DC01`, resolve the domain, and locate a domain controller.

Commands used:

```cmd
ping 192.168.10.10
ping MRTG-DC01
nslookup mrtg.local
nltest /dsgetdc:mrtg.local
```

![DC02 connectivity to DC01 validated](images/04-dc02-connectivity-to-dc01-validated.png)

---

## Phase 2 — Join DC02 to the Domain

`MRTG-DC02` was joined to the existing `mrtg.local` domain.

![DC02 domain membership confirmed](images/05-dc02-domain-membership-confirmed.png)

Before installing Active Directory Domain Services, a Hyper-V checkpoint was created for rollback.

Checkpoint:

```text
MRTG-DC02_Pre-ADDS-Install
```

![DC02 pre-ADDS checkpoint created](images/06-dc02-pre-adds-checkpoint-created.png)

---

## Phase 3 — Install Active Directory Domain Services

The Active Directory Domain Services role was selected on `MRTG-DC02`.

![AD DS role selected on DC02](images/07-adds-role-selected-on-dc02.png)

The AD DS role installed successfully, but additional configuration was required before the server could become a domain controller.

![AD DS role installed on DC02](images/08-adds-role-installed-on-dc02.png)

---

## Phase 4 — Promote DC02 as an Additional Domain Controller

`MRTG-DC02` was promoted using the option:

```text
Add a domain controller to an existing domain
```

Domain:

```text
mrtg.local
```

![Add domain controller to existing domain](images/09-add-domain-controller-to-existing-domain.png)

The server was configured as a DNS server and Global Catalog.

![DC02 DNS and Global Catalog selected](images/10-dc02-dns-global-catalog-selected.png)

The replication source was set to the existing domain controller:

```text
MRTG-DC01.mrtg.local
```

![DC02 replication source DC01](images/11-dc02-replication-source-dc01.png)

The prerequisite check completed successfully.

![DC02 prerequisite check passed](images/12-dc02-prerequisite-check-passed.png)

After installation, `MRTG-DC02` rebooted and showed AD DS and DNS roles in Server Manager.

![DC02 promoted and rebooted](images/13-dc02-promoted-and-rebooted.png)

---

## Phase 5 — Validate Domain Controller Registration

Active Directory Users and Computers confirmed that both domain controllers were present in the **Domain Controllers** OU.

![Both domain controllers visible in ADUC](images/14-both-domain-controllers-visible-in-aduc.png)

Active Directory Sites and Services confirmed that both domain controllers were registered under the default site.

![Both DCs visible in Sites and Services](images/15-both-dcs-visible-in-sites-and-services.png)

DNS Manager confirmed DNS records for both domain controllers.

![DNS records for both domain controllers](images/16-dns-records-for-both-domain-controllers.png)

PowerShell confirmed that both domain controllers were discoverable and configured as Global Catalog servers.

Command used:

```powershell
Get-ADDomainController -Filter * | Select-Object HostName, Site, IPv4Address, IsGlobalCatalog
```

![Get-ADDomainController shows both DCs](images/17-get-addomaincontroller-shows-both-dcs.png)

---

## Phase 6 — Validate Active Directory Replication

Replication was validated using `repadmin`.

Command used:

```cmd
repadmin /replsummary
```

The final output showed zero replication failures for both domain controllers.

![Repadmin replication summary successful](images/18-repadmin-replsummary-successful.png)

Detailed replication status was validated with:

```cmd
repadmin /showrepl
```

The output showed successful inbound replication across the major naming contexts.

![Repadmin showrepl successful](images/19-repadmin-showrepl-successful.png)

Replication-specific domain controller diagnostics were checked with:

```cmd
dcdiag /test:replications /q
```

No output was returned, which indicates no replication errors were detected by the replication-specific diagnostic test.

![DCDIAG replication health check](images/20-dcdiag-replication-health-check.png)

---

## Phase 7 — Prove Two-Way Object Replication

To prove replication beyond command output, a test OU was created on `MRTG-DC01`.

Test OU:

```text
Lab12-Replication-Test
```

![Test OU created on DC01](images/21-test-ou-created-on-dc01.png)

The same OU appeared on `MRTG-DC02`, proving replication from `DC01` to `DC02`.

![Test OU replicated to DC02](images/22-test-ou-replicated-to-dc02.png)

A test user was then created on `MRTG-DC02`.

Test user:

```text
Replication Test
```

![Test user created on DC02](images/23-test-user-created-on-dc02.png)

The test user appeared on `MRTG-DC01`, proving replication from `DC02` back to `DC01`.

![Test user replicated to DC01](images/24-test-user-replicated-to-dc01.png)

This confirmed two-way Active Directory object replication between both domain controllers.

---

## Phase 8 — Configure DNS Redundancy

After `MRTG-DC02` was promoted and DNS was installed, DNS client settings were updated so each domain controller could use itself first and the other domain controller second.

`MRTG-DC01` DNS settings:

```text
Preferred DNS: 192.168.10.10
Alternate DNS: 192.168.10.11
```

![DC01 DNS redundancy configured](images/25a-dc01-dns-redundancy-configured.png)

`MRTG-DC02` DNS settings:

```text
Preferred DNS: 192.168.10.11
Alternate DNS: 192.168.10.10
```

![DC02 DNS redundancy configured](images/25b-dc02-dns-redundancy-configured.png)

Final replication validation was performed after updating DNS settings.

Commands used:

```cmd
repadmin /replsummary
dcdiag /test:replications /q
```

The replication summary showed zero failures, and the replication diagnostic test returned no errors.

![Final replication health validation](images/26-final-replication-health-validation.png)

---

## Phase 9 — Final Hyper-V Checkpoints

Final checkpoints were created for both domain controllers after successful replication validation.

`MRTG-DC02` final checkpoint:

```text
MRTG-DC02_Post-Lab12_AD-Replication-Validated
```

![DC02 final Lab 12 checkpoint created](images/27a-dc02-final-lab12-checkpoint-created.png)

`MRTG-DC01` final checkpoint:

```text
MRTG-DC01_Post-Lab12_AD-Replication-Validated
```

![DC01 final Lab 12 checkpoint created](images/27b-dc01-final-lab12-checkpoint-created.png)

---

## Troubleshooting Note

During replication validation, the initial `repadmin /replsummary` output returned DNS lookup failures:

```text
8524 - The DSA operation is unable to proceed because of a DNS lookup failure.
```

The issue was resolved by validating AD DNS records, confirming domain controller GUID resolution under `_msdcs.mrtg.local`, forcing KCC recalculation, and re-running replication synchronization.

Commands used during troubleshooting included:

```cmd
nslookup <DC-GUID>._msdcs.mrtg.local
nslookup -type=SRV _ldap._tcp.dc._msdcs.mrtg.local
repadmin /kcc
repadmin /syncall /AdeP
repadmin /replsummary
```

After correction, replication showed zero failures between `MRTG-DC01` and `MRTG-DC02`.

This reinforced the importance of DNS health in Active Directory replication. Active Directory depends heavily on DNS records for domain controller discovery, authentication, and replication partner location.

---

## Validation Summary

| Validation Check | Result |
|---|---|
| DC02 static IP configured | Passed |
| DC02 DNS pointed to DC01 before promotion | Passed |
| DC02 joined to `mrtg.local` | Passed |
| AD DS role installed | Passed |
| DC02 promoted as additional domain controller | Passed |
| DNS role installed on DC02 | Passed |
| Global Catalog enabled on DC02 | Passed |
| Both DCs visible in ADUC | Passed |
| Both DCs visible in Sites and Services | Passed |
| DNS records present for both DCs | Passed |
| `Get-ADDomainController` shows both DCs | Passed |
| `repadmin /replsummary` shows zero failures | Passed |
| `repadmin /showrepl` shows successful replication | Passed |
| `dcdiag /test:replications /q` returns no errors | Passed |
| OU replicated from DC01 to DC02 | Passed |
| User replicated from DC02 to DC01 | Passed |
| DNS redundancy configured | Passed |
| Final checkpoints created | Passed |

---

## Security and IAM Relevance

Adding a second domain controller improves identity infrastructure resilience. In an enterprise environment, Active Directory is a core dependency for authentication, authorization, Group Policy, DNS, and access to domain resources.

A single domain controller creates a major availability risk. If the only domain controller fails, users may lose access to authentication services, name resolution, policy processing, and directory-backed resources.

This lab demonstrates several IAM and security-relevant concepts:

- Directory service redundancy
- Domain controller promotion
- DNS dependency in Active Directory
- Global Catalog availability
- Replication health validation
- Operational rollback through checkpoints
- Two-way identity object replication
- Resilient authentication infrastructure

For government, defense, and regulated IT environments, identity availability and recovery are critical. This lab shows the foundation for building more reliable Active Directory infrastructure.

---

## Outcome

Lab-12 added `MRTG-DC02` as an additional domain controller for the `mrtg.local` domain, configured DNS and Global Catalog services, validated Active Directory replication, and confirmed two-way object replication between `MRTG-DC01` and `MRTG-DC02`.

The lab also included DNS redundancy configuration and final Hyper-V checkpoints for operational rollback.

Final state:

```text
MRTG-DC01
├── Domain Controller
├── DNS Server
├── Global Catalog
└── IP: 192.168.10.10

MRTG-DC02
├── Domain Controller
├── DNS Server
├── Global Catalog
└── IP: 192.168.10.11
```

Active Directory replication is healthy between both domain controllers.

---

## Next Lab

**Lab-13 — Centralized Logging and Event Forwarding for Identity Events**

Lab-13 will build on this directory resilience foundation by centralizing identity-related Windows security events and improving visibility into authentication, directory, and account lifecycle activity across the MRTG environment.
