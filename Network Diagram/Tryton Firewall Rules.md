
> [!info] These rules ensure that the workstation can only reach workstation in the DMZ through HTTPS, and `kerberos`. All other traffic is blocked and logged for SIEM analysis.
## Allow - Inbound

`WIN10_PRO  -->  HOST_TRYTON  : TCP 443 (HTTPS access to Tryton web page) 

- `Win10` --> From DMZ IP (192.168.1) on port 8000 for customer portal
- `AD` & `Kerberos` --> From VLAN30 (172.168.30)
- `2025 SERV` --> From VLAN 20 (172.168.20) for SQL queries


## Allow - Outbound

- `Win10` --> From DMZ IP (192.168.1) on port 8000 for customer portal
- `2025 SERV` --> From VLAN 20 (172.168.20) for SQL queries
- `Wazuh manager`: TCP 1514/1515 for the agent to deliver data

## **Deny Rules**

`ANY_REMOTE  -->  WIN10_PRO : ANY (Inbound)`

**Purpose:**  
Prevents unauthorized inbound connections to the workstation unless part of an established session.

`WIN10_PRO  -->  VLAN20 : ANY (Outbound except HTTPS 443)`

**Purpose:**  
Prevents users from accessing internal servers directly.

## ⚙️ **Configurations**

> [!danger] **Ubuntu 22.04 (Tryton) must NOT be able to access the OPNsense admin interface**  
> Web-based firewall management (HTTPS 443) is restricted to the ADMIN_VLAN only.

---

> [!note] **Firewall management access restricted to designated admin workstation**  
> Ensures users cannot attempt privilege escalation or misconfiguration.

---

> [!info] **All denied traffic is logged to the Wazuh SIEM**  
> Enables detection of lateral movement, malware callbacks, and policy violations.

