
# Inbound Rules (VLAN 20 - VLAN 30)

| Port | Protocol | Purpose                 | Action    | Logging |
| ---- | -------- | ----------------------- | --------- | ------- |
| 88   | TCP/UDP  | Kerberos authentication | **ALLOW** | Enabled |
| 389  | TCP      | LDAP directory queries  | **ALLOW** | Enabled |
| 636  | TCP      | LDAPS (secure LDAP)     | **ALLOW** | Enabled |
| 53   | UDP      | DNS queries             | **ALLOW** | Enabled |


