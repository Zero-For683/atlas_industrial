## ✅ **Allow Rules**

`WIN10_PRO  -->  TRYTON_WEB_APP : TCP 443 (Outbound)`

**Purpose:**  
Allows the workstation to connect to the Tryton Web App via HTTPS.  
(443 is forwarded by OPNsense → Tryton server port 8000 inside VLAN20.)

`TRYTON_WEB_APP  -->  WIN10_PRO : ESTABLISHED/RELATED`

**Purpose:**  
Allows return traffic from the Tryton server so the web session loads properly.

`WIN10_PRO  -->  DNS_SERVERS : UDP/TCP 53 WIN10_PRO  -->  NTP_SERVER  : UDP 123`

**Purpose:**  
DNS resolves the Tryton hostname; NTP keeps Kerberos time in sync (required for authentication).