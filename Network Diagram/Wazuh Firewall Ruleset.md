


> [!info] Wazuh Manager establishes communication with the wazuh agent.  Allowing the manager to communicate with the clients via 1514/1515 TCP & UDP

## **Allow - Inbound**

 Allow: Inbound Traffic from Agents on port 1515/1514:
- `AD` & `Kerberos` --> From VLAN30 (172.168.30)
- `Windows 10` --> From IP DMZ (192.168.1)
- `Tryton Web App` & `Customer portal` --> VLAN 20 (172.168.20)
- `2025 SERV` -> `UDP/TCP 88` (Kerberos)
- `2025 SERV` -> `TCP 636` (LDAPS)
- `2025 SERV` -> `UDP 123` Network Time Protocol (time syncs)

## Allow - Outbound


> [!info] Wazuh manager needs to communicate with kerberos to establish tickets for sessions. This will make session renewals and session creations possible.

- `2025 SERV` -> `UDP/TCP 88` (Kerberos)
- `2025 SERV` -> `TCP 636` (LDAPS)
- `2025 SERV` -> `UDP 123` Network Time Protocol (time syncs)


# Configurations

```ad-danger
**No outbound traffic should occur outside of the kerberos handshake.**

```


