# **Stage 7 – Deployment Log**

**Project:** Atlas Industrial Security Architecture  
**Stage:** Deployment & Operations  
**Objective:** Document the successful deployment and configuration of all production systems within the cyber range.

---

## **Deployment Overview**

During Stage 7, the Atlas Industrial team transitioned from architectural design to operational deployment. All planned systems were successfully deployed, configured, and validated in alignment with the approved logical and network architectures.

> **Note:** While Kerberos and centralized authentication were evaluated during design, the team elected **not to implement Kerberos during the deployment phase** to reduce complexity and focus on secure baseline operations. Authentication was handled locally and at the application level for this phase.

**Deployed Systems:**

1. Windows Server 2025 (Directory & Infrastructure Services)
    
2. Tryton Web Application (Ubuntu 22.04)
    
3. Wazuh SIEM (Ubuntu 22.04)
    
4. Vaultwarden Password Manager (Ubuntu 22.04, Docker)
    

---

## **1. Windows Server 2025 – Infrastructure Services**

**System Role:**

- Centralized Windows server infrastructure
    
- DNS services
    
- Administrative management platform
    

**Deployment Steps:**

- Installed Windows Server 2025 
    
- Configured server with:
    
    - Static IP addressing
        
    - Local administrator accounts
        
    - DNS services for internal name resolution
        
- Hardened system using:
    
    - Strong local password policies
        
    - Disabled unnecessary services
        
    - Restricted inbound firewall rules
        

**Security Validation:**

- Verified DNS resolution from internal systems
    
- Confirmed firewall rules restrict access to required ports only
    
- Security and authentication logs forwarded to Wazuh SIEM
    

**Status:** ✅ Operational

---

## **2. Tryton Web Application – Ubuntu 22.04**

**System Role:**

- Enterprise Resource Planning (ERP) web application
    
- Application-level authentication
    

**Deployment Steps:**

- Installed Ubuntu 22.04 
    
- Deployed Tryton using Docker Compose
    
- Configured:
    
    - Web application service on port **8000**
        
    - Local application authentication
        
    - Backend database connectivity
        
- Implemented firewall controls to:
    
    - Allow HTTPS access from the user subnet
        
    - Restrict database access to the Tryton service only
        

**Security Validation:**

- Verified successful user login via Tryton application accounts
    
- Confirmed no access to other internal VLANs
    
- Application logs successfully forwarded to Wazuh SIEM
    

**Status:** ✅ Operational

---

## **3. Wazuh SIEM – Ubuntu 22.04**

**System Role:**

- Centralized security monitoring and alerting
    
- Log aggregation and visibility
    

**Deployment Steps:**

- Installed Ubuntu 22.04 
    
- Deployed Wazuh using Docker Compose (single-node deployment)
    
- Configured:
    
    - Wazuh Manager
        
    - Indexer
        
    - Dashboard
        
- Installed agents on:
    
    - Windows Server 2025
        
    - Tryton application server
        
    - Windows 10 endpoint
        
- Opened required agent communication ports (1514/1515)
    

**Security Validation:**

- Verified log ingestion from all systems
    
- Confirmed alerting for:
    
    - Failed login attempts
        
    - Firewall deny events
        
    - Service availability issues
        
- Restricted dashboard access to authorized admin workstation
    

**Status:** ✅ Operational

---

## **4. Vaultwarden – Ubuntu 22.04 (Docker)**

**System Role:**

- Secure password and credential management
    
- Centralized secrets storage
    

**Deployment Steps:**

- Installed Docker and Docker Compose plugin
    
- Deployed Vaultwarden using Docker Compose
    
- Configured:
    
    - Persistent encrypted storage volumes
        
    - Admin token for management access
        
    - Websocket support
        
- Implemented HTTPS using a reverse proxy to meet security requirements
    
- Bound Vaultwarden service to internal network only
    

**Security Validation:**

- Confirmed HTTPS enforcement (required for client-side encryption)
    
- Verified admin interface protected by strong token
    
- Credentials stored securely and isolated from other application data
    
- Vaultwarden access restricted via firewall rules
    

**Status:** ✅ Operational

---

## **Post-Deployment Validation**

- All systems are reachable only through approved network paths
    
- VLAN segmentation successfully enforced
    
- Default credentials removed or rotated
    
- Security events and system logs actively monitored via SIEM
    
- Services persist after system reboots and container restarts
    

---

## **Operational Readiness Summary**

The Atlas Industrial environment is fully deployed and operational. While centralized authentication was deferred, the deployed systems demonstrate secure baseline operations, strong access controls, network segmentation, and effective monitoring. This stage provided hands-on experience deploying and validating systems in a realistic SOC-style environment.

**Overall Deployment Status:** ✅ **Successful**