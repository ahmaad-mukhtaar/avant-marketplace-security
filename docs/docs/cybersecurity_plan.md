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
