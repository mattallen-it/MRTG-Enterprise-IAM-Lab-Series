Lab 11 – DHCP Services for Enterprise Identity Infrastructure
Objective

Deploy and validate Windows DHCP in the MRTG lab so domain clients automatically receive the correct IP configuration, DNS settings, and domain-aware network options required for Active Directory communication, authentication, and reliable identity infrastructure.

Scope

This lab focused on installing and authorizing the DHCP Server role on MRTG-DC01, creating and activating an IPv4 scope, configuring DHCP scope options for the mrtg.local domain, and validating that CLIENT01 could successfully move from static addressing to DHCP while maintaining full domain functionality.

This lab did not include:

multiple scopes
VLAN segmentation
DHCP failover
routed gateway design
advanced reservations or policies beyond baseline validation
Environment
Infrastructure
Host Platform: Hyper-V
Domain Controller: MRTG-DC01
Client Workstation: CLIENT01
Domain: mrtg.local
Server Roles in Use
Active Directory Domain Services
DNS
DHCP
Baseline Addressing
MRTG-DC01: 192.168.10.10
CLIENT01 (before DHCP): 192.168.10.20
DHCP Scope Range: 192.168.10.100 - 192.168.10.200
Scope Exclusion: 192.168.10.150 - 192.168.10.160
Important Design Note

This lab network was operating as an isolated same-subnet environment with no default gateway configured. That was acceptable for this stage because the focus was validating DHCP support for AD and DNS-based identity services, not routed network access.

Architecture
Identity Infrastructure Flow
MRTG-DC01 provides:
Active Directory Domain Services
DNS
DHCP
CLIENT01 receives from DHCP:
IPv4 address
subnet mask
DNS server
DNS suffix
CLIENT01 uses the DHCP-provided DNS settings to:
resolve mrtg.local
locate MRTG-DC01
maintain domain communication and authentication
Logical Relationship

CLIENT01 → DHCP lease from MRTG-DC01 → DNS resolution to MRTG-DC01 → Active Directory communication within mrtg.local

Pre-Lab Checkpoint

A Hyper-V checkpoint was created before installing DHCP so the lab could be rolled back cleanly if configuration issues occurred.

Screenshot: Lab-11-01-Pre-DHCP-Checkpoint.png

Step 1 – Validate Baseline Network and Identity Health

Before deploying DHCP, baseline network and identity functionality were verified on both the domain controller and the client.

Domain Controller Baseline

On MRTG-DC01, ipconfig /all confirmed:

static IPv4 address
DNS pointed to itself
DHCP Enabled = No

Screenshots:

Lab-11-02-DC-ipconfig-all-Baseline.png
Lab-11-03-Server-Manager-AD-DNS-Baseline.png
Client Baseline

On CLIENT01, ipconfig /all confirmed:

static IPv4 address
DNS pointed to the domain controller
DHCP Enabled = No

Connectivity and DNS resolution were also tested before DHCP deployment.

Screenshots:

Lab-11-04-Client-ipconfig-all-Baseline.png
Lab-11-05-Client-Ping-and-NSLookup-Baseline.png
Baseline Result

At the start of the lab:

both systems were on the same subnet
DNS was functioning
the client could resolve and reach the domain controller
the environment was stable enough to introduce DHCP
Step 2 – Install the DHCP Server Role

The DHCP Server role was installed on MRTG-DC01 through Server Manager using the Add Roles and Features Wizard.

Installation Actions
selected DHCP Server
added required management tools
completed installation successfully

Screenshots:

Lab-11-06-Add-Roles-Wizard-DHCP-Selected.png
Lab-11-07-DHCP-Installation-Success.png
Lab-11-08-DHCP-Post-Install-Configuration.png
Why This Matters

In a Windows enterprise environment, DHCP helps ensure clients receive the correct network configuration required to locate DNS and directory services consistently.

Step 3 – Authorize DHCP in Active Directory

After installation, the DHCP Post-Install configuration wizard was used to authorize the DHCP server in Active Directory.

Authorization Actions
used domain administrative credentials
created required DHCP security groups
authorized MRTG-DC01 in AD
confirmed the DHCP console recognized the server

Screenshots:

Lab-11-09-DHCP-Configuration-Credentials.png
Lab-11-10-DHCP-Authorization-Success.png
Lab-11-11-DHCP-Console-Server-Visible.png
Why This Matters

Authorizing DHCP in Active Directory helps prevent unauthorized DHCP servers from distributing incorrect or malicious network settings inside a domain environment.

Step 4 – Create and Activate the IPv4 Scope

A new IPv4 scope was created to provide dynamic addressing for MRTG domain clients.

Scope Configuration
Scope Name: MRTG-Client-Scope
Description: DHCP scope for MRTG domain clients on 192.168.10.0/24
Start IP: 192.168.10.100
End IP: 192.168.10.200
Subnet Mask: 255.255.255.0
Exclusion Range: 192.168.10.150 - 192.168.10.160
Scope Options
Default Gateway: left blank
This matched the current isolated lab design and avoided inventing a router that did not actually exist.
Parent Domain: mrtg.local
DNS Server: 192.168.10.10
Scope Activation: enabled immediately

Screenshots:

Lab-11-12-New-Scope-Wizard-Name.png
Lab-11-13-New-Scope-IP-Range.png
Lab-11-14-New-Scope-Exclusions.png
Lab-11-15-New-Scope-Gateway-Option.png
Lab-11-16-New-Scope-DNS-Option.png
Lab-11-17-New-Scope-Activation-Summary.png
Lab-11-18-DHCP-IPv4-Scope-Active.png
Why This Matters

The DHCP scope defined the address pool and the critical domain-aware DNS configuration needed for clients to locate the domain controller and maintain identity services.

Step 5 – Convert CLIENT01 from Static IP to DHCP

After the scope was active, CLIENT01 was reconfigured to obtain its IP and DNS settings automatically.

Client Changes
set IPv4 to Obtain an IP address automatically
set DNS to Obtain DNS server address automatically
released old static configuration
renewed the lease from DHCP
Lease Result

After renewal, CLIENT01 received:

IPv4 Address: 192.168.10.101
Subnet Mask: 255.255.255.0
DHCP Server: 192.168.10.10
DNS Server: 192.168.10.10
DNS Suffix: mrtg.local

Screenshots:

Lab-11-19-Client-IPv4-Set-to-Automatic.png
Lab-11-20-Client-ipconfig-Renew-DHCP-Lease.png
Lab-11-21-Client-ipconfig-all-DHCP-Assigned.png
Lab-11-22-DHCP-Address-Lease-Visible.png
Why This Matters

This validated that DHCP was not only active, but also correctly distributing identity-aware network settings required by a domain-joined client.

Step 6 – Validate Active Directory Functionality After DHCP

After DHCP reassignment, CLIENT01 was tested to confirm that moving away from static addressing did not break domain services.

Validation Performed
pinged MRTG-DC01
resolved mrtg.local with nslookup
identified the domain controller with nltest /dsgetdc:mrtg.local
confirmed the logged-in session remained domain-based with whoami

Screenshots:

Lab-11-23-Client-Ping-DC-After-DHCP.png
Lab-11-24-Client-NSLookup-Domain-After-DHCP.png
Lab-11-25-Client-LogonServer-After-DHCP.png
Lab-11-26-Client-Domain-User-Session-After-DHCP.png
Validation Results
CLIENT01 could still resolve the domain controller by name
CLIENT01 could still reach MRTG-DC01
CLIENT01 successfully identified the domain controller in mrtg.local
the logged-in user session remained mrtg\kevin.carter
Security Perspective

In an Active Directory environment, DHCP is more than basic network automation. It is part of the identity infrastructure because clients depend on correct DNS and network configuration to:

locate domain controllers
authenticate users
apply Group Policy
maintain directory communication
support centralized logging and future enterprise controls

A misconfigured DHCP environment can break authentication, name resolution, and trust in the client-to-directory relationship very quickly.

Notes and Observations
1. No Default Gateway

No default gateway was configured in the scope because the current lab network was intentionally operating as an isolated, same-subnet environment. This was acceptable for validating DHCP support for AD and DNS.

2. NSLookup “Server: UnKnown”

nslookup successfully resolved mrtg.local, but the output showed:

DNS request timed out
Server: UnKnown

This did not break the lab. The domain still resolved correctly through 192.168.10.10. This likely points to a reverse lookup / PTR cleanup issue, not a DHCP failure.

3. Stray Lease Observation

A non-lab device briefly appeared in the DHCP lease list during testing. That indicated the lab network was not fully isolated at that moment. The final documentation was cleaned up to show only the intended lab client lease.

Outcome

Successfully installed and authorized DHCP on MRTG-DC01, created and activated an IPv4 scope for MRTG domain clients, configured DNS scope options to support mrtg.local, and validated that CLIENT01 received a DHCP lease while maintaining full Active Directory connectivity and domain functionality.

Key Takeaways
Domain controllers should remain statically addressed
DHCP clients in AD environments must receive the correct DNS server
DHCP supports identity infrastructure by enabling clients to reliably locate directory services
Good DHCP design depends on accurate scope definition, clean exclusions, and honest network architecture
Next Lab

Lab 12 – Additional Domain Controller and AD Replication

This next lab builds on the now-stable DHCP and DNS-backed identity environment by introducing directory redundancy, replication, and improved operational resilience.
