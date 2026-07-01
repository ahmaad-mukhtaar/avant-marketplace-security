# NIST CSF 2.0 Cybersecurity Implementation Plan

## 1. GOVERN (GV) & IDENTIFY (ID)
* **Asset Management:** All e-commerce production servers, customer databases, and point-of-sale endpoints are logged in a centralized asset inventory.
* **Risk Assessment:** Annual risk assessments are conducted to evaluate threats to cardholder data environments (CDE) in compliance with PCI-DSS baselines.

## 2. PROTECT (PR)
* **Identity & Access Management:** Implementation of Least Privilege access controls. Multi-Factor Authentication (MFA) is mandatory for all administrative access to the e-commerce backend.
* **Data Security:** Data-at-rest (customer databases) is encrypted using AES-256, and data-in-transit is secured using TLS 1.3.

## 3. DETECT (DE)
* **Continuous Monitoring:** Deployment of centralized logging to capture failed authentication attempts, unauthorized API calls, and network anomalies.

## 4. RESPOND (RS) & RECOVER (RC)
* **Incident Response:** A documented Incident Response Plan defines roles for containment, eradication, and notification during a data breach.
* **Resilience & Backups:** Automated, encrypted daily backups are stored securely offsite with tested restoration workflows to guarantee operational resilience.


# Vulnerability Assessment & Remediation Report
**Target Environment:** Avant-Marketplace Production Infrastructure (Simulated SMB)  
**Framework Alignment:** NIST SP 800-30 Rev 1, OWASP Top 10  
**Date:** July 2026

---

## 1. Executive Summary

### Overview
A comprehensive vulnerability assessment was conducted on the core digital assets powering our e-commerce platform. The objective was to identify security weaknesses, analyze technical risks, and provide actionable remediation strategies to safeguard customer data, financial records, and operational integrity.

### Key Findings
The assessment uncovered **three critical security gaps** that expose the business to severe risks, including data breaches, ransomware deployment, and regulatory non-compliance (PCI-DSS):
1. **Outdated Software Components:** The production database server runs an outdated version of PostgreSQL susceptible to remote code execution.
2. **Broken Object Level Authorization (OWASP Top 10):** A flaw in the web application API allows unauthenticated users to access other customers' order history and personal information.
3. **Weak Access Controls:** The administrative dashboard lacks Multi-Factor Authentication (MFA), leaving it vulnerable to credential stuffing and phishing attacks.

### Strategic Impact & Recommendation
If left unaddressed, these vulnerabilities could result in catastrophic financial losses, legal penalties, and severe damage to customer trust. 

Immediate resource allocation is directed toward:
* Implementing an aggressive **patch management schedule**.
* Hardening application APIs through **strict authorization checks**.
* Enforcing **mandatory Multi-Factor Authentication (MFA)** across all corporate and administrative accounts.

---

## 2. Scope & Target Assets

The boundary of this assessment was strictly limited to the primary internet-facing and data-handling systems of the e-commerce business:

| Asset Name | Asset Type | IP Address / URL | Function / Data Contained |
| :--- | :--- | :--- | :--- |
| avant-web-prod | Web Application | https://avant-marketplace.com | User interface, storefront, API gateway |
| avant-db-prod | Database Server | 10.0.1.15 (Internal network) | Customer PII, hashed passwords, transaction records |
| avant-admin-portal | Management Portal | https://admin.avant-marketplace.com | Employee backend for order and system management |

---

## 3. Detailed Technical Findings & Remediation Plans

Vulnerabilities are evaluated and prioritized using the **Common Vulnerability Scoring System (CVSS v3.1)**.

### Finding 1: Remote Code Execution via Outdated Database Software
* **Vulnerability Identifier:** CVE-2022-1514 (Simulated)
* **Severity Rating:** **Critical** (CVSS Score: 9.8)
* **Target Asset:** avant-db-prod
* **Evidence/Technical Detail:** An automated vulnerability scan identified that the database server is running PostgreSQL version 13.2. This specific version contains a known vulnerability allowing an unauthenticated remote attacker to execute arbitrary code on the underlying operating system with administrative privileges.
* **Potential Business Impact:** Complete compromise of the database engine. Attackers can exfiltrate the entire customer database, wipe structural tables, or deploy ransomware across the internal network.

#### 🛠️ Remediation Plan
* **Immediate Workaround:** Restrict database network access exclusively to the web application server (avant-web-prod) using host-based firewall rules (iptables / Security Groups).
* **Permanent Fix:** Schedule an immediate maintenance window to upgrade PostgreSQL to the latest stable, patched version (e.g., version 16.x or newer). Enact an automated monthly patch management policy for all production server binaries.

---

### Finding 2: Insecure Direct Object References (IDOR) / Broken Object Level Authorization
* **Vulnerability Identifier:** OWASP Top 10:2021 – A01:2021-Broken Access Control
* **Severity Rating:** **High** (CVSS Score: 8.5)
* **Target Asset:** avant-web-prod
* **Evidence/Technical Detail:** Manual API testing revealed that modifying the parameter in the URL path alters the data returned. For example, navigating to /api/v1/orders?id=1001 returns user data for Customer A. Changing the URL manually to /api/v1/orders?id=1002 while logged in as Customer A successfully returns the private order profile and data of Customer B without validating ownership.
* **Potential Business Impact:** Mass data exfiltration of customer personally identifiable information (PII). This directly violates data privacy laws (e.g., NDPR, GDPR) and invites severe compliance fines.

#### 🛠️ Remediation Plan
* **Permanent Fix:** Rewrite the backend API route handlers to implement strict object-level access controls. Ensure the application explicitly validates that the authenticated session ID matches the owner of the requested data resource before rendering the response.
* **Verification:** Run automated integration tests forcing unauthorized API requests to confirm the application blocks access natively.

---

### Finding 3: Missing Multi-Factor Authentication (MFA) on Administrative Portal
* **Vulnerability Identifier:** OWASP Top 10:2021 – A07:2021-Identification and Authentication Failures
* **Severity Rating:** **Medium** (CVSS Score: 6.5)
* **Target Asset:** avant-admin-portal
* **Evidence/Technical Detail:** Inspection of the administrative control interface confirmed that access relies entirely on a single factor: a username and password combination. No secondary verification step (such as a TOTP token or hardware key) is available or required.
* **Potential Business Impact:** Increased exposure to credential stuffing, brute-force attacks, and targeted phishing. A single leaked staff password grants full control over inventory, pricing, and system backend tools.

#### 🛠️ Remediation Plan
* **Immediate Workaround:** Deploy a Web Application Firewall (WAF) rule to restrict access to the /admin route exclusively to authorized corporate IP addresses or VPN subnets.
* **Permanent Fix:** Integrate an identity provider service (such as OAuth2, Okta, or native TOTP code libraries) to mandate Multi-Factor Authentication (MFA) for every user account possessing administrative or elevated system privileges.

---

## 4. Remediation Prioritization Timeline

To optimize limited IT engineering resources, remediation tasks must be addressed systematically based on risk impact:

1. [IMMEDIATE / 48 HOURS] -> Upgrade PostgreSQL Server (Finding 1)
2. [NEXT 7 DAYS] -> Patch API Authorization Controls (Finding 2)
3. [NEXT 30 DAYS] -> Enforce Portal MFA & IP Restrictions (Finding 3)

---

## 5. Stakeholder Presentation Summary (Mock Panel Briefing)

"To protect our revenue pipeline and maintain consumer trust, we are addressing three distinct vulnerabilities. Our highest risk stems from outdated database software, which acts as an unlocked back door to our data core. We are immediately sealing this entry point by updating the software and restricting server access lines. 

Second, we are re-engineering our web app APIs to prevent users from accidentally or intentionally viewing each other's data profiles. 

Lastly, we are eliminating single-password reliance on our management interfaces by introducing mandatory multi-factor authentication. These combined security improvements directly stabilize our operations against ransomware threats, solidify our compliance status, and assure our customers that their information remains safe with us."
