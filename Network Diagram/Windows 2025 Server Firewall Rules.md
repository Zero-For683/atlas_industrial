
# Allow

`VLAN20_TRYTON -> HOST_WIN2025`: TCP 88 (Kerberos authentication)  
`VLAN20_TRYTON -> HOST_WIN2025`: TCP 389 (LDAP directory queries)  
`VLAN20_TRYTON -> HOST_WIN2025`: TCP 636 (LDAPS secure queries)  
`VLAN20_TRYTON -> HOST_WIN2025`: UDP 53 (DNS resolution)

`VLAN50_USERS -> HOST_WIN2025`: TCP 88 (Kerberos authentication)  
`VLAN50_USERS -> HOST_WIN2025`: TCP 389 (LDAP queries)  
`VLAN50_USERS -> HOST_WIN2025`: TCP 636 (LDAPS)  
`VLAN50_USERS -> HOST_WIN2025`: TCP 445 (SMB for Group Policy)  
`VLAN50_USERS -> HOST_WIN2025`: TCP 135 (RPC endpoint mapper)  
`VLAN50_USERS -> HOST_WIN2025`: TCP 49152-65535 (RPC dynamic ports)  
`VLAN50_USERS -> HOST_WIN2025`: UDP 53 (DNS queries)

`HOST_ADMIN_WS (192.168.1.10) -> HOST_WIN2025`: TCP 3389 (RDP admin access only)

`HOST_WIN2025 -> HOST_WAZUH`: TCP 1514/1515 (Wazuh agent logs to SIEM)

# Deny 

`HOST_WIN2025 -> INTERNET`: ALL (CRITICAL - AD must never access Internet)  
`VLAN50_USERS_OTHER -> HOST_WIN2025`: TCP 3389 (block RDP from non-admin workstations)  
`HOST_WIN2025 -> ANY`: ALL other outbound traffic (default deny)  
`ANY -> HOST_WIN2025`: ALL other inbound traffic (default deny)