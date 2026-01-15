
# Allow outbound rules

`NET_USERS -> HOST_TRYTON`: TCP 443 (internal access to the web page)
`NET_USERS -> DNS/NTP`: To the DC (`53 TCP/UDP` & `123 UDP`)
`NET_USERS -> PORT_KERBEROS`: To the DC
`NET_USERS -> HOST_WAZUH`: TCP 1514/1515 for the agent to run

# Deny

`NET_USERS -> ANY: ALL`

# Configurations

> [!danger] `NET_USERS` can NOT be able to access the OPNsense firewall's admin interface

> [!important] Firewall management access restricted to a designated admin workstation only

> [!important] All denied traffic is logged for SIEM ingestion


