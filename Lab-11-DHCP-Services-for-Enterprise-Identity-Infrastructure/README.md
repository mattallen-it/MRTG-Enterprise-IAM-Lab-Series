# Lab 11: DHCP Services for Enterprise Identity Infrastructure

![Platform](https://img.shields.io/badge/Platform-Windows%20Server%202022-blue)
![Technology](https://img.shields.io/badge/Technology-Active%20Directory-blue)
![Service](https://img.shields.io/badge/Service-DHCP%20%26%20DNS-lightgrey)
![Tooling](https://img.shields.io/badge/Tooling-DHCP%20Console%20%26%20CMD-purple)
![Focus](https://img.shields.io/badge/Focus-Identity%20Infrastructure-orange)
![Validation](https://img.shields.io/badge/Validation-Client%20Lease%20%26%20Domain%20Discovery-brightgreen)

---

## Objective

Deploy DHCP services in the `mrtg.local` Active Directory environment.

This lab centralizes IPv4 address assignment and validates that a domain-joined workstation can receive the correct IP and DNS configuration required to locate Active Directory services.

The lab covers DHCP authorization, scope configuration, DNS option distribution, client lease validation, and domain controller discovery.

---

## Business Scenario

Monroe Redstone Technology Group requires centrally managed IP address configuration for domain-connected systems.

Manually assigning workstation addresses does not scale and increases the risk of duplicate addresses, incorrect DNS settings, inconsistent client configuration, and domain service discovery failures.

This lab addresses the need to:

- Deploy DHCP as a managed infrastructure service
- Authorize the Windows DHCP server in Active Directory
- Create a controlled IPv4 scope
- Define a dynamic address pool and exclusions
- Distribute the internal DNS server and domain suffix
- Validate client lease assignment
- Confirm domain communication after the DHCP transition
- Reduce network configuration drift

---

## Lab Summary

In this lab, I installed the DHCP Server role on `MRTG-DC01`.

I documented the server and client network baselines, authorized the Windows DHCP server in Active Directory, created the `MRTG-Client-Scope`, defined the dynamic address range, added an exclusion range, and configured the internal DNS options.

The client Windows computer name was `CLIENT01`, while its Hyper-V virtual machine name was `MRTG-CLIENT-01`.

After the scope was activated, `CLIENT01` was changed from static addressing to automatic IP and DNS configuration. The client renewed its lease and received `192.168.10.101`.

The lease was validated from both the client and DHCP server. Connectivity, DNS resolution, domain controller discovery, and the active domain-user context were then reviewed.

---

## Environment

| Component | Details |
|---|---|
| Domain | `mrtg.local` |
| Domain Controller, DNS, and DHCP Server | `MRTG-DC01` |
| Client Windows Computer Name | `CLIENT01` |
| Client Hyper-V VM Name | `MRTG-CLIENT-01` |
| Server Operating System | Windows Server 2022 |
| Directory Service | Active Directory Domain Services |
| DHCP Scope | `MRTG-Client-Scope` |
| DNS Server | `192.168.10.10` |
| Virtualization Platform | Hyper-V |
| Organization | Monroe Redstone Technology Group |

---

## Prerequisites

- Operational `mrtg.local` Active Directory domain
- `MRTG-DC01` configured with static address `192.168.10.10`
- Active Directory-integrated DNS operating on `MRTG-DC01`
- Domain-joined Windows client named `CLIENT01`
- Administrative access to install and authorize DHCP
- Existing `192.168.10.0/24` isolated lab network
- Baseline client connectivity to the domain controller

---

## Scope

### Included

- Temporary pre-change Hyper-V checkpoint
- Server and client network baseline review
- Baseline connectivity and DNS validation
- DHCP Server role installation
- DHCP post-installation configuration
- Active Directory authorization
- IPv4 scope creation
- Address-pool configuration
- Exclusion-range configuration
- Default gateway option review
- DNS scope-option configuration
- Scope activation
- Client automatic IPv4 and DNS configuration
- DHCP lease release and renewal
- Client-side lease validation
- Server-side lease validation
- Post-change connectivity testing
- DNS resolution testing
- Domain controller discovery testing
- Active domain-user context review

### Not Included

- DHCP reservations
- DHCP failover
- Split-scope design
- DHCP relay
- IPv6 DHCP
- Multi-site DHCP
- PXE integration
- Advanced DHCP policies
- Routed subnet gateway distribution
- Centralized DHCP logging
- SIEM integration
- Rogue DHCP protection at the switch layer

---

## Baseline Network State

| System | Setting | Value |
|---|---|---|
| `MRTG-DC01` | IPv4 Address | `192.168.10.10` |
| `CLIENT01` | Baseline IPv4 Address | `192.168.10.20` |
| Network | Subnet Mask | `255.255.255.0` |
| DNS | DNS Server | `192.168.10.10` |
| Domain | DNS Suffix | `mrtg.local` |

---

## DHCP Scope Configuration

| Setting | Value |
|---|---|
| Scope Name | `MRTG-Client-Scope` |
| Network | `192.168.10.0/24` |
| Start Address | `192.168.10.100` |
| End Address | `192.168.10.200` |
| Exclusion Range | `192.168.10.150` through `192.168.10.160` |
| DNS Domain Name | `mrtg.local` |
| DNS Server Option | `192.168.10.10` |
| Default Gateway | Not configured for the isolated single-subnet lab |
| Scope Status | Active |

---

## Client Lease Result

| Client | Assigned Address | DHCP Server | DNS Server | DNS Suffix |
|---|---|---|---|---|
| `CLIENT01` | `192.168.10.101` | `192.168.10.10` | `192.168.10.10` | `mrtg.local` |

---

## Architecture

`MRTG-DC01` provides Active Directory, DNS, and DHCP services for the isolated lab subnet.

```text
mrtg.local
|-- MRTG-DC01
|   |-- Active Directory Domain Services
|   |-- DNS
|   `-- DHCP Server
|
`-- CLIENT01
    `-- Domain client receiving DHCP configuration
```

DHCP design:

```text
Network:        192.168.10.0/24
Dynamic Range:  192.168.10.100 - 192.168.10.200
Exclusion:      192.168.10.150 - 192.168.10.160
DNS Server:     192.168.10.10
DNS Domain:     mrtg.local
Client Lease:   192.168.10.101
```

This design supports:

- Centralized client addressing
- Consistent DNS distribution
- Reduced manual configuration
- Client lease visibility
- Reliable domain service discovery
- Identity-supporting infrastructure stability

---

## Identity Infrastructure Relevance

Active Directory depends heavily on DNS and reliable network configuration.

Domain clients need the correct IP address, DNS server, and DNS suffix to locate domain controllers, process Group Policy, and access domain services.

| Dependency | IAM Relevance |
|---|---|
| IPv4 Address | Enables communication on the domain subnet |
| DNS Server | Allows clients to locate Active Directory services |
| DNS Suffix | Supports internal name resolution |
| DHCP Authorization | Helps prevent unapproved Windows DHCP servers from issuing leases in the domain |
| Scope Options | Standardize network settings for domain clients |
| Lease Visibility | Supports troubleshooting and asset correlation |
| Domain Controller Discovery | Confirms that the client can locate an Active Directory domain controller |

Active Directory authorization does not prevent every rogue DHCP server. Network protections such as DHCP snooping are required to address unauthorized DHCP at the switching layer.

---

## Implementation and Validation

### 1. Created a Pre-Change Lab Checkpoint

A Hyper-V checkpoint was created before installing DHCP.

![Pre-DHCP services checkpoint](screenshots/lab-11-01-pre-dhcp-services-checkpoint.png)

The checkpoint served as a temporary lab recovery point and was not treated as a supported server backup.

---

### 2. Documented the Domain Controller Baseline

The complete network configuration was reviewed on `MRTG-DC01`.

Command used:

```cmd
ipconfig /all
```

Validated address:

```text
192.168.10.10
```

![Domain controller IP configuration baseline](screenshots/lab-11-02-domain-controller-ipconfig-baseline.png)

This confirmed that the DHCP and DNS server had a static address.

---

### 3. Reviewed the Existing Server Roles

Server Manager was reviewed before the DHCP installation.

Existing roles included:

- Active Directory Domain Services
- DNS Server

![Server Manager AD and DNS baseline](screenshots/lab-11-03-server-manager-ad-dns-baseline.png)

This documented the server-role baseline before DHCP was added.

---

### 4. Documented the Client Network Baseline

The network configuration was reviewed on `CLIENT01`.

```cmd
ipconfig /all
```

Baseline values:

```text
DHCP Enabled: No
IPv4 Address: 192.168.10.20
DNS Server: 192.168.10.10
```

![Client IP configuration baseline](screenshots/lab-11-04-client-ipconfig-baseline.png)

This confirmed that the client used static addressing before the DHCP transition.

---

### 5. Validated Baseline Connectivity and DNS

The client tested communication with the domain controller and internal DNS.

```cmd
ping MRTG-DC01
nslookup mrtg.local
```

![Client ping and nslookup baseline](screenshots/lab-11-05-client-ping-and-nslookup-baseline.png)

This established a working baseline before DHCP was introduced.

---

### 6. Selected the DHCP Server Role

The DHCP Server role was selected in the Add Roles and Features Wizard on `MRTG-DC01`.

![DHCP Server role selected](screenshots/lab-11-06-dhcp-server-role-selected.png)

---

### 7. Completed the DHCP Role Installation

The DHCP Server role installation completed successfully.

![DHCP installation success](screenshots/lab-11-07-dhcp-installation-success.png)

The installation result indicated that post-installation configuration was still required.

---

### 8. Opened DHCP Post-Installation Configuration

The Server Manager notification was used to open the DHCP post-installation task.

![DHCP post-installation configuration required](screenshots/lab-11-08-dhcp-post-install-configuration-required.png)

The post-installation wizard prepares the DHCP security groups and authorizes the server in Active Directory.

---

### 9. Supplied Authorization Credentials

The post-installation wizard was completed with:

```text
MRTG\Administrator
```

![DHCP authorization credentials](screenshots/lab-11-09-dhcp-authorization-credentials.png)

This account had the rights required to authorize the DHCP server in the lab domain.

---

### 10. Confirmed DHCP Authorization

The post-installation summary confirmed successful authorization.

![DHCP authorization success](screenshots/lab-11-10-dhcp-authorization-success.png)

This approved the Windows DHCP server to issue leases in the Active Directory environment.

---

### 11. Confirmed DHCP Console Visibility

The DHCP management console displayed:

```text
mrtg-dc01.mrtg.local
```

IPv4 and IPv6 management containers were visible.

![DHCP console server visible](screenshots/lab-11-11-dhcp-console-server-visible.png)

---

### 12. Named the IPv4 Scope

A new IPv4 scope was created.

Scope name:

```text
MRTG-Client-Scope
```

Description:

```text
DHCP scope for MRTG domain clients on 192.168.10.0/24
```

![DHCP scope name](screenshots/lab-11-12-dhcp-scope-name.png)

---

### 13. Defined the Address Pool

| Setting | Value |
|---|---|
| Start Address | `192.168.10.100` |
| End Address | `192.168.10.200` |
| Subnet Mask | `255.255.255.0` |
| Prefix Length | `/24` |

![DHCP scope IP range](screenshots/lab-11-13-dhcp-scope-ip-range.png)

This defined the addresses available for dynamic allocation.

---

### 14. Added the Exclusion Range

The following addresses were excluded from dynamic assignment:

```text
192.168.10.150 - 192.168.10.160
```

![DHCP scope exclusions](screenshots/lab-11-14-dhcp-scope-exclusions.png)

Excluded addresses remain inside the scope range but are not offered as dynamic leases.

---

### 15. Reviewed the Default Gateway Option

The Router option was intentionally left blank.

![DHCP scope gateway option](screenshots/lab-11-15-dhcp-scope-gateway-option.png)

The isolated Hyper-V network used one subnet and had no router or outbound path requiring a default gateway.

---

### 16. Configured DNS Scope Options

The scope was configured to distribute:

| Option | Value |
|---|---|
| DNS Domain Name | `mrtg.local` |
| DNS Server | `192.168.10.10` |

![DHCP scope DNS option](screenshots/lab-11-16-dhcp-scope-dns-option.png)

These options allow clients to use the internal Active Directory DNS service.

---

### 17. Selected Immediate Scope Activation

The scope was configured for immediate activation.

```text
Yes, I want to activate this scope now
```

![DHCP scope activation](screenshots/lab-11-17-dhcp-scope-activation.png)

---

### 18. Confirmed the Active Scope

The completed scope appeared under IPv4.

```text
IPv4
`-- Scope [192.168.10.0] MRTG-Client-Scope
```

![DHCP IPv4 scope active](screenshots/lab-11-18-dhcp-ipv4-scope-active.png)

This confirmed that the DHCP scope was available to issue leases.

---

### 19. Enabled Automatic Client Configuration

The IPv4 properties on `CLIENT01` were changed to:

```text
Obtain an IP address automatically
Obtain DNS server address automatically
```

![Client IPv4 automatic](screenshots/lab-11-19-client-ipv4-automatic.png)

This allowed the client to request its configuration through DHCP.

---

### 20. Released and Renewed the Client Lease

Commands used:

```cmd
ipconfig /release
ipconfig /renew
```

The client received:

```text
IPv4 Address: 192.168.10.101
Subnet Mask: 255.255.255.0
```

![Client DHCP lease renewed](screenshots/lab-11-20-client-dhcp-lease-renewed.png)

This confirmed successful DHCP communication.

---

### 21. Validated the Client Configuration

The full client network configuration was reviewed.

```cmd
ipconfig /all
```

Validated values:

```text
DHCP Enabled: Yes
Autoconfiguration Enabled: Yes
IPv4 Address: 192.168.10.101
DHCP Server: 192.168.10.10
DNS Server: 192.168.10.10
DNS Suffix Search List: mrtg.local
```

![Client DHCP-assigned IP configuration](screenshots/lab-11-21-client-dhcp-assigned-ipconfig.png)

This confirmed that the DHCP server supplied both address and DNS configuration.

---

### 22. Verified the Lease in the DHCP Console

The active lease was reviewed from the DHCP server.

```text
Client IP Address: 192.168.10.101
Name: CLIENT01.mrtg.local
```

![DHCP address lease visible](screenshots/lab-11-22-dhcp-address-lease-visible.png)

This confirmed server-side visibility of the client lease.

---

### 23. Validated Post-Change Connectivity

The client tested connectivity to the domain controller.

```cmd
ping MRTG-DC01
```

Validated response:

```text
Reply from 192.168.10.10
```

![Client ping to domain controller after DHCP](screenshots/lab-11-23-client-ping-dc-after-dhcp.png)

This confirmed IP connectivity after the DHCP transition.

---

### 24. Validated Internal DNS Resolution

The client queried the internal domain.

```cmd
nslookup mrtg.local
```

Validated result:

```text
Name: mrtg.local
Address: 192.168.10.10
```

![Client domain lookup after DHCP](screenshots/lab-11-24-client-nslookup-domain-after-dhcp.png)

The `Server: Unknown` result indicated that a matching reverse DNS record for the DNS server was unavailable. Forward resolution of `mrtg.local` still succeeded.

---

### 25. Validated Domain Controller Discovery

The client queried Active Directory for a domain controller.

```cmd
nltest /dsgetdc:mrtg.local
```

Validated result:

```text
DC: \\MRTG-DC01.mrtg.local
Address: \\192.168.10.10
The command completed successfully
```

![Client domain controller discovery](screenshots/lab-11-25-client-domain-controller-discovery.png)

This confirmed that the DHCP-configured client could locate an Active Directory domain controller.

---

### 26. Reviewed the Active Domain User Context

The active user context was reviewed.

```cmd
whoami
```

Validated result:

```text
mrtg\kevin.carter
```

![Client domain user session](screenshots/lab-11-26-client-domain-user-session.png)

This confirmed that the existing client session was running under a domain identity after the network change.

The command confirms the current user context. It does not independently prove that a new authentication occurred after the DHCP lease renewal.

---

## Security and IAM Relevance

Identity services depend on reliable network and DNS configuration.

Incorrect DNS settings can prevent clients from locating domain controllers, processing Group Policy, or authenticating to domain services.

This lab supports:

- Centralized network configuration
- Active Directory authorization of the Windows DHCP server
- Consistent internal DNS distribution
- Reduced workstation configuration drift
- Improved domain-service discovery
- Client lease visibility
- Documented infrastructure validation

DHCP and DNS do not perform IAM functions directly, but they provide critical infrastructure that Active Directory authentication and policy processing depend on.

---

## Risks Addressed

This lab reduces the risk of:

- Manual workstation address errors
- Duplicate static IP addresses
- Incorrect client DNS configuration
- Domain controller discovery failures
- Group Policy processing issues
- Name-resolution-related authentication failures
- Weak visibility into client address assignment
- Accidental use of an unauthorized Windows DHCP server
- Client network configuration drift

---

## Control Mapping

| Control Area | Lab Contribution |
|---|---|
| Network Configuration Management | Centralizes client IPv4 assignment |
| Identity Infrastructure Support | Distributes DNS settings required for domain discovery |
| DHCP Authorization | Authorizes the Windows DHCP server in Active Directory |
| Operational Consistency | Standardizes client address and DNS configuration |
| Scope Management | Defines dynamic ranges and exclusions |
| Lease Visibility | Confirms the client lease in the DHCP console |
| Change Validation | Captures pre-change and post-change evidence |
| Domain Reliability | Validates DNS and domain controller discovery |

---

## Validation Results

| Validation Item | Result |
|---|---|
| Temporary pre-change checkpoint created | Passed |
| Domain controller baseline documented | Passed |
| Client baseline documented | Passed |
| Baseline connectivity and DNS tested | Passed |
| DHCP Server role installed | Passed |
| DHCP post-installation configuration completed | Passed |
| DHCP server authorized in Active Directory | Passed |
| DHCP server visible in management console | Passed |
| IPv4 scope created | Passed |
| Address pool configured | Passed |
| Exclusion range configured | Passed |
| Default gateway option reviewed | Passed |
| DNS scope options configured | Passed |
| Scope activated | Passed |
| Client configured for automatic addressing | Passed |
| Client lease renewed | Passed |
| Client received `192.168.10.101` | Passed |
| Lease visible in DHCP console | Passed |
| Client reached `MRTG-DC01` | Passed |
| Client resolved `mrtg.local` | Passed |
| Client discovered the domain controller | Passed |
| Active domain-user context reviewed | Passed |

---

## Evidence Collected

| Evidence | File |
|---|---|
| Pre-DHCP services checkpoint | `screenshots/lab-11-01-pre-dhcp-services-checkpoint.png` |
| Domain controller network baseline | `screenshots/lab-11-02-domain-controller-ipconfig-baseline.png` |
| Server Manager role baseline | `screenshots/lab-11-03-server-manager-ad-dns-baseline.png` |
| Client network baseline | `screenshots/lab-11-04-client-ipconfig-baseline.png` |
| Baseline client connectivity and DNS | `screenshots/lab-11-05-client-ping-and-nslookup-baseline.png` |
| DHCP Server role selection | `screenshots/lab-11-06-dhcp-server-role-selected.png` |
| DHCP installation | `screenshots/lab-11-07-dhcp-installation-success.png` |
| DHCP post-installation task | `screenshots/lab-11-08-dhcp-post-install-configuration-required.png` |
| DHCP authorization credentials | `screenshots/lab-11-09-dhcp-authorization-credentials.png` |
| DHCP authorization result | `screenshots/lab-11-10-dhcp-authorization-success.png` |
| DHCP console server visibility | `screenshots/lab-11-11-dhcp-console-server-visible.png` |
| DHCP scope name | `screenshots/lab-11-12-dhcp-scope-name.png` |
| DHCP address pool | `screenshots/lab-11-13-dhcp-scope-ip-range.png` |
| DHCP exclusion range | `screenshots/lab-11-14-dhcp-scope-exclusions.png` |
| DHCP gateway option | `screenshots/lab-11-15-dhcp-scope-gateway-option.png` |
| DHCP DNS options | `screenshots/lab-11-16-dhcp-scope-dns-option.png` |
| DHCP scope activation | `screenshots/lab-11-17-dhcp-scope-activation.png` |
| Active IPv4 scope | `screenshots/lab-11-18-dhcp-ipv4-scope-active.png` |
| Client automatic IPv4 configuration | `screenshots/lab-11-19-client-ipv4-automatic.png` |
| Client lease renewal | `screenshots/lab-11-20-client-dhcp-lease-renewed.png` |
| Client DHCP configuration | `screenshots/lab-11-21-client-dhcp-assigned-ipconfig.png` |
| Server-side lease visibility | `screenshots/lab-11-22-dhcp-address-lease-visible.png` |
| Post-change client connectivity | `screenshots/lab-11-23-client-ping-dc-after-dhcp.png` |
| Post-change DNS resolution | `screenshots/lab-11-24-client-nslookup-domain-after-dhcp.png` |
| Domain controller discovery | `screenshots/lab-11-25-client-domain-controller-discovery.png` |
| Active domain-user context | `screenshots/lab-11-26-client-domain-user-session.png` |

---

## What I Would Improve in Production

In a production environment, I would:

- Host DHCP on dedicated infrastructure servers instead of a domain controller
- Configure DHCP failover
- Use reservations for infrastructure devices where appropriate
- Document scope ownership and naming standards
- Use IP Address Management for larger environments
- Delegate DHCP authorization instead of routinely using broad administrative credentials
- Configure DHCP audit-log collection and retention
- Monitor DHCP service health and lease capacity
- Configure DHCP relay for routed subnets
- Define gateway options for routed networks
- Create reverse lookup zones and appropriate PTR records
- Implement switch-level DHCP snooping
- Monitor for unauthorized DHCP behavior
- Integrate DHCP events with centralized logging
- Use formal change management for scope modifications
- Use supported backups instead of Hyper-V checkpoints

---

## Lessons Learned

This lab reinforced that Active Directory depends on supporting infrastructure.

Domain clients require reliable IP configuration and correct internal DNS settings to locate domain controllers and process domain services.

The primary takeaway is that DHCP standardizes client configuration, but the service must be authorized, scoped, activated, and validated carefully.

Successful IP assignment alone is not enough. DNS resolution and domain controller discovery must also succeed.

---

## Outcome

Lab 11 successfully deployed DHCP services in the MRTG Active Directory environment.

The lab confirmed that:

- Server and client network baselines were documented
- The DHCP Server role was installed
- DHCP post-installation configuration was completed
- The Windows DHCP server was authorized in Active Directory
- `MRTG-Client-Scope` was created and activated
- The dynamic range and exclusion range were configured
- Internal DNS options were distributed
- `CLIENT01` changed from static to automatic configuration
- The client received `192.168.10.101`
- The lease appeared in the DHCP console
- IP connectivity remained functional
- Internal DNS resolution succeeded
- Domain controller discovery succeeded
- The active domain-user context remained available

The environment now provides centralized IPv4 configuration for domain clients while preserving access to Active Directory and DNS services.

---

## Next Lab

[Lab 12: Additional Domain Controller and AD Replication](../Lab-12-Additional-Domain-Controller-and-AD-Replication/)

Lab 12 adds a second domain controller and validates Active Directory replication to improve directory-service resilience.
