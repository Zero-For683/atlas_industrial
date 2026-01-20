# SIEM Use Case Document  
**Platform:** Wazuh SIEM  
**Scope:** Windows Server 2025, Ubuntu 22.04 (Tryton Application & Wazuh SIEM), Windows 10 Endpoint

---

## Purpose

This document defines initial SIEM detection and alerting use cases designed to identify common attack techniques, misconfigurations, and malicious behavior within the proposed environment. These use cases prioritize early detection of credential abuse, privilege escalation, and unauthorized access to critical systems.

---

## Use Case 1: Excessive Authentication Failures (Credential Abuse)

**Threat Addressed**
- Brute-force attacks  
- Password spraying  
- Credential stuffing  
- Early-stage account compromise  

**Monitored Systems**
- Windows Server 2025 (Active Directory)
- Windows 10 Endpoint
- Ubuntu 22.04 (SSH)

**Log Sources**
- Windows Security Event Logs
- Linux authentication logs (`/var/log/auth.log`)

**Detection Logic**
Trigger an alert when:
- More than **5 failed authentication attempts**
- Occur within **5 minutes**
- From the same source IP or targeting the same user account

**Severity**
- High

**SOC Response Actions**
- Validate source IP reputation
- Identify affected accounts
- Correlate activity across endpoints and servers
- Escalate if followed by successful login

---

## Use Case 2: Privilege Escalation or Administrative Group Modification

**Threat Addressed**
- Unauthorized privilege escalation
- Insider threat activity
- Post-exploitation persistence

**Monitored Systems**
- Windows Server 2025 (Domain Controller)
- Windows 10 Endpoint

**Log Sources**
- Windows Security Event Logs (Group membership changes, privilege assignment)

**Detection Logic**
Trigger an alert when:
- A user is added to privileged groups (e.g., Domain Admins, Local Administrators)
- Privilege escalation events occur outside approved change windows
- Privileges are granted by non-administrative accounts

**Severity**
- Critical

**SOC Response Actions**
- Validate change against approved administrative activity
- Review source account and host
- Investigate related authentication and process execution events
- Roll back unauthorized permissions if confirmed malicious

---

## Use Case 3: Suspicious Process Execution on Endpoints or Servers

**Threat Addressed**
- Malware execution
- Living-off-the-land attacks
- Unauthorized script execution

**Monitored Systems**
- Windows 10 Endpoint
- Windows Server 2025
- Ubuntu 22.04 (Tryton & Wazuh)

**Log Sources**
- Windows process creation logs
- Linux audit logs
- Wazuh agent telemetry

**Detection Logic**
Trigger an alert when:
- Scripts or binaries execute from temporary or user-writable directories
- Administrative tools are executed by non-admin users
- Unusual parent-child process relationships are observed

**Severity**
- Medium to High

**SOC Response Actions**
- Validate process legitimacy
- Check command-line arguments and hashes
- Correlate with recent authentication events
- Isolate endpoint if malicious activity is confirmed

---

## Use Case 4: Unauthorized Access Attempts to Application or Database Services

**Threat Addressed**
- Unauthorized internal access
- Application abuse
- Lateral movement attempts

**Monitored Systems**
- Ubuntu 22.04 (Tryton Application Server)
- PostgreSQL Database (if logged separately)

**Log Sources**
- Application logs
- Database authentication logs
- Linux system logs

**Detection Logic**
Trigger an alert when:
- Repeated failed application or database login attempts occur
- Access attempts originate from unauthorized network segments
- Service accounts authenticate interactively

**Severity**
- Medium

**SOC Response Actions**
- Identify source host and user
- Verify network segmentation controls
- Reset affected credentials if necessary
- Review firewall and access control rules

---

## Summary

These initial SIEM use cases provide foundational visibility into authentication abuse, privilege escalation, malware execution, and unauthorized access. They are designed to be expanded over time as the environment matures and threat modeling evolves.
