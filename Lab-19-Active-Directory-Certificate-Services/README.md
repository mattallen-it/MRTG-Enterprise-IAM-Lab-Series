# Lab 19 — Active Directory Certificate Services

![Platform](https://img.shields.io/badge/Platform-Windows%20Server-blue)
![Technology](https://img.shields.io/badge/Technology-Active%20Directory%20Certificate%20Services-blue)
![Tooling](https://img.shields.io/badge/Tooling-Certification%20Authority%20%26%20CertMgr-purple)
![Focus](https://img.shields.io/badge/Focus-Enterprise%20PKI-orange)
![Security](https://img.shields.io/badge/Security-Internal%20Trust%20Services-red)
![Validation](https://img.shields.io/badge/Validation-Certificate%20Issued%20%26%20Trusted-brightgreen)

## Objective

The objective of this lab was to install, configure, troubleshoot, and validate Active Directory Certificate Services in the MRTG enterprise lab environment.

This lab focused on building an internal Enterprise Root Certification Authority, integrating certificate services with Active Directory, validating available certificate templates, enrolling a user certificate from a domain-joined workstation, confirming the issued certificate on the CA, and verifying that the internal root CA was trusted by the client.

## Scope

This lab included:

- Creating a pre-lab Hyper-V checkpoint
- Installing the Active Directory Certificate Services role
- Selecting the Certification Authority role service
- Configuring an Enterprise Root CA
- Creating a new CA private key
- Configuring CA cryptography settings
- Naming the internal CA
- Setting the CA validity period
- Using the default CA database location
- Validating the Certification Authority console
- Validating certificate templates on the CA
- Opening the Current User certificate store
- Testing Active Directory Enrollment Policy
- Troubleshooting certificate template availability
- Validating domain controller discovery
- Validating logon server connectivity
- Enrolling a user certificate
- Confirming the certificate in the Current User Personal store
- Confirming the issued certificate on the CA
- Confirming root CA trust on the client
- Creating post-lab checkpoints

## Environment

| Component | Details |
|---|---|
| Domain | `mrtg.local` |
| Certification Authority | `MRTG-DC01-CA` |
| CA Server | `MRTG-DC01` |
| Domain Controller | `MRTG-DC01` |
| Additional Domain Controller | `MRTG-DC02` |
| Client Workstation | `MRTG-CLIENT-01` |
| Test User | `mrtg\mike.chen` |
| CA Type | Enterprise Root CA |
| Hash Algorithm | `SHA256` |
| Key Length | `2048` |
| Certificate Store Tested | Current User Personal Store |
| CA Database Path | `C:\Windows\System32\CertLog` |

## Scenario

Monroe Redstone Technology Group needed an internal certificate authority to support enterprise trust services.

Certificates are used to establish trust for users, computers, services, authentication, encryption, and secure communication. This lab introduced Active Directory Certificate Services as the foundation for future identity and security labs involving certificate-based authentication and trusted internal services.

The certificate trust path tested in this lab was:

```text
Domain User → Active Directory Enrollment Policy → User Certificate Template → MRTG-DC01-CA → Issued Certificate
```

## Design Notes

For this lab, `MRTG-DC01` was configured as an Enterprise Root CA.

This is acceptable in a controlled lab environment because the purpose was to learn the AD CS deployment workflow, understand how certificate templates are exposed through Active Directory, and validate user certificate enrollment from a domain-joined workstation.

In a production or government-regulated environment, a stronger design would usually separate certificate authority roles and use a more formal PKI hierarchy.

A stronger production model would likely use:

```text
Offline Root CA → Online Issuing/Subordinate CA
```

This reduces risk by protecting the root CA and limiting exposure of the issuing CA.

## Implementation Steps

### 1. Created Pre-Lab Checkpoint

A Hyper-V checkpoint was created before installing Active Directory Certificate Services.

Checkpoint name:

```text
Pre-Lab-19-ADCS
```

![Pre-Lab AD CS Checkpoint](images/lab-19-01-pre-lab-adcs-checkpoint.png)

### 2. Selected the Active Directory Certificate Services Role

The Active Directory Certificate Services role was selected on `MRTG-DC01`.

![AD CS Role Selected](images/lab-19-02-adcs-role-selected.png)

### 3. Selected the Certification Authority Role Service

Only the Certification Authority role service was selected for this lab.

Additional services such as Web Enrollment, Online Responder, and Network Device Enrollment Service were not selected because this lab focused on the core CA deployment.

![Certification Authority Role Service Selected](images/lab-19-03-certification-authority-role-service-selected.png)

### 4. Completed AD CS Role Installation

The AD CS role installation completed successfully.

Server Manager indicated that additional post-deployment configuration was required.

![AD CS Installation Complete](images/lab-19-04-adcs-installation-complete.png)

### 5. Verified AD CS Configuration Requirement

Server Manager displayed the post-deployment configuration warning for AD CS.

![Server Manager AD CS Configuration Required](images/lab-19-05-server-manager-adcs-configuration-required.png)

### 6. Selected Enterprise CA

The CA was configured as an Enterprise CA.

This allowed the CA to integrate with Active Directory and use AD-based certificate enrollment policies and certificate templates.

![Enterprise CA Selected](images/lab-19-06-enterprise-ca-selected.png)

### 7. Selected Root CA

The CA was configured as a Root CA.

This made `MRTG-DC01-CA` the top-level certificate authority in the MRTG lab PKI hierarchy.

![Root CA Selected](images/lab-19-07-root-ca-selected.png)

### 8. Created a New Private Key

A new private key was created for the CA.

![Create New Private Key Selected](images/lab-19-08-create-new-private-key-selected.png)

### 9. Configured Cryptography Settings

The CA was configured with the following cryptographic settings:

| Setting | Value |
|---|---|
| Provider | `RSA#Microsoft Software Key Storage Provider` |
| Key Length | `2048` |
| Hash Algorithm | `SHA256` |

![Cryptography Settings Configured](images/lab-19-09-cryptography-settings-configured.png)

### 10. Configured CA Name

The CA was named:

```text
MRTG-DC01-CA
```

The distinguished name was:

```text
CN=MRTG-DC01-CA,DC=mrtg,DC=local
```

![CA Name Configured](images/lab-19-10-ca-name-configured.png)

### 11. Configured CA Validity Period

The CA validity period was configured for 5 years.

![CA Validity Period Configured](images/lab-19-11-ca-validity-period-configured.png)

### 12. Configured Certificate Database Location

The default certificate database and log locations were used:

```text
C:\Windows\System32\CertLog
```

![Certificate Database Location Configured](images/lab-19-12-certificate-database-location-configured.png)

### 13. Confirmed AD CS Configuration Settings

The final AD CS configuration was reviewed before applying the settings.

![AD CS Configuration Confirmation](images/lab-19-13-adcs-configuration-confirmation.png)

### 14. Completed AD CS Configuration

The Certification Authority configuration completed successfully.

![AD CS Configuration Successful](images/lab-19-14-adcs-configuration-successful.png)

## Validation

### Certification Authority Console Validated

The Certification Authority console was opened on `MRTG-DC01`.

The CA was visible as:

```text
MRTG-DC01-CA
```

The following CA containers were present:

```text
Revoked Certificates
Issued Certificates
Pending Requests
Failed Requests
Certificate Templates
```

![Certification Authority Console](images/lab-19-15-certification-authority-console.png)

### Certificate Templates Validated

Default certificate templates were visible under the CA.

This confirmed that the Enterprise CA was integrated with Active Directory certificate templates.

![Certificate Templates Visible](images/lab-19-16-certificate-templates-visible.png)

### Current User Certificate Store Opened

On `MRTG-CLIENT-01`, the Current User certificate store was opened using:

```text
certmgr.msc
```

The Personal certificate store was initially empty.

![Current User Certificate Store Opened](images/lab-19-17-current-user-certificate-store-opened.png)

### Active Directory Enrollment Policy Selected

The domain user was able to access the Active Directory Enrollment Policy from the certificate enrollment wizard.

![Active Directory Enrollment Policy Selected](images/lab-19-18-active-directory-enrollment-policy-selected.png)

## Troubleshooting

### Certificate Types Initially Unavailable

During the first enrollment attempt, no certificate templates were available to the user.

![Certificate Types Not Available](images/lab-19-19-certificate-types-not-available.png)

This was treated as a certificate enrollment troubleshooting point.

Certificate enrollment depends on the workstation being able to contact a domain controller, retrieve Active Directory enrollment policy, and locate available certificate templates.

### Domain Connectivity and Logon Server Validated

Domain controller discovery was validated from `MRTG-CLIENT-01` using:

```cmd
nltest /dsgetdc:mrtg.local
```

The client successfully discovered a domain controller:

```text
MRTG-DC02.mrtg.local
```

The logon server was also validated using:

```cmd
echo %logonserver%
```

The user session was authenticated through:

```text
\\MRTG-DC01
```

![Domain Connectivity and Logon Server Validated](images/lab-19-20-domain-connectivity-and-logon-server-validated.png)

### User Certificate Template Became Available

After validating domain connectivity and refreshing the enrollment path, certificate templates became available.

The available certificate templates included:

```text
Basic EFS
User
```

![User Certificate Template Available](images/lab-19-21-user-certificate-template-available.png)

### User Certificate Enrollment Successful

The `User` certificate template was selected and successfully enrolled.

![User Certificate Enrollment Successful](images/lab-19-22-user-certificate-enrollment-successful.png)

### User Certificate Installed in Personal Store

The issued certificate appeared in the Current User Personal certificate store.

The certificate details showed:

| Field | Value |
|---|---|
| Issued To | `Mike Chen` |
| Issued By | `MRTG-DC01-CA` |
| Certificate Template | `User` |
| Store | Current User → Personal → Certificates |

![User Certificate Installed in Personal Store](images/lab-19-23-user-certificate-installed-in-personal-store.png)

### Issued Certificate Visible on CA

The issued certificate was visible in the Certification Authority console under Issued Certificates.

The CA showed the request from:

```text
MRTG\mike.chen
```

![Issued Certificate Visible on CA](images/lab-19-24-issued-certificate-visible-on-ca.png)

### Root CA Trusted on Client

The root CA certificate was visible in the client trusted root store.

Path validated:

```text
Trusted Root Certification Authorities → Certificates
```

The trusted root certificate showed:

```text
MRTG-DC01-CA
```

![Root CA Trusted on Client](images/lab-19-25-root-ca-trusted-on-client.png)

## Validation Summary

| Test | Expected Result | Actual Result | Status |
|---|---|---|---|
| AD CS role installed | Role installs successfully | Installation succeeded | Passed |
| CA configured | Enterprise Root CA configured | Configuration succeeded | Passed |
| CA console opens | `MRTG-DC01-CA` visible | CA visible in console | Passed |
| Certificate templates visible | Templates available on CA | Templates visible | Passed |
| Current User certificate store opens | User certificate store accessible | Store opened successfully | Passed |
| Initial enrollment behavior tested | Enrollment policy checked | No certificate types were initially available | Documented |
| Domain controller discovery works | Client can locate a DC | `MRTG-DC02.mrtg.local` discovered | Passed |
| Logon server validates | User session shows logon server | `\\MRTG-DC01` confirmed | Passed |
| User certificate template available | User template selectable | User template became available | Passed |
| User certificate enrollment succeeds | Certificate enrollment completes | User certificate enrolled | Passed |
| Certificate appears in user store | Certificate installed locally | Certificate present in Personal store | Passed |
| Certificate appears on CA | Issued certificate visible | Certificate visible under Issued Certificates | Passed |
| Root CA trusted by client | Root CA in trusted store | `MRTG-DC01-CA` trusted | Passed |

## Post-Lab Checkpoints

A post-lab checkpoint was created for `MRTG-DC01` after the Enterprise Root CA was configured and validated.

Checkpoint name:

```text
MRTG-DC01_Post-Lab-19-ADCS-Enterprise-Root-CA-Validated
```

![Post-Lab DC01 Checkpoint](images/lab-19-26-post-lab-dc01-checkpoint.png)

A post-lab checkpoint was also created for `MRTG-CLIENT-01` after user certificate enrollment was validated.

Checkpoint name:

```text
Post-Lab-19-User-Certificate-Enrollment-Validated
```

![Post-Lab Client Checkpoint](images/lab-19-27-post-lab-client-checkpoint.png)

## Outcome

Lab 19 successfully installed, configured, troubleshot, and validated Active Directory Certificate Services in the MRTG enterprise lab environment.

The final configuration established `MRTG-DC01-CA` as an Enterprise Root CA. A domain user successfully accessed Active Directory Enrollment Policy, selected the User certificate template, enrolled a certificate, confirmed the issued certificate in the Current User Personal certificate store, verified the certificate on the CA, and confirmed that the MRTG root CA was trusted by the client.

This confirmed that the MRTG domain now has a working internal certificate authority capable of issuing trusted certificates to domain users.

## Skills Demonstrated

- Active Directory Certificate Services installation
- Enterprise Root CA configuration
- Certification Authority console usage
- Certificate template validation
- User certificate enrollment
- Current User certificate store validation
- Trusted Root Certification Authorities validation
- CA-side issued certificate validation
- Domain controller discovery using `nltest`
- Logon server validation
- Certificate enrollment troubleshooting
- PKI documentation and evidence capture
- IAM-focused trust service validation

## Real-World Relevance

Active Directory Certificate Services is a foundational enterprise trust service.

In real environments, certificates support secure authentication, encryption, device identity, service identity, smart card authentication, Wi-Fi authentication, VPN authentication, LDAPS, code signing, and secure internal web services.

For IAM and government-regulated IT environments, certificate services matter because certificates are tied directly to trust, identity assurance, authentication strength, and secure access control.

This lab connects directly to:

- Enterprise PKI
- Identity trust
- Certificate-based authentication
- Domain-integrated enrollment
- Device and user identity validation
- Secure access infrastructure
- Audit-ready certificate issuance

## Lessons Learned

- Installing AD CS is only the first step; certificate issuance must be validated.
- Enterprise CAs integrate with Active Directory enrollment policy.
- Certificate templates control what users and computers can request.
- Domain connectivity and policy refresh are required for certificate enrollment.
- A certificate should be validated from both the client side and the CA side.
- Trusted root validation confirms that the client trusts certificates issued by the internal CA.
- Troubleshooting failed enrollment is valuable because PKI depends on AD, DNS, policy, and trust working together.
- In production, CA design should be planned carefully and should not be treated like a basic server role.

## What I Would Do Differently

In a production or government-regulated environment, I would not casually place an Enterprise Root CA directly on a domain controller.

A stronger design would include:

- Offline Root CA
- Online Issuing/Subordinate CA
- Dedicated CA servers
- Formal certificate policy and certificate practice statements
- Stronger key length and lifecycle planning based on policy
- CA backup and recovery procedures
- Certificate revocation planning
- Role separation for CA administration
- Monitoring and auditing of certificate issuance

For this lab, using `MRTG-DC01` was acceptable because the goal was to understand AD CS installation, certificate enrollment, and domain trust behavior in a controlled environment.

## Next Lab

[**Lab 20 — Identity Lifecycle Automation with PowerShell**](../Lab-20-Identity-Lifecycle-Automation-with-PowerShell/)

Lab 20 will build on this enterprise identity foundation by focusing on PowerShell-based identity lifecycle automation, including user creation, account updates, group membership changes, and repeatable IAM administration workflows.
