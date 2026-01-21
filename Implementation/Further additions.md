>[!danger] Delete all `phorenzics` users. There are spies amongst us 

>[!danger] Once all the applications are stood up, delete all `.env` and `docker-compose.yml`/`compose.yml` files. They contain passwords in cleartext
>.
>Also delete browser history incase creds were leaked when generating passwords


# Service accounts

We need to create 3 service accounts:
- `postgresql-svc`
- `tryton-svc`
- `wazuh-svc`

These accounts cannot have `ssh` access. Set their `/etc/passwd` line to `/usr/bin/nologin`. Their home directories need to be the root of the docker volume created

```ad-important
Delete any and all `phorenzics` accounts off the systems. They're on all 4. 
```

---

# Vault warden

We will need to setup `vaultwarden` on the `wazuh-ubuntu` machine. 
https://github.com/dani-garcia/vaultwarden

We will separate access to who has what credentials so if one account/login gets compromised, the whole database isnt leaked

Gabriel:
- Wazuh stack (manager, dashboard, kibana, etc)

Chuck:
- PostgreSQL admin panel

Johnny:
- Domain admin (He's the AD guy)

Chris:
- Tryton ERP (trytondb database credentials)


---

# DNS

For DNS, resolve each hostname

Windows 2025 server:
- `dc01.atlas.corp`
- `pg.atlas.corp` (DNS record for app)

Windows 10: 
- `wkst01.atlas.corp`

Tryton-Ubuntu:
- `app01.atlas.corp`
- `tryton.atlas.corp` (DNS record for app)

Wazuh-Ubuntu:
- `siem01.atlas.corp`
- `wazuh-dashboard.atlas.corp` (DNS record for app)
- `wazuh-api.atlas.corp` (DNS record for app)
- `wazuh-indexer.atlas.corp` (DNS record for app)


---


# SSH Certificates


- `app01.atlas.corp` will host the `SSH User CA private key` and signs user certificates. 

Both `app01` and `siem01` will trust the CA public key
Only `admin` and `user` accounts can SSH (certificate-only)


With that said not everyone will have full admin access to the ubuntu machines.


Wazuh-Ubuntu:
- Admin: Gabriel, Chuck
- User: Johnny, Chris

Tryton-Ubuntu:
- Admin: Johnny, Chris
- User: Gabriel, Chuck


ACCOUNTS:
- `gabriel_soc@atlas.corp`
- `chuck@atlas.corp`
- `johnny@atlas.corp`
- `chris@atlas.corp`

>[!note] The keys will be stored in the user session on Windows 2025 Server separately for each user in a secure spot (TBD)


---

# Firewalls (DO THIS LAST)


Setup the firewalls and only allow inbound/outbound connections to specific IP addresses. 

This way Windows 10 cant directly communicate with 2025's server



# Setup log aggregation (DO THIS LAST)

Configure what sorts of logs the agents pull, and build SIEM queries. 

Its important to do this AFTER the firewall step so we can pull from it. SSSSSs