
These rules ensure that the workstation can only reach the Tryton Web App through HTTPS.  
All other traffic is blocked and logged for SIEM analysis.

## **Outbound Rules (VLAN30 → VLAN20)**

| Port          | Protocol | Purpose                                                 | Action    | Logging |
| ------------- | -------- | ------------------------------------------------------- | --------- | ------- |
| **443**       | TCP      | HTTPS access to Tryton Web App (forwarded to port 8000) | **ALLOW** | Enabled |
| **53**        | UDP/TCP  | DNS resolution                                          | **ALLOW** | Enabled |
| **123**       | UDP      | NTP time sync (required for Kerberos)                   | **ALLOW** | Enabled |
| **88**        | TCP/UDP  | Kerberos authentication to Domain Controller            | **ALLOW** | Enabled |
| **389**       | TCP      | LDAP directory queries (AD lookup)                      | **ALLOW** | Enabled |
| **445**       | TCP      | SMB for AD workstation policies                         | **ALLOW** | Enabled |
| **1514/1515** | TCP      | Wazuh agent → SIEM communications                       | **ALLOW** | Enabled |
| **All other** | ANY      | Everything else                                         | **DENY**  | Enabled |

## ✅ **Allow Rules**

`WIN10_PRO  -->  TRYTON_WEB_APP : TCP 443 (Outbound)`
`TRYTON_WEB_APP  -->  WIN10_PRO : ESTABLISHED/RELATED`
`WIN10_PRO  -->  DNS_SERVERS : UDP/TCP 53 WIN10_PRO  -->  NTP_SERVER  : UDP 123`

## ❌ **Deny Rules**

`ANY_REMOTE  -->  WIN10_PRO : ANY (Inbound)`

**Purpose:**  
Prevents unauthorized inbound connections to the workstation unless part of an established session.

`WIN10_PRO  -->  VLAN20 : ANY (Outbound except HTTPS 443)`

**Purpose:**  
Prevents users from accessing internal servers directly.