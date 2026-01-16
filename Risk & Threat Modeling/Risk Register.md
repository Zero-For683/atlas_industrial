**Atlas Industrial LLC – Risk Register (Stage 3: Risk & Threat Modeling)**
## **1. Web Application Attack Surface (Tryton)**

**System:** Tryton  
**Source:** Tryton documentation cites brute-force login, SQL injection, and XSS as primary risks for browser-based ERP systems.

Tryton Documentation

| Item              | Details                                                                                                       |
| ----------------- | ------------------------------------------------------------------------------------------------------------- |
| **Threat**        | Web-based attacks (brute force, XSS, SQL injection)                                                           |
| **Vulnerability** | Public-facing login page, weak input validation, outdated web components                                      |
| **Likelihood**    | **High**                                                                                                      |
| **Impact**        | **High** — ERP manages inventory, production workflows, customer data                                         |
| **CIA Impact**    | **C: High*, *I: High*, *A: Medium**                                                                           |
| **Risk Level**    | **High**                                                                                                      |
| **Mitigation**    | MFA, rate limiting, WAF rules, hardened Docker configuration, encrypted communication, secure coding settings |

## **2. Database Service Exposure**

**System:** PostgreSQL Database  
**Source:** PostgreSQL documentation describes this as a major risk if port 5432 is exposed publicly.

PostgreSQL Documentation

| Item               | Details                                                                                                        |
| ------------------ | -------------------------------------------------------------------------------------------------------------- |
| **Threat**         | External attacker exploiting exposed database port (5432)                                                      |
| **Vulnerability**  | Open port exposure, brute-force attacks, SQL injection attempts                                                |
| **Likelihood**     | **Medium** (common attack vector)                                                                              |
| **Impact**         | **High** — Database holds financial, inventory, and customer data                                              |
| **CIA Impact** | **Confidentiality: High**, **Integrity: High**, **Availability: Medium**                                   |
| **Risk Level**     | **High**                                                                                                       |
| **Mitigation**     | Firewall rules, network segmentation, disable remote access, enable TLS, strong passwords, change default port |
## **3. Misconfiguration Risks (Docker, SIEM, Database, ERP)**

**System:** Entire environment (Tryton, PostgreSQL, Wazuh)  
**Source:** Both Tryton & PostgreSQL docs warn that Docker misconfiguration can expose services.

PostgreSQL Documentation

Tryton Documentation

|Item|Details|
|---|---|
|**Threat**|Accidental misconfiguration that exposes ports, credentials, or debug services|
|**Vulnerability**|Weak .env file permissions, exposed Docker ports, default creds|
|**Likelihood**|**Medium**|
|**Impact**|**High** — system-wide compromise possible|
|**CIA Impact**|**C: High**, **I: High**, **A: Medium**|
|**Risk Level**|**High**|
|**Mitigation**|Enforce .env file permissions, disable default Docker networks, validate compose files, use hardened images, perform CIS Docker benchmarks|
## **4. Certificate Misconfiguration (Wazuh)**

**System:** Wazuh SIEM (Dashboard + Manager + Indexer)  
**Source:** Wazuh installation guide stresses issues with missing/incorrect certificates causing insecure communication or service failures.

Wazuh Installation

| Item              | Details                                                                                                                       |
| ----------------- | ----------------------------------------------------------------------------------------------------------------------------- |
| **Threat**        | Attackers intercept SIEM traffic or cause service failures                                                                    |
| **Vulnerability** | Incorrect or missing TLS certificates, unencrypted dashboard                                                                  |
| **Likelihood**    | **Medium**                                                                                                                    |
| **Impact**        | **High** — SIEM integrity is critical for monitoring & detection                                                              |
| **CIA Impact**    | **C: High**, **I: High**, **A: High**                                                                                         |
| **Risk Level**    | **High**                                                                                                                      |
| **Mitigation**    | Generate correct certs, enforce HTTPS, validate pem files, restart faulty services, use automated certificate lifecycle tools |
## **5. User Endpoint Vulnerability (Windows 10 Workstation)**

**System:** Windows 10 endpoint  
**Source:** Due Diligence doc identifies user environment as “biggest weakness.”

Due-Diligence

|Item|Details|
|---|---|
|**Threat**|Phishing, malware, credential theft|
|**Vulnerability**|Human error, social engineering, outdated software|
|**Likelihood**|**High**|
|**Impact**|**High** — endpoint compromise can pivot to DB or ERP|
|**CIA Impact**|**C: High**, **I: Medium**, **A: Medium**|
|**Risk Level**|**High**|
|**Mitigation**|Wazuh agent monitoring, endpoint hardening, user training, email security controls, application allowlisting|
