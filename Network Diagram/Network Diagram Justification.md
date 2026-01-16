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

