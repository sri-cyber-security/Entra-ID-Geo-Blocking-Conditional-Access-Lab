# Zero Trust & Geo-Blocking Implementation in Microsoft Entra ID 

## Project Overview
This lab demonstrates how to protect cloud identities from unauthorized foreign login attempts by implementing a **Geo-Blocking Conditional Access Policy (`CA02`)** in **Microsoft Entra ID P2**. By defining a country-based **Named Location** and layering it with an existing MFA policy (`CA01`), this project demonstrates **Defense-in-Depth**, exclusion logic, and Entra ID's **"Block Always Wins"** precedence rule using the **What If** simulation tool.

---

## Lab Environment
* **Identity Provider:** Microsoft Entra ID 
* **License:** Microsoft Entra ID P2 (Trial)
* **Test Identity:** `Test-User` (Already governed by `CA01 - Require MFA for Test User`)
* **Target Scope:** All Cloud Resources

---

## Implementation Steps & Proof of Execution

### 1. Creating a Trusted Named Location
Created a country-based Named Location to define the authorized operating region:
* **Location Name:** `Allowed Country - India`
* **Lookup Method:** Determine location by IP address (IPv4 and IPv6)
* **Selected Country:** India

<img width="1920" height="900" alt="Ca2 named loc adding India" src="https://github.com/user-attachments/assets/7cfe77ed-d461-41ba-aa9e-ea99fd3e1bcd" />

---

### 2. Configuring Geo-Blocking Network Scope (`CA02`)
Created a new Conditional Access policy named **`CA02 - Block Access Outside India`** targeting `Test-User` across **All resources**, using Include/Exclude location logic:
* **Include:** `Any network or location`
* **Exclude:** `Allowed Country - India`

<img width="1920" height="908" alt="cao2 include network" src="https://github.com/user-attachments/assets/e2498574-9d1e-4f77-9f74-0e13a09e5830" />
<img width="1920" height="915" alt="ca02 exclude network" src="https://github.com/user-attachments/assets/a5f77c6e-e4f0-413d-ae52-dcfffeaf56f4" />

---

### 3. Configuring Access Controls & Enabling Policy
Configured the **Grant** control to **`Block access`** and enabled the policy (`On`) so all sign-ins originating outside India are immediately blocked.

<img width="1920" height="910" alt="ca02 access block" src="https://github.com/user-attachments/assets/441d278a-682e-4365-a553-da2653d92fb1" />
<img width="1932" height="890" alt="ca02 policyview" src="https://github.com/user-attachments/assets/4540fba5-7d3c-4a85-9670-873387e33c17" />

---

### 4. Policy Validation Using the "What If" Tool
Simulated global sign-in attempts for `Test-User` to verify layered policy behavior:

#### Scenario A: Foreign Sign-in Attempt (Argentina)
* **Simulated Location:** Argentina
* **Evaluation Result:** Both `CA01 - Require MFA for Test User` and `CA02 - Block Access Outside India` applied.
* **Outcome:** **Access Blocked.** In Microsoft Entra ID, **Block always takes precedence** over Grant (MFA) controls, stopping the foreign login attempt immediately.

<img width="1920" height="919" alt="ca02whatif1" src="https://github.com/user-attachments/assets/a44a6423-bbf8-42bf-92b8-477dd06d3fc7" />
<img width="1920" height="922" alt="ca02whatif2" src="https://github.com/user-attachments/assets/0b624c79-7994-4211-9295-678e634f3a0a" />

#### Scenario B: Legitimate Sign-in Attempt (India)
* **Simulated Location:** India
* **Evaluation Result:** 
  * **Policies that will apply:** `CA01 - Require MFA for Test User` (Requires MFA)
  * **Policies that will not apply:** `CA02 - Block Access Outside India` (Excluded due to `Location`)
* **Outcome:** **Access Allowed with MFA.** Authorized users inside India bypass the geo-block while remaining protected by Zero Trust MFA.
  
<img width="1920" height="908" alt="ca02 whatif ind allow" src="https://github.com/user-attachments/assets/7e40c4b7-26c9-4495-b9b3-b4d2f929cb65" />
<img width="1920" height="915" alt="ca02 whatif poli not apply" src="https://github.com/user-attachments/assets/3c249378-ea6e-462c-8c8b-245b16a018ed" />

---

## Key IAM Skills Demonstrated
* **Geo-Fencing & Named Locations:** Defining IP-based country boundaries in Microsoft Entra ID.
* **Defense-in-Depth:** Layering MFA (`CA01`) and Geo-Blocking (`CA02`) on the same identity.
* **Policy Precedence:** Validating that explicit **Block** controls always override **Grant (MFA)** controls.
* **Safe Policy Testing:** Simulating global login scenarios with the **What If** tool without risking tenant lockouts.
