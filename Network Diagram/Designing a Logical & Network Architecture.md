# Logical Architecture Diagram

![[Logical Architecture.png]]


# Network Architecture Diagram

![[Network_Diagram.drawio.png]]


# Network Layout Justification

> [!NOTE]  The environment is segmented into dedicated network zones to enforce least privilege, reduce lateral movement, and protect critical assets.

### Network Segments

- **User Network (192.168.1.0/24)**  
    Hosts Windows 10 user workstations. This network represents the highest-risk zone due to direct user interaction and exposure to phishing or malware.
    
- **Application Network – VLAN20 (172.168.20.0/24)**  
    Hosts the Tryton ERP web application and customer portal services. This network is semi-trusted and only accessible over HTTPS.
    
- **Database Network – VLAN30 (172.168.30.0/24)**  
    Hosts the PostgreSQL database used by Tryton. This network is fully isolated and only allows inbound connections from the application network.
    
- **SIEM Network – VLAN10 (172.168.10.0/24)**  
    Hosts the `Wazuh` SIEM. This network acts as a centralized log collection zone and does not initiate connections to other internal networks.
    
- **Authentication Services (Windows Server 2025)**  
    Provides Active Directory, Kerberos, DNS, and time synchronization. Authentication traffic is tightly scoped and explicitly allowed only where required.
    

> [!IMPORTANT]  This segmentation ensures that a compromise in one area (for example, a Windows 10 workstation) does not provide unrestricted access to critical backend systems.

---

## Perimeter and External Access

> [!NOTE]  External access is strictly limited to a single hardened entry point.

External users access the `Tryton` service exclusively through **HTTPS (TCP 8000)** exposed on the OPNsense firewall. A port forward maps inbound HTTPS traffic to the `Tryton` ***customer portal*** hosted in VLAN20.

### Design Principles

- No direct exposure of databases, SIEM, or authentication services to the internet    
- External users interact only with the web application layer
- All inbound traffic is statefully inspected and logged

> [!IMPORTANT]  This approach mirrors real-world enterprise design where a single hardened entry point protects internal services.

---

## Internal Access Controls

### Windows 10 User Access

> [!NOTE] Windows 10 workstations are treated as untrusted endpoints and are restricted to only the services required for business operations.

#### Allowed Actions

- Access the `Tryton` web application over HTTPS
- Communicate with Active Directory services for authentication, DNS, and time synchronization
- Send logs to the `Wazuh` SIEM via agent communication ports

#### Explicitly Blocked

- Direct access to databases
- Direct access to the SIEM management interface
- Access to firewall management interfaces

> [!IMPORTANT]  These controls prevent compromised user endpoints from being used for lateral movement or privilege escalation.

---

## Application and Database Isolation

> [!NOTE]  The application and database tiers follow a strict one-way trust model.

### Tryton Web Application

The `Tryton` application is permitted to communicate with:

- PostgreSQL on TCP 5432 only
- Active Directory services for Kerberos-based authentication
- The `Wazuh` SIEM for log delivery

> [!info] Since the `PostgreSQL` database will be hosted on `Windows 2025 Server`, it will be segmented via a separate IP address through a docker container
### Database Server

- Accepts connections only from the Tryton application
- Does not initiate outbound connections
- Has no direct exposure to users or external networks

> [!IMPORTANT] This design significantly reduces the blast radius of an application-level compromise.

---

## SIEM and Monitoring Architecture

> [!NOTE] The SIEM operates in a receive-only posture to avoid becoming an attack pivot. The only workstation that can access `Wazuh`'s dashboard is the `Windows 2025 Server`.

- Endpoints, servers, and firewalls send logs to `Wazuh`
- `Wazuh` does not initiate sessions to user, application, or database networks
- All denied firewall traffic is logged and forwarded for analysis

> [!IMPORTANT] This ensures centralized visibility while preventing the SIEM itself from being abused for lateral movement.

---

## Authentication and Kerberos Controls

> [!NOTE] Authentication services are tightly scoped to reduce credential exposure.

- Only required ports are allowed (88, 389, 636, 53, 123)
- Services authenticate through the domain controller instead of using local credentials
- Time synchronization is enforced to support Kerberos ticket validity

> [!IMPORTANT] Restricting authentication traffic limits opportunities for credential abuse and ticket attacks.

---

## Firewall Management and Administrative Access

> [!danger]  Firewall and infrastructure management interfaces are treated as critical assets.

- Access restricted to a designated administrative workstation
- Inaccessible from user, application, or external networks
- All management activity is logged for auditing and incident detection

> [!IMPORTANT] This prevents unauthorized configuration changes and reduces the risk of attacker persistence through firewall compromise. 


# Tryton Firewall Rules


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


# Wazuh Firewall Ruleset

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


# Windows 10 Firewall Ruleset


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


# Windows 2025 Server Firewall Rules

## **Allow - Inbound**

`VLAN20_TRYTON -> HOST_WIN2025`: TCP 88 (Kerberos authentication)  
`VLAN20_TRYTON -> HOST_WIN2025`: TCP 389 (LDAP directory queries)  
`VLAN20_TRYTON -> HOST_WIN2025`: TCP 636 (LDAPS secure queries)  
`VLAN20_TRYTON -> HOST_WIN2025`: UDP 53 (DNS resolution)
`VLAN20_TRYTON -> HOST_WIN2025`: TCP 443 (Admin panel for Tryton)

`VLAN50_USERS -> HOST_WIN2025`: TCP 88 (Kerberos authentication)  
`VLAN50_USERS -> HOST_WIN2025`: TCP 389 (LDAP queries)  
`VLAN50_USERS -> HOST_WIN2025`: TCP 636 (LDAPS)  
`VLAN50_USERS -> HOST_WIN2025`: TCP 445 (SMB for Group Policy)  
`VLAN50_USERS -> HOST_WIN2025`: TCP 135 (RPC endpoint mapper)  
`VLAN50_USERS -> HOST_WIN2025`: TCP 49152-65535 (RPC dynamic ports)  
`VLAN50_USERS -> HOST_WIN2025`: UDP 53 (DNS queries)

`HOST_ADMIN_WS (192.168.1.10) -> HOST_WIN2025`: TCP 3389 (RDP admin access only)

## **Allow - Outbound**

`HOST_WIN2025 -> HOST_WAZUH (172.168.10.0/24)`: TCP 1514/1515 (Wazuh agent logs to SIEM)
`VLAN20_TRYTON -> HOST_WIN2025`: TCP 443 (Admin panel for Tryton)
## **Deny - Inbound**

`VLAN50_USERS_OTHER -> HOST_WIN2025`: TCP 3389 (block RDP from non-admin workstations)  
`ANY -> HOST_WIN2025`: ALL other traffic (default deny)

## **Deny - Outbound**

`HOST_WIN2025 -> INTERNET`: ALL (CRITICAL - AD must never access Internet)  
`HOST_WIN2025 -> VLAN20_TRYTON`: ALL (no reverse connections to application servers)  
`HOST_WIN2025 -> VLAN50_USERS`: ALL (server doesn't initiate to clients)  
`HOST_WIN2025 -> ANY`: ALL other traffic (default deny)

## **Configurations**

 **Windows Defender Firewall enabled on all profiles (Domain, Public, Private)**

 **Default action: BLOCK all inbound and outbound traffic (explicit allow only)**

**All denied traffic is logged and forwarded to Wazuh SIEM for analysis**

 **CRITICAL ALERT if AD server attempts Internet connection (indicates compromise)**

