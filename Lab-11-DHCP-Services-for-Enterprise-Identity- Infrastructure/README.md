# Lab-11: DHCP Services for Enterprise Identity Infrastructure

![Platform](https://img.shields.io/badge/Platform-Windows%20Server%202022-1f6feb)
![Directory](https://img.shields.io/badge/Directory-Active%20Directory-0e8a16)
![Focus](https://img.shields.io/badge/Focus-DHCP%20Services-7a3cff)
![Service](https://img.shields.io/badge/Service-Enterprise%20Identity%20Infrastructure-bd561d)

---

## Overview

In this lab, I implemented DHCP Services in the Monroe Redstone Technology Group (MRTG) Active Directory environment to centralize IPv4 address assignment for domain-connected systems and support reliable identity infrastructure. The objective was to move beyond static-only client networking and show that workstations can automatically receive the correct IP configuration, default gateway, DNS settings, and domain context needed for authentication and management.

Unlike manual static addressing, DHCP is not just a convenience feature. It is a centrally managed infrastructure service that ensures clients receive consistent network configuration at scale. That distinction matters because Active Directory authentication, DNS name resolution, Group Policy processing, remote administration, and general domain communication all depend on stable and predictable client addressing.

This lab builds directly on the authentication hardening work from Lab 10. Lab 10 established differentiated password controls across identity tiers. Lab 11 extends that foundation by introducing the supporting network service that helps domain-joined systems consistently locate and communicate with identity services.

---

## Objectives

- Install and configure the DHCP Server role
- Authorize the DHCP server in Active Directory
- Create a usable IPv4 scope for enterprise workstations
- Configure scope options for gateway, DNS, and domain name
- Add exclusions and a reservation for controlled lease assignment
- Validate that a domain-connected client receives the correct lease
- Confirm DNS resolution and domain communication using DHCP-provided settings
- Demonstrate how DHCP supports identity-dependent infrastructure operations

---

## Scope

### Included

- Installation of the DHCP Server role
- DHCP post-installation configuration
- Active Directory authorization of the DHCP server
- Creation of an IPv4 scope
- Configuration of scope options
- Creation of an exclusion range
- Creation of a DHCP reservation
- Lease renewal and client validation
- Verification of DNS-aware network configuration
- Validation of domain communication from a DHCP client

### Not Included

- DHCP failover configuration
- Split-scope DHCP design
- IPv6 DHCP configuration
- DHCP relay agents
- PXE or imaging integration
- DHCP policies by device class or vendor class
- Multi-site DHCP deployment
- High-availability or load-balanced DHCP architecture

This lab stays focused on DHCP service deployment, Active Directory authorization, scope configuration, and client lease validation in support of directory-connected infrastructure.

---

## Lab Environment

### Systems

- **Host Platform:** Hyper-V
- **Domain Controller / DNS Server:** `MRTG-DC01`
- **DHCP Server:** `MRTG-DC01`
- **Client System:** `MRTG-CLIENT-01`
- **Domain:** `mrtg.local`

> In this lab, DHCP was implemented on the domain controller to keep the environment compact. In larger production environments, DHCP is often separated to a member server or dedicated infrastructure server.

### Network / Scope Design

- **IPv4 Network:** `192.168.10.0/24`
- **Scope Name:** `MRTG-Workstations`
- **Scope Range:** `192.168.10.100 - 192.168.10.199`
- **Subnet Mask:** `255.255.255.0`
- **Default Gateway:** `192.168.10.1`
- **Preferred DNS Server:** `192.168.10.10`
- **DNS Domain Name:** `mrtg.local`
- **Exclusion Range:** `192.168.10.150 - 192.168.10.159`
- **Reservation Target:** `MRTG-CLIENT-01`

### Directory Structure

- **Parent OU:** `_MRTG`
- **Servers OU:** `_MRTG/Servers`
- **Computers OU:** `_MRTG/Computers`
- **Domain Controller:** `Domain Controllers`
- **Client Device:** `MRTG-CLIENT-01`

### Systems Used

- **Server:** `MRTG-DC01`
- **Client:** `MRTG-CLIENT-01`

### Tools / Technologies Used

- Windows Server 2022
- DHCP Server role
- Server Manager
- DHCP Management Console
- Active Directory
- DNS
- Command Prompt
- PowerShell
- `ipconfig`
- `nslookup`
- `ping`

---

## Architecture / Design

This lab was designed to support the following identity-infrastructure need:

> MRTG requires centrally managed IP address assignment so domain-connected systems consistently receive the correct gateway, DNS server, and domain-aware network configuration needed to communicate with Active Directory services.

### Design Logic

- Workstations should receive standardized IPv4 settings automatically rather than through manual per-device configuration
- DHCP should be authorized in Active Directory so only approved infrastructure can issue leases
- Scope options should direct clients to the internal DNS server that supports domain authentication and service discovery
- Exclusions should preserve address space for controlled assignment needs
- Reservations should provide predictable addressing for selected systems without forcing full static configuration

### IAM Perspective

This design supports:

- identity-dependent network reliability
- centralized infrastructure control
- predictable client-to-directory communication
- DNS-aware authentication support
- reduced network configuration drift
- stronger operational governance for domain-connected systems

The key design point is that DHCP is not just a networking convenience. It is a support service for identity operations. If a client receives the wrong DNS server, the wrong gateway, or inconsistent addressing, authentication, policy application, and administrative access can all break. This lab demonstrates how centralized lease management supports a healthier Active Directory environment.

---

## Implementation Steps

### 1. Created a Pre-Lab Checkpoint

Before making changes, I created a Hyper-V checkpoint to preserve the clean post-Lab 10 environment.

**Screenshot Placeholder:** Hyper-V checkpoint for the Lab 11 starting state

---

### 2. Installed the DHCP Server Role

On `MRTG-DC01`, I installed the **DHCP Server** role through Server Manager and included the DHCP management tools.

This provided the services and administrative console needed to configure centralized lease assignment for the MRTG environment.

**Screenshot Placeholder:** Add Roles and Features showing DHCP Server role selection

---

### 3. Completed DHCP Post-Installation Configuration

After installation, I completed the DHCP post-installation tasks so the server could be properly integrated into the domain environment.

This step prepared the server for authorization and active DHCP management.

**Screenshot Placeholder:** DHCP post-installation configuration complete

---

### 4. Opened the DHCP Management Console

In Server Manager, I launched the DHCP console and verified that the local server object was available for configuration.

This is the main management interface used to build scopes, reservations, exclusions, and lease settings.

**Screenshot Placeholder:** DHCP console open on `MRTG-DC01`

---

### 5. Created the IPv4 Scope

I created a new IPv4 scope named `MRTG-Workstations` for the `192.168.10.0/24` network.

Configured values:

- **Start IP address:** `192.168.10.100`
- **End IP address:** `192.168.10.199`
- **Subnet mask:** `255.255.255.0`

This scope defined the address pool that domain-connected client systems can use.

**Screenshot Placeholder:** Scope creation wizard with `MRTG-Workstations`

---

### 6. Added an Exclusion Range

I configured an exclusion range inside the scope:

- **Exclusion range:** `192.168.10.150 - 192.168.10.159`

This preserved a portion of the address pool so those IPs would not be handed out dynamically.

**Screenshot Placeholder:** Exclusion range configured in the scope

---

### 7. Configured Scope Options

I configured the scope options so clients would receive the correct supporting network settings automatically.

Configured values:

- **003 Router:** `192.168.10.1`
- **006 DNS Servers:** `192.168.10.10`
- **015 DNS Domain Name:** `mrtg.local`

These settings are critical because they ensure DHCP clients can route traffic correctly and use the internal DNS service that supports Active Directory name resolution and authentication workflows.

**Screenshot Placeholder:** Scope options showing router, DNS server, and domain name

---

### 8. Authorized the DHCP Server in Active Directory

I authorized `MRTG-DC01` as a valid DHCP server in Active Directory.

This matters because domain environments are designed to prevent unauthorized or rogue DHCP servers from issuing leases.

**Screenshot Placeholder:** DHCP server shown as authorized in the console

---

### 9. Activated the Scope

Once the scope was configured, I activated it so the server could begin leasing addresses to eligible clients.

This changed the scope from a configured object into an active address source for the MRTG environment.

**Screenshot Placeholder:** Active IPv4 scope in DHCP console

---

### 10. Created a Reservation for MRTG-CLIENT-01

To demonstrate controlled lease assignment, I created a reservation for `MRTG-CLIENT-01` using its MAC address.

Configured values:

- **Reserved IP address:** `192.168.10.110`
- **Reservation name:** `MRTG-CLIENT-01`

This allowed the client to continue using DHCP while still receiving a predictable IP address.

**Screenshot Placeholder:** Reservation object for `MRTG-CLIENT-01`

---
### 11. Renewed the Client Lease

On `MRTG-CLIENT-01`, I renewed the DHCP lease so the client would request fresh configuration from the DHCP server.

**Commands used:**

```powershell
ipconfig /release
ipconfig /renew
ipconfig /all
```

This allowed me to verify that the client received an address from the MRTG scope along with the correct supporting options.

**Screenshot Placeholder:** `ipconfig /all` on `MRTG-CLIENT-01`

---

### 12. Validated DNS Resolution and Domain Communication

After renewing the lease, I validated that the client could resolve internal names and communicate correctly with domain infrastructure.

**Commands used:**

```powershell
nslookup mrtg-dc01.mrtg.local
ping mrtg-dc01
ping mrtg.local
```

This confirmed that the DHCP-delivered configuration was supporting proper directory-aware communication.

**Screenshot Placeholder:** Successful `nslookup` and `ping` results from the DHCP client

### 13. Reviewed Active Leases

I returned to the DHCP console and confirmed that the lease was visible under **Address Leases** and that the reservation behavior matched the expected client assignment.

This provided administrative proof that the DHCP server was actively issuing and tracking leases.

**Screenshot Placeholder:** Address Leases showing the client assignment

---

## Validation / Proof

### Validation Scenario 1 - DHCP Server Authorization

**System tested:** `MRTG-DC01`

I opened the DHCP console and confirmed that:

- the server appeared in the DHCP console without authorization errors
- the server was authorized in Active Directory
- the IPv4 scope was visible and active

This confirmed that the DHCP service was approved for use in the MRTG domain environment.

**Screenshot Placeholder:** Authorized server and active scope

---

### Validation Scenario 2 - Client Lease Assignment

**System tested:** `MRTG-CLIENT-01`

I opened Command Prompt on the client and confirmed that:

- the system received an IPv4 address from the configured scope
- the subnet mask matched the intended network
- the default gateway was correct
- the DNS server pointed to the internal domain controller / DNS server

This confirmed that the client was receiving the intended network configuration through DHCP.

**Screenshot Placeholder:** `ipconfig /all` showing DHCP-provided settings

---

### Validation Scenario 3 - Reservation Validation

**System tested:** `MRTG-CLIENT-01`

I renewed the client lease and confirmed that:

- the client received the reserved IP address
- the reservation appeared in the DHCP console
- the lease aligned with the intended reserved assignment

This confirmed that MRTG could use DHCP reservations for predictable address control without fully static host configuration.

**Screenshot Placeholder:** Reservation and matching client lease

---

### Validation Scenario 4 - DNS and Domain Connectivity

**System tested:** `MRTG-CLIENT-01`

I tested name resolution and domain communication and confirmed that:

- `nslookup` resolved the domain controller correctly
- `ping mrtg-dc01` succeeded
- the client could communicate with domain infrastructure using DHCP-provided settings

This confirmed that the DHCP configuration was supporting directory-dependent operations rather than just assigning an IP address.

**Screenshot Placeholder:** Successful DNS resolution and connectivity tests

---

## Security Relevance

This lab strengthens the MRTG environment by moving beyond manual per-device network settings and into centralized infrastructure control that supports directory services.

**Key security benefits include:**

- reduced risk of client misconfiguration
- more consistent DNS settings for domain-connected systems
- Active Directory authorization of DHCP infrastructure
- better support for authentication, policy processing, and administrative access
- improved visibility into lease assignment and address usage
- predictable IP control through reservation-based management

This matters because identity systems do not operate in isolation. Active Directory depends heavily on proper DNS and stable network configuration. If a client receives the wrong DNS settings or inconsistent addressing, authentication issues, failed policy processing, and administrative troubleshooting problems can follow. DHCP gives Active Directory a more reliable foundation by standardizing how systems receive those settings.

---

## Key Skills Demonstrated

- DHCP Server role installation
- DHCP post-installation configuration
- Active Directory-integrated DHCP authorization
- IPv4 scope creation
- DHCP exclusion range configuration
- DHCP reservation management
- Scope option configuration
- Lease renewal and client troubleshooting
- DNS-aware network validation
- Identity-supporting infrastructure administration

---

## Outcome

By the end of this lab, the MRTG environment was able to:

- centrally assign IPv4 addresses to domain-connected client systems
- deliver consistent gateway, DNS, and domain-name configuration through DHCP
- reserve address space through exclusions and reservations
- validate lease assignment from the server side and client side
- confirm that DHCP-delivered settings supported internal DNS resolution and domain communication

This lab moved the environment beyond static client networking and into centralized infrastructure management, which is a more realistic enterprise model for supporting Active Directory-connected systems.

---

## Next Lab

**Lab-12** will build on this infrastructure foundation by deploying an additional domain controller and validating Active Directory replication so the MRTG environment can improve resilience, service availability, and directory consistency.
