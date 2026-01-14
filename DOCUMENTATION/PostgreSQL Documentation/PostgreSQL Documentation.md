# Installation

```ad-summary
https://hub.docker.com/_/postgres

PostgreSQL will be deployed using the official Docker image and a docker-compose.yml file to provide a reliable, containerized database service for Atlas Industrial.
```

An .env file must be created in the same directory as the .yml compose file to securely 
store PostgreSQL authentication credentials (username, password) and configuration 
parameters (database name, data directory path). Alternatively, we can define these 
values directly in a manually-built docker-compose file.

> [!example] `POSTGRES_USER`, `POSTGRES_PASSWORD`, `POSTGRES_DB`

> [!danger] We need to ***ensure*** that the `.env` file has the proper permissions so passwords cannot be accessed in cleartext
> 

**PostgreSQL (open-source relational database management system)**

# Why It Fits Atlas Industrial LLC

```ad-summary
PostgreSQL is a powerful, open-source relational database designed for managing 
transactional and analytical data. This aligns directly with Atlas Industrial's need 
for reliable storage of operational data. Because PostgreSQL provides:

• ACID-compliant transaction processing
• Strong data integrity and constraint enforcement
• JSONB and full-text search capabilities
• Integration with applications such as Tryton ERP for inventory and accounting

```

In other words, PostgreSQL enables Atlas Industrial LLC to store, manage, and analyze production, supply chain, and business data in a reliable, scalable database foundation that supports critical business operations.
# **Why We Chose It (Technical Justification)**

1. **Open-source and cost-effective**  
    No licenses needed, ACID compliance wit  enterprise level reliability.
    
2. **Runs reliably on Windows**  
    Fits the Windows server provided in our environment.
    
3. **Docker-ready**  
    Allows our team to deploy, test, and maintain it efficiently using containerization.
    
4. **Extensible architecture**  
    Allows custom features allowing filtered searches within the database without changing the core database engine.

# **Operational Due Diligence Findings**

During testing, PostgreSQL was deployed successfully on Microsoft Windows Server 2025 Standard Evaluation using Docker. The team validated:

- PostgreSQL container launched and accepted connections from application containers
- Database creation and user authentication functional via psql client
- Data persistence verified across container restarts using Docker volumes
- Backup and restore operations tested using pg_dump and pg_restore

This confirms PostgreSQL is a viable and stable business application for Atlas Industrial’s operational environment.
# **Risks and Vulnerabilities Introduced**

## Risks and Vulnerabilities Introduced

### 1. **Database Service Exposure**
- If port **5432** is exposed to untrusted networks, PostgreSQL becomes a target for brute-force authentication attacks, SQL injection attempts, and protocol-level exploits.
- **Mitigation:** Restrict access using Windows Firewall rules and network segmentation; enforce strong passwords; enable SSL/TLS for encrypted connections; consider changing the default port.

### 2. **Sensitive Data Storage**
- Stores financial, inventory, and customer data. 
- Requires strict access control, encryption, backup policies, enable audit logging for all administrative actions.

### 3. **Misconfiguration Risk**
- Incorrect Docker or database configuration could expose ports publicly.
- Default credentials, weak authentication rules, and disabled logging.

### 4. Legacy Integration Challenges
- PostgreSQL may not fully integrate with outdated (legacy) systems, creating data inconsistency risks.

Changes made from default configurations:
- Enabled logging via .conf files 
- Ensured the encryptions are conducted with scram-sha-256
- Created inbound rule for the port PostgreSQL will be utilizing