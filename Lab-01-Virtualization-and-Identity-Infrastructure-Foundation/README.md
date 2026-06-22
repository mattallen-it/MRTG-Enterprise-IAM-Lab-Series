# Lab 01: Virtualization and Identity Infrastructure Foundation

![Platform](https://img.shields.io/badge/Platform-Hyper--V-blue)
![Technology](https://img.shields.io/badge/Technology-Virtualization-blue)
![Host](https://img.shields.io/badge/Host-Windows%2011%20Pro-lightgrey)
![Focus](https://img.shields.io/badge/Focus-Infrastructure%20Foundation-green)
![Security](https://img.shields.io/badge/Security-Lab%20Isolation-red)
![Validation](https://img.shields.io/badge/Validation-Hyper--V%20Ready-brightgreen)

---

## Objective

Establish the virtualization and infrastructure foundation for the Monroe Redstone Technology Group IAM lab environment.

This lab validates the Windows 11 Pro host, enables Hyper-V, creates an isolated virtual network, organizes virtual machine storage, prepares installation media, and creates the first Windows Server virtual machine.

---

## Business Scenario

Monroe Redstone Technology Group requires a controlled and repeatable environment for building and validating identity infrastructure.

Before implementing Active Directory, Group Policy, access controls, security monitoring, or IAM governance, the organization must establish a stable virtualization foundation.

This lab addresses the need to:

- Validate host hardware and operating system readiness
- Review TPM and BitLocker security status
- Confirm CPU virtualization support
- Enable Hyper-V and its management tools
- Create an isolated virtual network
- Organize virtual machine and virtual disk storage
- Prepare Windows Server installation media
- Create the first Windows Server virtual machine
- Establish a repeatable foundation for future IAM labs

---

## Lab Summary

In this lab, I prepared the virtualization foundation for the MRTG IAM lab series.

The Windows 11 Pro host was validated for 64-bit architecture, available memory, TPM readiness, BitLocker protection, and CPU virtualization support.

Hyper-V was enabled, an internal virtual switch was created, dedicated storage folders were configured, and the Windows Server 2022 ISO was staged.

The first server virtual machine, `MRTG-DC01`, was then created and connected to the internal lab network.

---

## Environment

| Component | Details |
|---|---|
| Host Operating System | Windows 11 Pro |
| Host Version | 25H2 |
| Processor | AMD Ryzen 5 7600X3D |
| Installed RAM | 32 GB |
| System Type | 64-bit operating system, x64-based processor |
| Hypervisor | Hyper-V |
| Lab Storage Drive | `D:\LABS` |
| Virtual Switch | `MRTG-Internal` |
| First VM | `MRTG-DC01` |
| Installation Media | Windows Server 2022 ISO |
| Organization | Monroe Redstone Technology Group |

---

## Prerequisites

- Windows 11 Pro host
- Administrative access to the host
- CPU virtualization enabled in firmware
- Sufficient memory and storage for virtual machines
- Windows Server 2022 installation media
- Dedicated storage location for lab files

---

## Scope

### Included

- Host hardware and operating system review
- System architecture validation
- TPM readiness validation
- BitLocker status validation
- CPU virtualization validation
- Hyper-V feature validation
- Hyper-V Manager validation
- Internal virtual switch creation
- Lab storage structure preparation
- Hyper-V default storage path configuration
- Windows Server ISO staging
- `MRTG-DC01` virtual machine creation
- Initial VM inventory validation

### Not Included

- Windows Server installation
- Active Directory Domain Services installation
- Domain controller promotion
- DNS or DHCP configuration
- Domain join validation
- Group Policy configuration
- User or group provisioning
- Logging or SIEM configuration

---

## Host Readiness

| Requirement | Result |
|---|---|
| Windows 11 Pro | Confirmed |
| 64-bit operating system | Confirmed |
| x64-based processor | Confirmed |
| 32 GB RAM | Confirmed |
| CPU virtualization | Enabled |
| TPM | Ready for use |
| BitLocker | Enabled on OS drive |
| Hyper-V | Installed |

The results confirmed that the host could support the planned Hyper-V lab environment.

TPM readiness and BitLocker protection were reviewed as host security controls. They are not requirements for running Hyper-V.

---

## Lab Network Design

An internal Hyper-V virtual switch was created for isolated communication between the lab systems.

| Network Component | Purpose |
|---|---|
| `MRTG-Internal` | Provides isolated communication between lab virtual machines |
| `MRTG-DC01` | Future primary domain controller |
| `MRTG-CLIENT-01` | Future domain-joined workstation |
| `MRTG-DC02` | Future additional domain controller |
| `MRTG-LOG01` | Future logging and monitoring server |

The internal switch separates lab traffic from the physical network while allowing the virtual machines to communicate with one another.

---

## System Naming Standard

| System | Planned Role |
|---|---|
| `MRTG-DC01` | Primary domain controller |
| `MRTG-DC02` | Additional domain controller |
| `MRTG-CLIENT-01` | Domain-joined workstation |
| `MRTG-LOG01` | Logging and monitoring server |

The `MRTG` prefix identifies systems belonging to Monroe Redstone Technology Group and keeps the lab inventory organized.

---

## Storage Design

The lab storage structure was prepared on the `D:` drive.

```text
D:\LABS
|-- HyperV
|   |-- VirtualHardDisks
|   `-- VirtualMachines
`-- ISO
    `-- Windows_Server_2022.iso
```

This structure separates virtual machine configuration files, virtual hard disks, and installation media.

---

## Virtual Machine Configuration

| Setting | Value |
|---|---|
| VM Name | `MRTG-DC01` |
| Generation | Generation 2 |
| Startup Memory | 8192 MB |
| Network | `MRTG-Internal` |
| Virtual Hard Disk | `D:\LABS\HyperV\VirtualHardDisks\MRTG-DC01.vhdx` |
| Installation Media | `D:\LABS\ISO\Windows_Server_2022.iso` |

---

## Infrastructure Design Principles

- Isolate the lab from personal and production systems
- Use consistent system naming
- Separate server and client roles
- Store lab resources in predictable locations
- Build infrastructure in controlled phases
- Maintain controlled lab recovery points
- Document major configuration changes
- Collect evidence for validation and troubleshooting

---

## Implementation and Validation

### 1. Reviewed Host Hardware

The host hardware was reviewed to confirm sufficient resources for the planned lab environment.

Validated hardware included:

- AMD Ryzen 5 7600X3D processor
- 32 GB RAM

![Host hardware specifications](screenshots/lab-01-01_host_hardware_specs.png)

---

### 2. Validated System Architecture

The system type was reviewed to confirm that the host was running a 64-bit operating system on an x64-based processor.

![Host system architecture](screenshots/lab-01-02_host_system_architecture.png)

This confirmed compatibility with Hyper-V virtualization.

---

### 3. Confirmed the Host Operating System

The host operating system was validated as:

```text
Windows 11 Pro
Version 25H2
```

![Host operating system version](screenshots/lab-01-03_host_os_version.png)

Windows 11 Pro includes support for Hyper-V.

---

### 4. Validated TPM Readiness

TPM Management was opened to confirm that the Trusted Platform Module was ready for use.

![TPM ready](screenshots/lab-01-04-tpm-ready.png)

This verified that the host supported modern platform security capabilities.

---

### 5. Confirmed BitLocker Protection

BitLocker Drive Encryption was reviewed on the operating system drive.

```text
C: BitLocker on
```

![BitLocker enabled](screenshots/lab-01-05_bitlocker_enabled.png)

This confirmed that the host operating system drive was encrypted.

---

### 6. Confirmed CPU Virtualization

Task Manager was used to verify that CPU virtualization was enabled.

```text
Virtualization: Enabled
```

![CPU virtualization enabled](screenshots/lab-01-06-cpu-virtualization-enabled.png)

This confirmed that the processor was ready to support Hyper-V virtual machines.

---

### 7. Validated the Hyper-V Feature

Windows Features confirmed that Hyper-V was enabled.

Validated components included:

- Hyper-V Management Tools
- Hyper-V Platform

![Hyper-V installed](screenshots/lab-01-07_hyperv_installed.png)

---

### 8. Opened Hyper-V Manager

Hyper-V Manager opened successfully.

![Hyper-V Manager console](screenshots/lab-01-08_hyperv_manager_console.png)

This confirmed that the management tools were available for virtual machine administration.

---

### 9. Created the Internal Virtual Switch

An internal Hyper-V virtual switch was created for lab communication.

```text
MRTG-Internal
```

![Internal network created](screenshots/lab-01-09_internal_network_created.png)

This provided isolated connectivity for the MRTG virtual machines.

---

### 10. Created the Lab Folder Structure

A dedicated storage structure was created under `D:\LABS`.

![Hyper-V folder structure](screenshots/lab-01-10-hyperv-folder-structure.png)

This organized virtual machine files, virtual hard disks, and installation media.

---

### 11. Configured Default Storage Paths

Hyper-V default storage paths were configured to use the dedicated lab folders.

![Hyper-V default storage paths](screenshots/lab-01-11-hyperv-default-storage-paths.png)

This ensured that new virtual machines and virtual hard disks would be stored in the intended location.

---

### 12. Prepared the Windows Server ISO

The Windows Server 2022 ISO was placed in the lab ISO folder.

![Windows Server ISO ready](screenshots/lab-01-12-windows-server-iso-ready.png)

This prepared the installation media for the first server virtual machine.

---

### 13. Configured MRTG-DC01

The New Virtual Machine Wizard was used to configure `MRTG-DC01`.

Validated settings included:

- Generation 2
- 8192 MB startup memory
- `MRTG-Internal` network connection
- VHDX stored under `D:\LABS\HyperV\VirtualHardDisks`
- Windows Server 2022 ISO attached

![MRTG-DC01 VM configuration](screenshots/lab-01-13-dc01-vm-configuration.png)

---

### 14. Created and Started MRTG-DC01

`MRTG-DC01` was created and displayed as running in Hyper-V Manager.

![MRTG-DC01 created](screenshots/lab-01-14-mrtg-dc01-created.png)

This confirmed that the first server VM was ready for operating system installation.

---

## Security and IAM Relevance

Identity infrastructure depends on the systems that host and protect it.

This lab established several controls that support the future IAM environment:

- Isolated lab communication
- Verified host security posture
- BitLocker-protected host storage
- TPM readiness
- Controlled infrastructure deployment
- Consistent system naming
- Defined server and client roles
- Repeatable VM configuration
- Evidence-based validation

A stable virtualization foundation reduces configuration drift and provides a controlled environment for testing identity and security changes.

---

## Risks Addressed

This lab reduces the risk of:

- Uncontrolled lab growth
- Inconsistent system naming
- Insufficient host resources
- Poor separation between server and client roles
- Disorganized VM storage
- Incorrect virtual network configuration
- Weak documentation
- Difficulty reproducing or troubleshooting later configurations

---

## Control Mapping

| Control Area | Lab Contribution |
|---|---|
| Infrastructure Foundation | Establishes the virtual environment for future IAM labs |
| Host Readiness | Validates the operating system, hardware, virtualization, TPM, and BitLocker status |
| Security Isolation | Uses an internal Hyper-V switch for lab communication |
| Operational Consistency | Establishes standard names, roles, and storage locations |
| Change Readiness | Creates a controlled base for phased configuration |
| Documentation | Captures implementation and validation evidence |
| Audit Readiness | Establishes screenshot-based evidence collection |

---

## Validation Results

| Validation Item | Result |
|---|---|
| Host hardware reviewed | Passed |
| Windows 11 Pro confirmed | Passed |
| 64-bit system architecture confirmed | Passed |
| TPM ready for use | Passed |
| BitLocker enabled on OS drive | Passed |
| CPU virtualization enabled | Passed |
| Hyper-V installed | Passed |
| Hyper-V Manager opened | Passed |
| Internal virtual switch created | Passed |
| Lab folder structure created | Passed |
| Hyper-V storage paths configured | Passed |
| Windows Server 2022 ISO staged | Passed |
| `MRTG-DC01` configured | Passed |
| `MRTG-DC01` created and started | Passed |

---

## Evidence Collected

| Evidence | File |
|---|---|
| Host hardware specifications | `screenshots/lab-01-01_host_hardware_specs.png` |
| Host system architecture | `screenshots/lab-01-02_host_system_architecture.png` |
| Host operating system version | `screenshots/lab-01-03_host_os_version.png` |
| TPM readiness | `screenshots/lab-01-04-tpm-ready.png` |
| BitLocker protection | `screenshots/lab-01-05_bitlocker_enabled.png` |
| CPU virtualization | `screenshots/lab-01-06-cpu-virtualization-enabled.png` |
| Hyper-V installation | `screenshots/lab-01-07_hyperv_installed.png` |
| Hyper-V Manager | `screenshots/lab-01-08_hyperv_manager_console.png` |
| Internal virtual network | `screenshots/lab-01-09_internal_network_created.png` |
| Hyper-V folder structure | `screenshots/lab-01-10-hyperv-folder-structure.png` |
| Hyper-V storage paths | `screenshots/lab-01-11-hyperv-default-storage-paths.png` |
| Windows Server ISO | `screenshots/lab-01-12-windows-server-iso-ready.png` |
| MRTG-DC01 configuration | `screenshots/lab-01-13-dc01-vm-configuration.png` |
| MRTG-DC01 creation | `screenshots/lab-01-14-mrtg-dc01-created.png` |

---

## What I Would Improve in Production

In a production environment, I would:

- Create formal infrastructure and network design documentation
- Define compute, memory, storage, and capacity requirements before deployment
- Document network segmentation and firewall requirements
- Use organization-approved naming standards
- Track systems and ownership in a configuration management database
- Apply approved security baselines before production use
- Define backup, recovery, and availability requirements
- Validate licensing and vendor support requirements
- Use formal change management
- Monitor host capacity and performance
- Treat hypervisor checkpoints as temporary rollback tools rather than backups

---

## Lessons Learned

This lab reinforced that identity infrastructure begins before Active Directory is installed.

System naming, virtual networking, storage organization, host readiness, and evidence collection directly affect the stability and maintainability of the identity environment.

The primary takeaway is that a structured foundation makes future IAM systems easier to build, troubleshoot, validate, and recover.

---

## Outcome

Lab 01 successfully established the virtualization and infrastructure foundation for the MRTG IAM lab series.

The lab confirmed that:

- The Windows 11 Pro host supports Hyper-V
- CPU virtualization is enabled
- TPM is ready for use
- BitLocker protects the host operating system drive
- Hyper-V is installed and manageable
- The `MRTG-Internal` virtual switch is available
- Lab storage is organized
- Windows Server 2022 installation media is prepared
- `MRTG-DC01` is created as the first server virtual machine

The environment is ready for Windows Server installation and Active Directory Domain Services preparation.

---

## Next Lab

[Lab 02: AD DS Deployment Preparation](../Lab-02-AD-DS-Deployment-Preparation/)

Lab 02 installs Windows Server, configures the base operating system, assigns the server identity, and prepares `MRTG-DC01` for domain controller promotion.
