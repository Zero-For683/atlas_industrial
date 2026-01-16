
These rules ensure that the workstation can only reach the Tryton Web App through HTTPS.  
All other traffic is blocked and logged for SIEM analysis.
## ✅ **Allow Rules**

`WIN10_PRO  -->  TRYTON_WEB_APP : TCP 443 (Outbound)`
`TRYTON_WEB_APP  -->  WIN10_PRO : ESTABLISHED/RELATED`
`WIN10_PRO  -->  HOST_TRYTON  : TCP 443 (HTTPS access to Tryton web page) 
`WIN10_PRO  -->  DNS/NTP : TCP/UDP 53 & UDP 123 (DNS resolution + time sync) `

## ❌ **Deny Rules**

`ANY_REMOTE  -->  WIN10_PRO : ANY (Inbound)`

**Purpose:**  
Prevents unauthorized inbound connections to the workstation unless part of an established session.

`WIN10_PRO  -->  VLAN20 : ANY (Outbound except HTTPS 443)`

**Purpose:**  
Prevents users from accessing internal servers directly.

## ⚙️ **Configurations**

> [!danger] **WIN10_PRO must NOT be able to access the OPNsense admin interface**  
> Web-based firewall management (HTTPS 443) is restricted to the ADMIN_VLAN only.

---

> [!note] **Firewall management access restricted to designated admin workstation**  
> Ensures users cannot attempt privilege escalation or misconfiguration.

---

> [!info] **All denied traffic is logged to the Wazuh SIEM**  
> Enables detection of lateral movement, malware callbacks, and policy violations.

