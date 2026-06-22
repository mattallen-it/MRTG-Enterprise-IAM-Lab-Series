# Lab 12: Additional Domain Controller and AD Replication

![Platform](https://img.shields.io/badge/Platform-Windows%20Server%202022-blue)
![Technology](https://img.shields.io/badge/Technology-Active%20Directory-blue)
![Service](https://img.shields.io/badge/Service-DNS%20%26%20Global%20Catalog-lightgrey)
![Tooling](https://img.shields.io/badge/Tooling-REPADMIN%20%26%20DCDIAG-purple)
![Focus](https://img.shields.io/badge/Focus-Directory%20Resilience-orange)
![Validation](https://img.shields.io/badge/Validation-AD%20Replication-brightgreen)

---

## Objective

Add a second domain controller to the `mrtg.local` Active Directory domain and validate directory replication.

This lab prepares `MRTG-DC02`, joins it to the existing domain, installs Active Directory Domain Services, promotes it as an additional domain controller, enables DNS and Global Catalog services, and validates replication health.

---

## Business Scenario

Monroe Redstone Technology Group needs to reduce its dependency on a single domain controller.

If the only domain controller becomes unavailable, authentication, DNS resolution, Group Policy processing, and directory-backed access may be disrupted.

This lab addresses the need to:

- Add a second domain controller
- Provide another authentication and directory-service endpoint
- Add DNS and Global Catalog capability
- Validate domain controller registration
- Confirm Active Directory replication health
- Demonstrate object replication in both directions
- Configure redundant DNS client settings
- Document the limits of same-host lab redundancy

---

## Lab Summary

In this lab, I prepared `MRTG-DC02` as an additional domain controller for `mrtg.local`.

The server received a static IP address, used `MRTG-DC01` for DNS during preparation, joined the existing domain, installed AD DS, and was promoted as an additional domain controller with DNS and Global Catalog enabled.

After promotion, both domain controllers were validated through Active Directory Users and Computers, Active Directory Sites and Services, DNS Manager, PowerShell, `repadmin`, and `dcdiag`.

Multi-master replication was demonstrated by creating an OU on `MRTG-DC01` and confirming it on `MRTG-DC02`, then creating a user on `MRTG-DC02` and confirming it on `MRTG-DC01`.

DNS client redundancy was configured, replication health was revalidated, and temporary Hyper-V lab checkpoints were created after validation.

---

## Environment

| Component | Details |
|---|---|
| Domain | `mrtg.local` |
| Original Domain Controller | `MRTG-DC01` |
| Additional Domain Controller | `MRTG-DC02` |
| Client Windows Computer Name | `CLIENT01` |
| Client Hyper-V VM Name | `MRTG-CLIENT-01` |
| Hypervisor | Hyper-V |
| Virtual Network | `MRTG-Internal` |
| Server Operating System | Windows Server 2022 Standard Evaluation |
| Directory Service | Active Directory Domain Services |
| Supporting Services | DNS and Global Catalog |
| Organization | Monroe Redstone Technology Group |

---

## Prerequisites

- Operational `mrtg.local` domain
- Healthy `MRTG-DC01`
- Active Directory-integrated DNS
- Static address `192.168.10.10` on `MRTG-DC01`
- Windows Server 2022 installed on `MRTG-DC02`
- Network connectivity between both servers
- Administrative credentials for domain join and promotion
- Validated time synchronization
- Supported backup and recovery plan for production use

---

## Scope

### Included

- `MRTG-DC02` preparation
- Static IP and DNS configuration
- Domain connectivity validation
- Domain join
- Temporary pre-change Hyper-V checkpoint
- AD DS role installation
- Additional domain controller promotion
- DNS and Global Catalog configuration
- Initial replication-source selection
- Prerequisite validation
- Domain controller registration validation
- DNS record validation
- Replication validation with `repadmin`
- Replication diagnostics with `dcdiag`
- Multi-master object replication testing
- DNS client redundancy configuration
- Final replication validation
- Temporary post-change lab checkpoints

### Not Included

- Physical host redundancy
- Multi-site Active Directory topology
- Site-link cost or schedule tuning
- Read-only domain controller deployment
- DHCP failover
- FSMO role transfer
- Automated domain controller backup
- Disaster recovery testing
- Hybrid identity
- Production hardening baselines

---

## IP Addressing

| System | IP Address | Role |
|---|---:|---|
| `MRTG-DC01` | `192.168.10.10` | Original domain controller, DNS, and Global Catalog |
| `MRTG-DC02` | `192.168.10.11` | Additional domain controller, DNS, and Global Catalog |
| `CLIENT01` | `192.168.10.101` | Domain-joined client |

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

This configuration allows each domain controller to use its local DNS service first and the other domain controller as an alternate resolver.

In production, DNS client order should follow the organization's Active Directory and DNS design. Many environments configure each domain controller to prefer another healthy internal DNS server and use itself as the alternate.

---

## Architecture

### Before Lab 12

```text
mrtg.local
`-- MRTG-DC01
    |-- Active Directory Domain Services
    |-- DNS
    `-- Global Catalog
```

### After Lab 12

```text
mrtg.local
|-- MRTG-DC01
|   |-- Active Directory Domain Services
|   |-- DNS
|   `-- Global Catalog
|
`-- MRTG-DC02
    |-- Active Directory Domain Services
    |-- DNS
    `-- Global Catalog
```

The second domain controller provides logical directory and DNS redundancy.

Both virtual domain controllers remain on the same physical Hyper-V host, so the lab does not provide protection from a complete host failure.

---

## Directory Resilience Model

| Component | Purpose |
|---|---|
| Additional Domain Controller | Provides another authentication and directory-service endpoint |
| DNS on `MRTG-DC02` | Provides an additional internal DNS service |
| Global Catalog on `MRTG-DC02` | Supports directory searches and sign-in requirements |
| Active Directory Replication | Synchronizes directory partitions between domain controllers |
| DNS Client Redundancy | Provides an alternate internal DNS resolver |
| Replication Validation | Confirms synchronization health |
| Object Replication Testing | Demonstrates multi-master directory behavior |
| Hyper-V Checkpoints | Provide temporary recovery points for this controlled lab only |

Hyper-V checkpoints are not a replacement for supported System State backups or Active Directory recovery procedures.

---

## Implementation and Validation

### 1. Prepared the Second Domain Controller VM

`MRTG-DC02` was created in Hyper-V and connected to `MRTG-Internal`.

![Hyper-V showing DC01 and DC02 running](screenshots/lab-12-01-hyperv-dc01-dc02-running.png)

This placed both servers on the same isolated lab network.

---

### 2. Renamed MRTG-DC02

The Windows Server computer name was configured as:

```text
MRTG-DC02
```

![DC02 renamed and IP configured](screenshots/lab-12-02-dc02-server-renamed-and-ip-configured.png)

The server identity was confirmed before domain join and promotion.

---

### 3. Configured Static IP and DNS

`MRTG-DC02` received the following initial network configuration:

| Setting | Value |
|---|---|
| IPv4 Address | `192.168.10.11` |
| Subnet Mask | `255.255.255.0` |
| Preferred DNS | `192.168.10.10` |

![DC02 static IP and DNS configured](screenshots/lab-12-03-dc02-static-ip-dns-configured.png)

Using `MRTG-DC01` as DNS allowed the new server to locate the existing domain before promotion.

---

### 4. Validated Connectivity and Domain Discovery

Commands used:

```cmd
ping 192.168.10.10
ping MRTG-DC01
nslookup mrtg.local
nltest /dsgetdc:mrtg.local
```

![DC02 connectivity to DC01 validated](screenshots/lab-12-04-dc02-connectivity-to-dc01-validated.png)

The results confirmed:

- IP connectivity to `MRTG-DC01`
- Name resolution for the existing domain
- Discovery of an existing domain controller

---

### 5. Joined MRTG-DC02 to the Domain

`MRTG-DC02` was joined to:

```text
mrtg.local
```

![DC02 domain membership confirmed](screenshots/lab-12-05-dc02-domain-membership-confirmed.png)

The server was now a domain member but was not yet a domain controller.

---

### 6. Created a Pre-AD DS Lab Checkpoint

Checkpoint name:

```text
MRTG-DC02_Pre-ADDS-Install
```

![DC02 pre-ADDS checkpoint created](screenshots/lab-12-06-dc02-pre-adds-checkpoint-created.png)

This created a temporary lab recovery point before installing AD DS.

---

### 7. Selected the AD DS Role

The Active Directory Domain Services role was selected on `MRTG-DC02`.

![AD DS role selected on DC02](screenshots/lab-12-07-adds-role-selected-on-dc02.png)

---

### 8. Installed the AD DS Role

The role installation completed successfully.

![AD DS role installed on DC02](screenshots/lab-12-08-adds-role-installed-on-dc02.png)

Additional configuration was still required before the server could operate as a domain controller.

---

### 9. Selected Additional Domain Controller Deployment

Promotion was started with:

```text
Add a domain controller to an existing domain
```

Domain:

```text
mrtg.local
```

![Add domain controller to existing domain](screenshots/lab-12-09-add-domain-controller-to-existing-domain.png)

This added `MRTG-DC02` to the existing domain instead of creating a separate forest.

---

### 10. Selected DNS and Global Catalog

The following options were enabled:

```text
Domain Name System (DNS) server
Global Catalog (GC)
```

![DC02 DNS and Global Catalog selected](screenshots/lab-12-10-dc02-dns-global-catalog-selected.png)

This prepared the second domain controller to provide DNS and Global Catalog services.

---

### 11. Selected the Initial Replication Source

Initial replication used:

```text
MRTG-DC01.mrtg.local
```

![DC02 replication source DC01](screenshots/lab-12-11-dc02-replication-source-dc01.png)

This identified the existing domain controller as the source for the initial directory copy.

---

### 12. Completed the Prerequisite Check

The prerequisite validation passed.

![DC02 prerequisite check passed](screenshots/lab-12-12-dc02-prerequisite-check-passed.png)

The DNS delegation warning was expected because the isolated internal namespace did not have an external parent DNS zone requiring delegation.

---

### 13. Completed Promotion and Restart

After promotion, `MRTG-DC02` restarted and displayed AD DS and DNS in Server Manager.

![DC02 promoted and rebooted](screenshots/lab-12-13-dc02-promoted-and-rebooted.png)

This confirmed that the server was operating as an additional domain controller.

---

### 14. Validated Both Domain Controllers in ADUC

The Domain Controllers OU contained:

```text
MRTG-DC01
MRTG-DC02
```

![Both domain controllers visible in ADUC](screenshots/lab-12-14-both-domain-controllers-visible-in-aduc.png)

This confirmed that both domain controller computer objects were present.

---

### 15. Validated Active Directory Site Registration

Active Directory Sites and Services showed both domain controllers in the default site.

![Both DCs visible in Sites and Services](screenshots/lab-12-15-both-dcs-visible-in-sites-and-services.png)

This confirmed registration in the current site topology.

---

### 16. Validated DNS Host Records

DNS Manager showed records for both domain controllers and the client.

| Host | Address |
|---|---:|
| `mrtg-dc01` | `192.168.10.10` |
| `mrtg-dc02` | `192.168.10.11` |
| `CLIENT01` | `192.168.10.101` |

![DNS records for both domain controllers](screenshots/lab-12-16-dns-records-for-both-domain-controllers.png)

This confirmed forward DNS registration for both domain controllers.

---

### 17. Validated Domain Controllers with PowerShell

Command used:

```powershell
Get-ADDomainController -Filter * |
    Select-Object HostName, Site, IPv4Address, IsGlobalCatalog
```

![Get-ADDomainController shows both DCs](screenshots/lab-12-17-get-addomaincontroller-shows-both-dcs.png)

Validated result:

| Domain Controller | Address | Global Catalog |
|---|---:|---|
| `MRTG-DC01.mrtg.local` | `192.168.10.10` | `True` |
| `MRTG-DC02.mrtg.local` | `192.168.10.11` | `True` |

---

### 18. Reviewed the Replication Summary

Command used:

```cmd
repadmin /replsummary
```

![Repadmin replication summary successful](screenshots/lab-12-18-repadmin-replsummary-successful.png)

The summary reported zero replication failures for both domain controllers.

---

### 19. Reviewed Detailed Replication Status

Command used:

```cmd
repadmin /showrepl
```

![Repadmin showrepl successful](screenshots/lab-12-19-repadmin-showrepl-successful.png)

Successful inbound replication was shown for the major directory partitions:

- `DC=mrtg,DC=local`
- `CN=Configuration,DC=mrtg,DC=local`
- `CN=Schema,CN=Configuration,DC=mrtg,DC=local`
- `DC=DomainDnsZones,DC=mrtg,DC=local`
- `DC=ForestDnsZones,DC=mrtg,DC=local`

`repadmin /showrepl` reports inbound replication for the domain controller being queried.

---

### 20. Ran the Replication Diagnostic Test

Command used:

```cmd
dcdiag /test:replications /q
```

![DCDIAG replication health check](screenshots/lab-12-20-dcdiag-replication-health-check.png)

The quiet-mode command returned no output, indicating that the replication test did not report an error.

---

### 21. Created a Test OU on MRTG-DC01

Test OU:

```text
Lab12-Replication-Test
```

![Test OU created on DC01](screenshots/lab-12-21-test-ou-created-on-dc01.png)

This created a visible directory object for replication testing.

---

### 22. Confirmed the Test OU on MRTG-DC02

The management console was connected to `MRTG-DC02`, where the test OU became visible.

![Test OU replicated to DC02](screenshots/lab-12-22-test-ou-replicated-to-dc02.png)

This demonstrated replication from `MRTG-DC01` to `MRTG-DC02`.

---

### 23. Created a Test User on MRTG-DC02

A user was created inside the replicated test OU while connected to `MRTG-DC02`.

```text
Replication Test
```

![Test user created on DC02](screenshots/lab-12-23-test-user-created-on-dc02.png)

This created a directory change on the second domain controller.

---

### 24. Confirmed the Test User on MRTG-DC01

The test user became visible while the management console was connected to `MRTG-DC01`.

![Test user replicated to DC01](screenshots/lab-12-24-test-user-replicated-to-dc01.png)

This demonstrated replication from `MRTG-DC02` to `MRTG-DC01`.

Together, the two tests demonstrated Active Directory's multi-master replication behavior.

---

### 25. Configured DNS Client Redundancy on MRTG-DC01

| Setting | Value |
|---|---|
| Preferred DNS | `192.168.10.10` |
| Alternate DNS | `192.168.10.11` |

![DC01 DNS redundancy configured](screenshots/lab-12-25-dc01-dns-redundancy-configured.png)

---

### 26. Configured DNS Client Redundancy on MRTG-DC02

| Setting | Value |
|---|---|
| Preferred DNS | `192.168.10.11` |
| Alternate DNS | `192.168.10.10` |

![DC02 DNS redundancy configured](screenshots/lab-12-26-dc02-dns-redundancy-configured.png)

Both domain controllers now had access to two internal DNS resolvers.

---

### 27. Completed Final Replication Validation

Commands used:

```cmd
repadmin /replsummary
dcdiag /test:replications /q
```

![Final replication health validation](screenshots/lab-12-27-final-replication-health-validation.png)

| Validation | Result |
|---|---|
| `repadmin /replsummary` | Zero failures |
| `dcdiag /test:replications /q` | No reported replication errors |

This confirmed that replication remained healthy after the DNS client changes.

---

### 28. Created the Final MRTG-DC02 Lab Checkpoint

Checkpoint name:

```text
MRTG-DC02_Post-Lab12_AD-Replication-Validated
```

![DC02 final Lab 12 checkpoint created](screenshots/lab-12-28-dc02-final-lab12-checkpoint-created.png)

---

### 29. Created the Final MRTG-DC01 Lab Checkpoint

Checkpoint name:

```text
MRTG-DC01_Post-Lab12_AD-Replication-Validated
```

![DC01 final Lab 12 checkpoint created](screenshots/lab-12-29-dc01-final-lab12-checkpoint-created.png)

The final checkpoints preserved temporary lab recovery points after validation. They were not substitutes for supported domain controller backups.

---

## Troubleshooting and Validation Notes

Active Directory replication was reviewed through several independent methods:

```cmd
repadmin /replsummary
repadmin /showrepl
dcdiag /test:replications /q
```

Validation also included:

- Domain controller visibility in ADUC
- Site registration
- DNS host records
- Global Catalog status
- Object creation on each domain controller
- Object visibility on the replication partner

A second domain controller should not be considered healthy based only on successful promotion. DNS registration, directory partitions, replication status, and object consistency must also be validated.

---

## Security and IAM Relevance

Active Directory is a central dependency for authentication, authorization, Group Policy, DNS, and directory-backed access.

This lab supports:

- Additional authentication capacity
- Directory-service redundancy
- DNS redundancy
- Global Catalog availability
- Multi-master identity-data replication
- Replication health validation
- Reduced dependency on one domain controller
- Evidence-based infrastructure review

Because both virtual domain controllers share one physical Hyper-V host, the lab improves service-level redundancy but does not eliminate the physical host as a single point of failure.

---

## Risks Addressed

This lab reduces the risk of:

- Dependency on one domain controller service instance
- Loss of the only Active Directory replication partner
- Single-instance internal DNS service
- Unvalidated domain controller promotion
- Missing replication health evidence
- Directory object inconsistency
- Weak troubleshooting readiness

This lab does not mitigate:

- Complete Hyper-V host failure
- Shared storage failure
- Site-wide outage
- Unsupported recovery practices
- Missing backups

---

## Control Mapping

| Control Area | Lab Contribution |
|---|---|
| Directory Resilience | Adds a second domain controller |
| Authentication Availability | Provides another domain-service endpoint |
| DNS Resilience | Runs DNS on both domain controllers |
| Global Catalog Availability | Enables Global Catalog on `MRTG-DC02` |
| Replication Validation | Uses `repadmin` and `dcdiag` |
| Identity Data Consistency | Demonstrates object replication in both directions |
| Troubleshooting Readiness | Validates DNS, discovery, site registration, and replication |
| Audit Readiness | Captures deployment and health-validation evidence |

---

## Validation Results

| Validation Item | Result |
|---|---|
| `MRTG-DC02` VM prepared | Passed |
| Server renamed to `MRTG-DC02` | Passed |
| Static IP configured | Passed |
| DNS pointed to `MRTG-DC01` before promotion | Passed |
| Connectivity to `MRTG-DC01` validated | Passed |
| Domain DNS resolution validated | Passed |
| Domain controller discovery validated | Passed |
| `MRTG-DC02` joined to `mrtg.local` | Passed |
| Temporary pre-change checkpoint created | Passed |
| AD DS role installed | Passed |
| `MRTG-DC02` promoted as an additional domain controller | Passed |
| DNS enabled on `MRTG-DC02` | Passed |
| Global Catalog enabled on `MRTG-DC02` | Passed |
| Initial replication source selected | Passed |
| Prerequisite checks passed | Passed |
| Both domain controllers visible in ADUC | Passed |
| Both domain controllers visible in Sites and Services | Passed |
| DNS records present for both domain controllers | Passed |
| PowerShell returned both domain controllers | Passed |
| Both domain controllers reported as Global Catalog servers | Passed |
| Replication summary reported zero failures | Passed |
| Inbound replication status reviewed | Passed |
| Replication diagnostic reported no errors | Passed |
| Test OU replicated to `MRTG-DC02` | Passed |
| Test user replicated to `MRTG-DC01` | Passed |
| DNS client redundancy configured | Passed |
| Final replication validation completed | Passed |
| Temporary final checkpoints created | Passed |

---

## Evidence Collected

| Evidence | File |
|---|---|
| Both domain controller VMs running | `screenshots/lab-12-01-hyperv-dc01-dc02-running.png` |
| MRTG-DC02 server identity | `screenshots/lab-12-02-dc02-server-renamed-and-ip-configured.png` |
| MRTG-DC02 static IP and DNS | `screenshots/lab-12-03-dc02-static-ip-dns-configured.png` |
| Connectivity and discovery validation | `screenshots/lab-12-04-dc02-connectivity-to-dc01-validated.png` |
| Domain membership | `screenshots/lab-12-05-dc02-domain-membership-confirmed.png` |
| Pre-AD DS checkpoint | `screenshots/lab-12-06-dc02-pre-adds-checkpoint-created.png` |
| AD DS role selection | `screenshots/lab-12-07-adds-role-selected-on-dc02.png` |
| AD DS role installation | `screenshots/lab-12-08-adds-role-installed-on-dc02.png` |
| Additional domain controller deployment | `screenshots/lab-12-09-add-domain-controller-to-existing-domain.png` |
| DNS and Global Catalog selection | `screenshots/lab-12-10-dc02-dns-global-catalog-selected.png` |
| Initial replication source | `screenshots/lab-12-11-dc02-replication-source-dc01.png` |
| Prerequisite validation | `screenshots/lab-12-12-dc02-prerequisite-check-passed.png` |
| Completed promotion | `screenshots/lab-12-13-dc02-promoted-and-rebooted.png` |
| Domain Controllers OU | `screenshots/lab-12-14-both-domain-controllers-visible-in-aduc.png` |
| Active Directory site registration | `screenshots/lab-12-15-both-dcs-visible-in-sites-and-services.png` |
| DNS host records | `screenshots/lab-12-16-dns-records-for-both-domain-controllers.png` |
| PowerShell domain controller inventory | `screenshots/lab-12-17-get-addomaincontroller-shows-both-dcs.png` |
| Replication summary | `screenshots/lab-12-18-repadmin-replsummary-successful.png` |
| Detailed replication status | `screenshots/lab-12-19-repadmin-showrepl-successful.png` |
| Replication diagnostic | `screenshots/lab-12-20-dcdiag-replication-health-check.png` |
| Test OU on MRTG-DC01 | `screenshots/lab-12-21-test-ou-created-on-dc01.png` |
| Test OU on MRTG-DC02 | `screenshots/lab-12-22-test-ou-replicated-to-dc02.png` |
| Test user on MRTG-DC02 | `screenshots/lab-12-23-test-user-created-on-dc02.png` |
| Test user on MRTG-DC01 | `screenshots/lab-12-24-test-user-replicated-to-dc01.png` |
| MRTG-DC01 DNS client settings | `screenshots/lab-12-25-dc01-dns-redundancy-configured.png` |
| MRTG-DC02 DNS client settings | `screenshots/lab-12-26-dc02-dns-redundancy-configured.png` |
| Final replication validation | `screenshots/lab-12-27-final-replication-health-validation.png` |
| Final MRTG-DC02 checkpoint | `screenshots/lab-12-28-dc02-final-lab12-checkpoint-created.png` |
| Final MRTG-DC01 checkpoint | `screenshots/lab-12-29-dc01-final-lab12-checkpoint-created.png` |

---

## What I Would Improve in Production

In a production environment, I would:

- Place domain controllers on separate physical hosts
- Use separate sites or failure domains where possible
- Follow a formal domain controller deployment checklist
- Use a namespace based on a registered organizational domain
- Document FSMO role ownership
- Configure supported System State backups
- Test authoritative and non-authoritative recovery procedures
- Monitor replication continuously
- Alert on replication latency and failure
- Monitor DNS service and zone health
- Review DNS client order against the production design
- Apply domain controller security baselines
- Restrict interactive access
- Avoid unnecessary roles and applications
- Define Active Directory sites and subnets
- Test domain controller unavailability scenarios
- Use formal change management
- Avoid relying on hypervisor checkpoints as backups

---

## Lessons Learned

This lab reinforced that adding a second domain controller requires more than installing a role.

The server must have correct network and DNS settings, join the existing domain, complete promotion, register in DNS, appear in the site topology, and replicate every required directory partition.

The primary takeaway is that directory resilience must be demonstrated through health commands and object-level testing.

The lab also showed the difference between service redundancy and full infrastructure resilience. Two domain controllers on one Hyper-V host still depend on that host.

---

## Outcome

Lab 12 successfully added `MRTG-DC02` as an additional domain controller for `mrtg.local`.

The lab confirmed that:

- `MRTG-DC02` received static IP and DNS configuration
- The server joined the existing domain
- AD DS was installed
- `MRTG-DC02` was promoted successfully
- DNS and Global Catalog were enabled
- Both domain controllers appeared in Active Directory management tools
- DNS records existed for both domain controllers
- PowerShell identified both servers as Global Catalogs
- `repadmin` reported healthy replication
- `dcdiag` reported no replication errors
- An OU replicated from `MRTG-DC01` to `MRTG-DC02`
- A user replicated from `MRTG-DC02` to `MRTG-DC01`
- DNS client redundancy was configured
- Final replication validation passed

The environment now has two healthy domain controller service instances with validated multi-master replication, while the shared Hyper-V host remains a documented infrastructure limitation.

---

## Next Lab

[Lab 13: Centralized Logging and Event Forwarding for Identity Events](../Lab-13-Centralized-Logging-and-Event-Forwarding-for-Identity-Events/)

Lab 13 centralizes identity-related Windows events to improve visibility into authentication, account-management, and directory activity.
