# Lab-01 — Virtualization and Identity Infrastructure Foundation

![type: lab](https://img.shields.io/badge/type-lab-blue) ![focus: identity infrastructure](https://img.shields.io/badge/focus-identity%20infrastructure-green) ![platform: Hyper-V](https://img.shields.io/badge/platform-Hyper--V-0078D6) ![os: Windows 11](https://img.shields.io/badge/os-Windows%2011-lightgrey) ![domain: identity](https://img.shields.io/badge/domain-identity-informational) ![level: foundation](https://img.shields.io/badge/level-foundation-lightgrey)

Establish a secure and scalable identity infrastructure foundation by preparing a virtualization environment to support Active Directory Domain Services for MRTG.

---

## Organization

Monroe Redstone Technology Group (MRTG)

## Lab Scope

This foundational lab combines three infrastructure preparation stages:

• Host virtualization readiness  
• Hyper-V infrastructure configuration  
• Windows Server preparation for Active Directory Domain Controller deployment

These steps establish the environment required for building and managing an Active Directory identity infrastructure.

---

## Lab Environment

### Host Machine
- Windows 11 Pro
- AMD Ryzen 5 7600X3D
- 32 GB RAM

### Virtualization Platform
- Hyper-V

### Domain (Planned)

### Virtual Machine

- Name: MRTG-DC01  
- Operating System: Windows Server 2022 Standard (Desktop Experience)  
- Generation: Gen 2  
- Memory: 8 GB (8192 MB)  
- vCPU: 2  
- Disk: 80 GB dynamically expanding VHDX  
- Network: MRTG-Internal (Internal Virtual Switch) 

---

## Architecture

- Host System (Windows 11 Pro)
  - Hyper-V enabled
  - BitLocker enabled

- Virtualization Layer
  - Hyper-V Manager
  - Internal Virtual Switch (for isolated lab network)

- Intended Role: Domain Controller (Active Directory Domain Services, DNS)
- Will serve as centralized identity provider for MRTG environment

This architecture establishes an isolated identity boundary for MRTG’s Active Directory infrastructure.

---

## Security Considerations

- Enforced separation between host system and lab environment to prevent privilege crossover
- Hyper-V used as an isolation boundary for identity infrastructure testing
- BitLocker verified to ensure host-level data protection
- Standard user account configured for daily operations, with administrative access reserved for privileged tasks
- Identity infrastructure is intentionally isolated to simulate enterprise trust boundaries and reduce attack surface

# Lab Steps and Evidence

### 1. Verified Host Hardware Specifications

The host system hardware was validated to ensure sufficient CPU and memory resources for running virtualization workloads.

![Hardware Specs](screenshots/01_host_hardware_specs.png)

---

### 2. Confirmed System Architecture

The system architecture was verified to ensure a 64-bit operating system capable of supporting Hyper-V virtualization.

![System Architecture](screenshots/02_host_system_architecture.png)

---

### 3. Verified Host Operating System Version

The host system is running **Windows 11 Pro**, which supports Hyper-V virtualization and enterprise security features.

![Host OS](screenshots/03_host_os_version.png)

---

### 4. Verified TPM Availability

Trusted Platform Module (TPM) availability was confirmed to support hardware-based security features such as BitLocker.

![TPM Ready](screenshots/04_tpm_ready.png)

---

### 5. Confirmed BitLocker Encryption

BitLocker encryption was verified on the operating system drive to ensure the host system meets basic security requirements for infrastructure hosting.

![BitLocker Enabled](screenshots/05_bitlocker_enabled.png)

---

### 6. Verified CPU Virtualization Support

CPU virtualization support was confirmed to ensure the processor can support Hyper-V virtual machines.

![CPU Virtualization](screenshots/06_cpu_virtualization_enabled.png)

---

### 7. Confirmed Hyper-V Installation

Hyper-V was verified as installed with both the **Hyper-V platform** and **management tools** enabled.

![HyperV Installed](screenshots/07_hyperv_installed.png)

---

### 8. Opened Hyper-V Manager

Hyper-V Manager was launched to begin configuring the virtualization environment.

![HyperV Manager](screenshots/08_hyperv_manager_console.png)

---

### 9. Created Internal Lab Network

A dedicated **internal virtual switch** was created to isolate the lab network from the host network environment.

This allows domain services and authentication testing without affecting external systems.

![Internal Network](screenshots/09_internal_network_created.png)

---

### 10. Created Hyper-V Lab Folder Structure

A dedicated folder structure was created on the **LABS drive** to organize virtual machine files and virtual hard disks.

This structure separates infrastructure resources from the host operating system.

![Folder Structure](screenshots/10_HyperV_Folder_Structure.png)

---

### 11. Configured Hyper-V Storage Paths

Hyper-V default storage locations were configured to use the dedicated lab directories.

This ensures consistent storage management for future virtual machines.

![Storage Paths](screenshots/11_HyperV_Default_Storage_Paths.png)

---

### 12. Prepared Windows Server 2022 Installation Media

The Windows Server 2022 ISO image was downloaded and staged for deployment as the domain controller.

![Windows Server ISO](screenshots/12_Windows_Server_ISO_Ready.png)

---

### 13. Configured Domain Controller Virtual Machine

A new virtual machine named **MRTG-DC01** was created with the following configuration:

- Generation 2
- 8192 MB RAM (8 GB)
- 2 vCPU
- Internal virtual network (MRTG-Internal)
- 80 GB dynamically expanding VHDX
- Windows Server 2022 ISO attached for installation

This configuration establishes the baseline system that will later be promoted to a Domain Controller for centralized identity management.

![DC01 Config](screenshots/13_DC01_VM_Configuration.png)

---

### 14. Created the MRTG-DC01 Virtual Machine

The MRTG-DC01 virtual machine was successfully created within the Hyper-V environment and is ready for Windows Server installation.

![DC01 Created](screenshots/14_MRTG_DC01_Created.png)

---

## Outcome

A Windows Server 2022 virtual machine (**MRTG-DC01**) was successfully deployed within an isolated Hyper-V environment.

This system establishes the foundational identity infrastructure layer and will be promoted to a Domain Controller in the next phase.

The environment now supports the deployment of Active Directory Domain Services and centralized authentication within a controlled internal network.

---

## Next Lab

[Lab 02 — Active Directory Domain Services (AD DS) Deployment](../Lab-02-Active-Directory-Domain-Services-Deployment/README.md)

The next lab will cover:

- Installing the Active Directory Domain Services (AD DS) role
- Creating a new domain forest
- Configuring DNS for the domain environment
- Promoting the server to a Domain Controller
