
These rules ensure that the workstation can only reach the Tryton Web App through HTTPS.  
All other traffic is blocked and logged for SIEM analysis.
## ✅ **Allow Rules**

`WIN10_PRO  -->  TRYTON_WEB_APP : TCP 443 (Outbound)`
`TRYTON_WEB_APP  -->  WIN10_PRO : ESTABLISHED/RELATED`
`WIN10_PRO  -->  DNS_SERVERS : UDP/TCP 53 
`WIN10_PRO  -->  NTP_SERVER : UDP 123 `


| Zone                        | Description                     | Subnet          |
| --------------------------- | ------------------------------- | --------------- |
| **VLAN30 – Workstations**   | Internal user endpoints         | 192.168.30.0/24 |
| **VLAN20 – Server Network** | Tryton server + PostgreSQL      | 172.168.20.0/24 |
| **OPNsense LAN Interfaces** | Enforces segmentation and rules | —               |
# **3. Final Firewall Rule Table (Submission-Ready)**

## ❌ **Deny Rules**

`ANY_REMOTE  -->  WIN10_PRO : ANY (Inbound)`

**Purpose:**  
Prevents unauthorized inbound connections to the workstation unless part of an established session.

`WIN10_PRO  -->  VLAN20 : ANY (Outbound except HTTPS 443)`

**Purpose:**  
Prevents users from accessing internal servers directly.

## ⚙️ **Configurations**

> [!danger] **Workstation cannot directly access Tryton on port 8000**  
> Port 8000 is _never_ exposed to clients — access is only through HTTPS 443 → NAT → 8000.

---

> [!note] **Windows Firewall must be set to "Block inbound connections" (default)**  
> Ensures only established sessions from HTTPS responses are allowed back in.

---

> [!info] **Outbound filtering enabled for sensitive zones**  
> Only web (443), DNS (53), NTP (123), Kerberos (88/389/445), and Wazuh agent ports should be allowed.

---

> [!tip] **Log dropped outbound traffic for SIEM ingestion**  
> Helps detect compromised workstations attempting lateral movement.


