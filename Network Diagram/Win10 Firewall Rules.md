
> [!info] These rules ensure that the workstation can only reach the `Tryton` web app customer portal, and `kerberos`. All other traffic is blocked and logged for SIEM analysis.
# Allow outbound rules

`Win10 -> Tryton`: TCP 443 (internal access to the web page)
`Win10 -> Win SERV`: To the DC (`53 TCP/UDP` & `123 UDP` for DNS & NTP)
`Win10 -> Kerberos`: To the DC (TCP/UDP `88`)
`Win10 -> Wazuh Manager`: TCP 1514/1515 for the agent to deliver data


# Allow inbound rules

`Kerberos` -> `Win SERV`: Allow inbound traffic to negotiate and handle Kerberos interactions
`Win10 -> Win SERV`: From the DC (`53 TCP/UDP` & `123 UDP` for DNS & NTP)

# Deny

`NET_USERS -> ANY: ALL`

# Configurations

> [!danger] `NET_USERS` can NOT be able to access the OPNsense firewall's admin interface

> [!important] Firewall management access restricted to a designated admin workstation only

> [!important] All denied traffic is logged for SIEM ingestion


