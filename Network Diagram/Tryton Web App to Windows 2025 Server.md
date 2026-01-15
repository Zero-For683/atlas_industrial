The following prevents lateral movement across other identity services. All authentication attempts will be logged to the Wazuh SIEM.
# Inbound Rules (VLAN 20 - VLAN 30)

| Port      | Protocol | Purpose                 | Action    | Logging |
| --------- | -------- | ----------------------- | --------- | ------- |
| 88        | TCP/UDP  | Kerberos authentication | **ALLOW** | Enabled |
| 389       | TCP      | LDAP directory queries  | **ALLOW** | Enabled |
| 636       | TCP      | LDAPS (secure LDAP)     | **ALLOW** | Enabled |
| 53        | UDP      | DNS queries             | **ALLOW** | Enabled |
| All other | ANY      | Everything else         | **DENY**  | Enabled |

# Allow

`VLAN20_TRYTON -> HOST_WIN2025`: TCP 88 (Kerberos authentication)  
`VLAN20_TRYTON -> HOST_WIN2025`: TCP 389 (LDAP directory queries)  
`VLAN20_TRYTON -> HOST_WIN2025`: TCP 636 (LDAPS secure directory queries)  
`VLAN20_TRYTON -> HOST_WIN2025`: UDP 53 (DNS resolution)

# Deny

`VLAN20_TRYTON -> HOST_WIN2025`: ALL other ports (blocked & logged)  
`VLAN20_TRYTON -> VLAN30_ANY`: ALL (no access to rest of VLAN 30)