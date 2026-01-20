_(User Roles, Permissions, and Restrictions)_

This matrix enforces **least privilege**, limits lateral movement, and aligns with how your architecture was designed.

---

## **Access Control Matrix – Atlas Industrial**

|**Role**|**Windows 10 User**|**Tryton ERP**|**PostgreSQL DB**|**Wazuh SIEM**|**OPNsense Firewall**|**Active Directory**|
|---|---|---|---|---|---|---|
|**Standard User**|Login, local apps|Web access only|❌ No access|❌ No access|❌ No access|Auth only|
|**Business Analyst**|Login, reporting tools|Read / limited write|❌ No access|Read alerts|❌ No access|Auth only|
|**SOC Analyst**|Login, admin tools|❌ No access|❌ No access|Full monitoring|❌ No access|Read auth logs|
|**System Administrator**|Admin login|Admin|DB admin|Admin|❌ No access|Admin|
|**Database Administrator**|Admin login|❌ No access|Full admin|❌ No access|❌ No access|Service auth|
|**Security Administrator**|Admin login|❌ No access|❌ No access|Admin|Firewall admin|Admin|
|**Service Account**|❌ No login|App auth only|Limited role|Log shipping|❌ No access|Kerberos only|

## **Access Control Notes**

- **No users** have direct database access except DB administrators.
    
- **Firewall access** is restricted to **Security Administrators only**.
    
- **Service accounts** are non-interactive and Kerberos-restricted.
    
- **Users authenticate via AD**, but permissions are enforced at the application and network layer.