
## **1. PURPOSE AND SCOPE**

## 1.1 Purpose

This policy establishes standardized procedures, schedules, responsibilities, and rollback plans for patching and maintaining all systems within the lab environment to ensure security, stability, and operational continuity.

## 1.2 Scope

This policy applies to all systems, applications, and network devices within the lab infrastructure, including:

- **Application Servers**: Tryton ERP (Ubuntu 22.04 LTS)
    
- **Database Servers**: PostgreSQL 17.2 (Windows Server 2025)
    
- **Security Infrastructure**: Wazuh SIEM (Ubuntu 22.04 LTS)
    
- **Endpoint Systems**: Windows 10 workstations
    
- **Network Devices**: Firewalls
    
- **Supporting Infrastructure**: Hypervisors, backup systems, and monitoring tools
    

---

## **2. PATCH MANAGEMENT FRAMEWORK**

## 2.1 Patch Classification

Patches are classified by severity to determine appropriate response timelines:

| **Severity Level** | **Definition**                                                                                            | **Target Deployment**                 |
| ------------------ | --------------------------------------------------------------------------------------------------------- | ------------------------------------- |
| **Critical**       | Actively exploited zero-day vulnerabilities; CVSS 9.0-10.0; affects internet facing or high value systems | 24-72 hours                           |
| **High**           | Exploitable vulnerabilities with public proof-of-concept; CVSS 7.0-8.9                                    | 7-14 days                             |
| **Medium**         | Vulnerabilities requiring local access or complex attack chains; CVSS 4.0-6.9                             | 30 days                               |
| **Low**            | Informational or defense-in-depth updates; CVSS 0.1-3.9                                                   | 60-90 days or next maintenance window |

## 2.2 Patch Sources

- **Operating Systems**: Microsoft Update (Windows), apt/Ubuntu repositories (Linux), of course going through the updates and ensure they will not disrupt any current processes or systems.
    
- **Applications**: Vendor specific channels (Tryton GitHub releases, PostgreSQL official releases, Wazuh package manager)
    
- **Security Tools**: Wazuh agent auto updates, signature updates on the SIEM console
    
- **Antivirus/EDR**: Daily signature and engine updates.
    

---

## **3. PATCHING SCHEDULES**

## 3.1 Regular Maintenance Windows

|**System Type**|**Maintenance Window**|**Frequency**|**Duration**|
|---|---|---|---|
|**Production Systems** (Tryton, PostgreSQL)|2nd Saturday, 02:00-06:00 CST|Monthly|4 hours|
|**Security Infrastructure** (Wazuh)|4th Saturday, 02:00-05:00 CST|Monthly|3 hours|
|**Windows Endpoints**|3rd Tuesday, 18:00-20:00 CST (Patch Tuesday +1)|Monthly|2 hours|
|**Network Devices**|1st Sunday, 01:00-04:00 CST|Quarterly|3 hours|
|**Signature Updates** (AV, IDS, Wazuh rules)|Automated daily at 03:00 CST|Daily|15 minutes|

## 3.2 Emergency Patching

- **Critical vulnerabilities** (actively exploited zero-days) may be patched outside scheduled windows with Change Approval Authority authorization
    
- Emergency patches for internet-facing systems (Tryton web interface) receive highest priority
    
- Emergency maintenance requires 4 hour advance notification to stakeholders when feasible
    

## 3.3 Change Freeze Periods

No non-emergency patching during:

- End-of-quarter processing periods (last 3 business days of each quarter)
    
- Annual audit windows (dates TBD by compliance team)
    
- Major system upgrades or migrations (scheduled separately)
    

---

## **4. ROLES AND RESPONSIBILITIES**

## 4.1 Responsibility Matrix (RACI)

|**Activity**|**System Owner**|**Security Engineer**|**Change Approver**|**On-Call Analyst**|
|---|---|---|---|---|
|**Vulnerability assessment & prioritization**|C|R/A|I|I|
|**Patch testing in non-prod**|R/A|C|I|-|
|**Production patch deployment**|R/A|C|I|I|
|**Emergency patch approval**|C|R|A|I|
|**Post-patch validation**|R/A|C|-|C|
|**Rollback execution**|R/A|C|I|C|
|**Compliance reporting**|C|R/A|I|-|

**Legend**: R = Responsible, A = Accountable, C = Consulted, I = Informed

## 4.2 Role Definitions

**System Owner**

- Schedules and executes patching within maintenance windows
    
- Performs pre-patch backups and post-patch validation
    
- Documents patch activities in change management system
    
- Owns system-specific rollback procedures
    

**Security Engineer**

- Monitors vulnerability feeds and security advisories (NVD, vendor bulletins, Wazuh alerts)
    
- Assesses vulnerabilities against lab threat model (references risk register)
    
- Prioritizes patches based on severity, exploitability, and system criticality
    
- Validates security tool functionality post-patch (Wazuh agents, firewall rules)
    

**Change Approver**

- Reviews and approves all standard and emergency change requests
    
- Authorizes out-of-window emergency patching for critical vulnerabilities
    
- Escalates conflicts between patching needs and operational requirements
    

**On-Call Analyst**

- Monitors systems during and after maintenance windows
    
- Responds to alerts indicating patch failures or service degradation
    
- Initiates rollback procedures per criteria in Section 6.3
    
- Documents incidents in ticketing system
    

---

## **5. PATCH DEPLOYMENT WORKFLOW**

## 5.1 Standard Patching Process

**Phase 1: Assessment (T-14 days before maintenance window)**

1. Security Engineer reviews vulnerability feeds and vendor advisories
    
2. Identify applicable patches for in-scope systems
    
3. Classify patches by severity (Section 2.1)
    
4. Create change request in ticketing system with:
    
    - Systems affected
        
    - Patch details (KB numbers, CVE IDs, vendor advisory links)
        
    - Expected downtime and rollback plan
        

**Phase 2: Testing (T-7 days)**

1. System Owner deploys patches to non-production test environment:
    
    - **Tryton**: Dedicated test instance on separate Ubuntu VM
        
    - **PostgreSQL**: Test database server (isolated from production)
        
    - **Wazuh**: Staging SIEM instance or isolated agent
        
    - **Windows 10**: Test workstation group (pilot users)
        
2. Execute validation checklist (Section 5.3)
    
3. Document test results; escalate any issues to Security Engineer
    

**Phase 3: Approval (T-3 days)**

1. Change Approver reviews change request, test results, and risk assessment
    
2. Approve or defer patching based on business impact and success criteria
    
3. Notify stakeholders of scheduled maintenance
    

**Phase 4: Pre-Deployment (T-0, before maintenance window)**

1. System Owner verifies backup completion:
    
    - **Tryton/Wazuh VMs**: Hypervisor snapshots (pre-patch labeled)
        
    - **PostgreSQL**: Full database backup (`pg_dumpall`) to network share
        
    - **Windows Endpoints**: System Restore points enabled
        
2. Confirm rollback procedures are documented and accessible
    
3. Disable monitoring alerts for maintenance window (suppress false positives)
    

**Phase 5: Deployment (During maintenance window)**

1. Execute patches in prioritized order:
    
    - **Order**: Database tier → Application tier → Security tools → Endpoints
        
    - **Method**: Native OS update mechanisms (Windows Update, apt, vendor installers)
        
2. System Owner monitors patch installation logs for errors
    
3. Perform phased reboots if required (database first, then dependent services)
    

**Phase 6: Validation (Immediately post-patch)**

1. Execute post-patch validation checklist (Section 5.3)
    
2. On-Call Analyst monitors SIEM and application logs for anomalies
    
3. System Owner documents patch status (successful/failed/rolled back) in change ticket
    

**Phase 7: Monitoring (T+24 hours to T+7 days)**

1. Enhanced monitoring for performance degradation or unexpected behavior
    
2. Security Engineer verifies vulnerability scanners confirm patch installation
    
3. System Owner closes change request with final status report
    

## 5.2 Emergency Patching Process (Critical Vulnerabilities)

1. **Notification (T-0)**: Security Engineer identifies critical vulnerability affecting production
    
2. **Risk Assessment (T+1 hour)**: Evaluate exploitability, exposure, and compensating controls
    
3. **Emergency Approval (T+2 hours)**: Change Approver authorizes out-of-window patching
    
4. **Abbreviated Testing (T+4 hours)**: Deploy to test environment; validate core functionality only
    
5. **Production Deployment (T+24 hours)**: Execute emergency patching with on-call support
    
6. **Extended Monitoring (T+48 hours)**: Continuous observation for stability issues
    

## 5.3 Validation Checklist

**All Systems:**

-  System boots successfully and reaches operational state
    
-  All critical services running (verify with `systemctl status` or Services.msc)
    
-  Network connectivity confirmed (ping gateway, DNS resolution)
    
-  Authentication mechanisms functional (local and domain accounts)
    
-  Monitoring agents reporting to SIEM (Wazuh agent active)
    

**Tryton ERP (Ubuntu):**

-  Tryton web interface accessible at `https://<tryton-ip>:8000`
    
-  Database connection successful (query test records)
    
-  User login and module functionality validated
    
-  Logs show no errors: `sudo journalctl -u tryton.service --since "10 minutes ago"`
    

**PostgreSQL (Windows Server 2025):**

-  PostgreSQL service running: `Get-Service postgresql-x64-17`
    
-  Database accepts connections: `psql -U postgres -c "SELECT version();"`
    
-  Replication status checked (if applicable): `SELECT * FROM pg_stat_replication;`
    
-  Scheduled backup jobs functional
    

**Wazuh SIEM (Ubuntu):**

-  Wazuh manager service active: `sudo systemctl status wazuh-manager`
    
-  Agents reporting in (check dashboard for active agents)
    
-  Alert generation functional (trigger test rule)
    
-  API accessibility confirmed: `curl -k https://<wazuh-ip>:55000/`
    

**Windows 10 Endpoints:**

-  User profile loads without errors
    
-  Network drives accessible
    
-  Core applications launch (browsers, productivity tools)
    
-  Wazuh agent communicating with manager
    

---

## **6. ROLLBACK PROCEDURES**

## 6.1 Rollback Decision Criteria

Initiate rollback if any of the following occur within 24 hours post-patch:

|**Trigger**|**Severity**|**Rollback Authority**|
|---|---|---|
|**Critical service outage** (Tryton/PostgreSQL unavailable)|Immediate|System Owner or On-Call Analyst|
|**Data corruption or loss**|Immediate|System Owner + Change Approver|
|**Widespread endpoint failures** (>20% of Windows 10 systems)|Immediate|System Owner|
|**Security tool failure** (Wazuh agents offline, firewall rules broken)|Within 2 hours|Security Engineer|
|**Performance degradation >30%** (response times, CPU/memory)|Within 8 hours|System Owner|
|**Failed validation checklist** (>2 critical items)|Before production release|System Owner|

## 6.2 Rollback Methods by System Type

**Tryton & Wazuh (Ubuntu VMs):**

1. Shut down affected VM gracefully: `sudo shutdown -h now`
    
2. Revert to pre-patch hypervisor snapshot via virtualization console
    
3. Power on VM and verify boot
    
4. Validate services: `sudo systemctl status tryton.service wazuh-manager.service`
    
5. Confirm agent connectivity (Wazuh) or web interface access (Tryton)
    
6. Document rollback reason in change ticket
    

**PostgreSQL (Windows Server 2025):**

1. Stop PostgreSQL service: `Stop-Service postgresql-x64-17`
    
2. Rename current data directory: `Rename-Item "C:\Program Files\PostgreSQL\17\data" "data_patched"`
    
3. Restore pre-patch backup:
    
    powershell
    
    `psql -U postgres -c "DROP DATABASE tryton_db;" # If applicable psql -U postgres -f "\\backup-share\postgres_pre-patch.sql"`
    
4. Restart PostgreSQL: `Start-Service postgresql-x64-17`
    
5. Verify connectivity from Tryton application
    
6. If database rollback insufficient, restore full VM from hypervisor snapshot
    

**Windows 10 Endpoints:**

1. **Single endpoint rollback**:
    
    - Boot to Safe Mode
        
    - Uninstall patch via Settings → Update & Security → View Update History → Uninstall Updates
        
    - Restart and validate per checklist
        
2. **Mass rollback (WSUS/GPO managed)**:
    
    - Disable automatic updates via GPO
        
    - Deploy uninstall script via Group Policy:
        
        powershell
        
        `wusa.exe /uninstall /kb:<KB-NUMBER> /quiet /norestart`
        
    - Re-enable updates after validation
        

**Network Devices:**

1. Console access required (SSH or physical console cable)
    
2. Reload previous configuration from startup-config or saved backup
    
3. Verify routing tables, firewall ACLs, and connectivity tests
    
4. Confirm Wazuh can receive logs from network devices
    

## 6.3 Rollback Validation

After rollback execution:

1. Re-run full validation checklist (Section 5.3)
    
2. Verify pre-patch functionality restored
    
3. On-Call Analyst monitors for 2 hours post-rollback
    
4. System Owner documents root cause analysis in change ticket
    
5. Security Engineer assesses vulnerability exposure if patch removed
    

## 6.4 Post-Rollback Actions

- **Incident Report**: Document failure root cause, systems impacted, and lessons learned
    
- **Vendor Escalation**: Contact vendor support if patch caused unexpected behavior
    
- **Alternative Mitigations**: Security Engineer identifies compensating controls (firewall rules, IDS signatures, access restrictions) to reduce risk until stable patch available
    
- **Re-Testing**: Schedule re-deployment with additional test scenarios addressing failure cause
    

---

## **7. BACKUP AND RECOVERY**

## 7.1 Pre-Patch Backup Requirements

|**System**|**Backup Method**|**Backup Location**|**Retention**|**RTO**|**RPO**|
|---|---|---|---|---|---|
|**Tryton VM**|Hypervisor snapshot|Local datastore + network share|2 snapshots|30 min|0 (snapshot before patch)|
|**PostgreSQL Database**|`pg_dumpall` script|Network file server (UNC path)|3 backups|1 hour|0 (backup before patch)|
|**Wazuh VM**|Hypervisor snapshot|Local datastore|2 snapshots|30 min|0 (snapshot before patch)|
|**Windows 10 Endpoints**|System Restore point|Local C:\ drive|Auto-managed|1 hour|24 hours|
|**Configuration Files**|Git repository or file copy|Version control system or backup server|Indefinite|15 min|0 (pre-patch commit)|

## 7.2 Backup Validation

- **Test Restores**: Quarterly validation of backup integrity (restore to isolated test environment)
    
- **Backup Monitoring**: Automated alerts if backup jobs fail or exceed expected duration
    
- **Documentation**: Maintain runbooks with restore procedures for each system type