# Lab-09: Password Policy and Account Lockout Hardening

![Platform](https://img.shields.io/badge/Platform-Windows%20Server%202022-blue)
![Directory](https://img.shields.io/badge/Directory-Active%20Directory-darkgreen)
![Focus](https://img.shields.io/badge/Focus-Password%20Policy%20%26%20Account%20Lockout-purple)


## Overview

In this lab, I implemented **domain-level password policy enforcement and account lockout configuration** in the **Monroe Redstone Technology Group (MRTG)** Active Directory environment. The objective was to strengthen authentication controls in the `mrtg.local` domain by configuring centralized password requirements and validating real account lockout behavior with a standard user account.

This lab used the **Default Domain Policy** to configure password and lockout settings at the correct scope for classic Active Directory account policy. After applying the policy, I tested a standard domain user account from a client workstation to confirm that repeated failed sign-in attempts triggered account lockout as expected.

Password policy and account lockout are foundational identity protections. Without them, weak password hygiene and repeated failed authentication attempts create unnecessary risk for the directory environment.

---

## Objectives

- Configure domain-level password policy settings
- Configure domain-level account lockout settings
- Validate that the effective default domain policy reflects the configured values
- Demonstrate enforced account lockout after repeated failed sign-in attempts
- Prove both user-side enforcement and admin-side verification of the lockout state

---

## Scope

### Included
- Review of the domain GPO structure
- Configuration of password policy in the **Default Domain Policy**
- Configuration of account lockout policy in the **Default Domain Policy**
- Policy application and validation from PowerShell
- Lockout testing using a standard domain user account
- Administrative verification of the locked-out account state

### Not Included
- Fine-Grained Password Policies
- Microsoft Entra ID / cloud authentication controls
- MFA configuration
- Password self-service reset workflows
- SIEM filtering or advanced event correlation

This lab stays focused on **domain-level password policy and account lockout enforcement in classic Active Directory**.

---

## Lab Environment

### Systems
- **Host Platform:** Hyper-V
- **Domain Controller:** `MRTG-DC01`
- **Client Workstation:** `MRTG-CL01`
- **Domain:** `mrtg.local`

### Identity Context
- **Directory Structure:** Existing MRTG enterprise OU structure
- **Test User:** `Kevin Carter` (`kevin.carter@mrtg.local`)
- **Administrative Context:** Domain administrative session used for policy configuration and validation

### Tools / Technologies Used
- Windows Server 2022
- Active Directory Users and Computers
- Group Policy Management
- Group Policy Management Editor
- PowerShell
- Active Directory PowerShell module
- Windows 11 sign-in testing

---

## Architecture / Design

This lab was designed to support the following enterprise need:

> **MRTG requires stronger baseline authentication controls to reduce weak password practices and limit repeated failed sign-in attempts against domain accounts.**

### Design Logic
- **Identity:** Standard domain users authenticate to `mrtg.local`
- **Access Need:** Users require normal domain authentication for workstation and resource access
- **Control Need:** Password policy and account lockout policy are enforced through the **Default Domain Policy**
- **Risk Reduced:** Weak password reuse, uncontrolled failed authentication attempts, and basic password-guessing exposure

### IAM Perspective

This design supports:

- **authentication hardening**
- **centralized identity protection**
- **consistent domain-wide enforcement of account policy**

A critical design point in this lab is scope. Classic Active Directory password and lockout policy must be configured at the **domain level**, not treated like a normal OU-linked user policy. That distinction matters because this lab is proving **domain authentication control**, not just standard GPO targeting.

---

## Implementation Steps

### Step 1 - Review Domain GPO Structure

**Action Performed:**  
Opened **Group Policy Management** on `MRTG-DC01` and reviewed the `mrtg.local` domain structure, including the available Group Policy Objects.

**Why It Was Done:**  
Before modifying authentication policy, the correct GPO location needed to be confirmed so password and lockout settings would be applied at the proper scope.

**Evidence:**  
![Domain GPO Structure](images/lab-09-01-domain-gpo-structure.png)

**What This Proves:**  
This confirms that the **Default Domain Policy** exists in the `mrtg.local` GPO structure and was identified as the correct target for domain-level account policy.

---

### Step 2 - Configure Password Policy Settings

**Action Performed:**  
Edited the **Default Domain Policy** and configured the following settings under:

`Computer Configuration > Policies > Windows Settings > Security Settings > Account Policies > Password Policy`

**Configured Values:**
- **Enforce password history:** `5 passwords remembered`
- **Maximum password age:** `90 days`
- **Minimum password age:** `1 day`
- **Minimum password length:** `12 characters`
- **Password must meet complexity requirements:** `Enabled`
- **Store passwords using reversible encryption:** `Disabled`

**Why It Was Done:**  
These settings establish a stronger baseline for password hygiene by requiring longer passwords, complexity, and limited password reuse.

**Evidence:**  
![Password Policy Settings](images/lab-09-02-password-policy-settings.png)

**What This Proves:**  
This proves the password policy was configured directly in the **Default Domain Policy** using explicit hardening settings.

---

### Step 3 - Configure Account Lockout Policy

**Action Performed:**  
Configured the following settings under:

`Computer Configuration > Policies > Windows Settings > Security Settings > Account Policies > Account Lockout Policy`

**Configured Values:**
- **Account lockout threshold:** `5 invalid logon attempts`
- **Account lockout duration:** `15 minutes`
- **Reset account lockout counter after:** `15 minutes`

**Why It Was Done:**  
These settings create a response to repeated failed authentication attempts by temporarily locking the account after the threshold is reached.

**Evidence:**  
![Lockout Policy Settings](images/lab-09-03-lockout-policy-settings.png)

**What This Proves:**  
This confirms that account lockout behavior was explicitly configured with a defined threshold, duration, and reset window.

---

### Step 4 - Confirm Domain-Level Scope

**Action Performed:**  
Returned to **Group Policy Management** and selected the `mrtg.local` domain root to confirm that the **Default Domain Policy** was linked there.

**Why It Was Done:**  
Password and account lockout policy must be applied at the domain root to govern classic Active Directory domain authentication behavior correctly.

**Evidence:**  
![Domain Root Link Confirmation](images/lab-09-04-domain-root-link-confirmation.png)

**What This Proves:**  
This proves the policy is linked at the correct domain scope and supports domain-wide authentication control.

---

### Step 5 - Apply the Policy

**Action Performed:**  
Opened PowerShell as Administrator on `MRTG-DC01` and ran:

```powershell
gpupdate /force
```

**Why It Was Done:**  
The configured settings needed to be applied so the environment would use the updated password and lockout policy values.

**Evidence:**  
![GPUpdate Success](images/lab-09-05-gpupdate-success.png)

**What This Proves:**  
This confirms the updated Group Policy settings were applied successfully.

---

### Step 6 - Validate Effective Default Domain Policy

**Action Performed:**  
Ran the following command in PowerShell on `MRTG-DC01`:

```powershell
Get-ADDefaultDomainPasswordPolicy
```

**Why It Was Done:**  
This provides stronger validation than the GPO editor alone by showing the effective password and lockout values the domain is actually using.

**Evidence:**  
![Password Policy Validation](images/lab-09-06-password-policy-validation.png)

**What This Proves:**  
This validates the effective domain policy values, including:

- Complexity enabled
- Minimum password length = `12`
- Password history count = `5`
- Maximum password age = `90 days`
- Lockout threshold = `5`
- Lockout duration = `15 minutes`
- Lockout observation window = `15 minutes`

---

### Step 7 - Prepare the Test User

**Action Performed:**  
Opened **Active Directory Users and Computers** and reviewed the `Kevin Carter` account before lockout testing.

**Why It Was Done:**  
A normal, active domain user account was needed to test real sign-in behavior and confirm the policy was affecting authentication as intended.

**Evidence:**  
![Test User Ready](images/lab-09-07-test-user-ready.png)

**What This Proves:**  
This shows the selected test identity and confirms the account was ready for validation.

---

## Validation / Testing

### Validation Scenario 1 - Verify Effective Domain Policy

**Test Performed:**  
Reviewed the effective default domain password policy from PowerShell after applying the updated policy.

**Identity / System Used:**  
Administrative session on `MRTG-DC01`

**Expected Result:**  
The effective policy should reflect the configured password and lockout settings.

**Actual Result:**  
The effective policy showed the expected values for complexity, password length, password history, lockout threshold, lockout duration, and observation window.

**Evidence:**  
![Test User Ready](images/lab-09-07-test-user-ready.png)

**Why This Matters:**  
This confirms the domain is actually using the configured policy values rather than just storing them in the editor.

---

### Validation Scenario 2 - Trigger Account Lockout from the Client

**Test Performed:**  
Used `Kevin Carter` on `MRTG-CL01` and intentionally entered an incorrect password repeatedly until the lockout threshold was reached.

**Identity / System Used:**  
`kevin.carter@mrtg.local` on `MRTG-CL01`

**Expected Result:**  
After 5 failed sign-in attempts, the account should become locked and authentication should be denied.

**Actual Result:**  
The sign-in screen displayed the message: **"The referenced account is currently locked out and may not be logged on to."**

**Evidence:**  
![Account Lockout Triggered](images/lab-09-08-account-lockout-triggered.png)

**Why This Matters:**  
This proves the lockout policy is enforced from the user side and is not just a configured value on paper.

---

### Validation Scenario 3 - Verify Lockout from the Admin Side

**Test Performed:**  
Ran the following command on `MRTG-DC01`:

```powershell
Search-ADAccount -LockedOut
```

**Identity / System Used:**  
Administrative PowerShell session on `MRTG-DC01`

**Expected Result:**  
The locked-out test account should appear in the results with `LockedOut : True`.

**Actual Result:**  
Kevin Carter appeared in the results and the account state showed `LockedOut : True`.

**Evidence:**  
![Locked Account Verification](images/lab-09-09-locked-account-verification.png)

**Why This Matters:**  
This provides administrative verification that the domain account entered a locked state and can be validated operationally by IT staff.

---

## Security Relevance

This lab supports enterprise security and IAM operations by:

- enforcing stronger password standards
- limiting repeated failed authentication attempts
- improving consistency of domain account policy
- supporting centralized identity protection
- making authentication misuse easier to detect and respond to

Password policy and account lockout are baseline identity protections, but they still matter because they directly shape how domain accounts are defended. Weak password controls and unlimited failed attempts make it easier for attackers or careless users to create unnecessary exposure.

By configuring password requirements and lockout controls at the proper domain scope, this lab improves the authentication security posture of the MRTG environment. It also demonstrates a practical IAM principle: controls are only meaningful when they are both configured correctly and validated through real behavior.

---

## Key Skills Demonstrated

- Group Policy Management
- Default Domain Policy administration
- Active Directory password policy configuration
- Account lockout policy configuration
- PowerShell policy validation
- Active Directory PowerShell module usage
- Client-side sign-in testing
- Administrative lockout verification
- Domain authentication hardening
- IAM-focused policy enforcement

---

## Outcome

By the end of this lab, the environment was able to:

- enforce domain-level password requirements
- apply lockout after repeated failed sign-in attempts
- show user-side lockout enforcement at the workstation
- confirm locked account state from the admin side

This means the MRTG environment now supports centralized domain authentication hardening through password policy and account lockout enforcement.

---

## Next Lab

Lab-10 will build on this authentication hardening foundation by implementing **Fine-Grained Password Policies** to differentiate authentication controls between standard users, service accounts, and privileged identities in the MRTG environment.
