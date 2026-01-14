# Installation

```ad-summary
https://hub.docker.com/r/tryton/tryton

Alternatively, you can deploy and download with a compose file. This is what we should do incase we need to add more dependencies associated with tryton. 
```

An `.env` file must be created in the same directory as the `.yml` compose file to create the variables that are generated automatically in the `docker.yml` file that is pulled. Alternatively, we can build a `docker-compose` file manually. 

> [!example] `DB_PASSWORD`, `PASSWORD`, `DB_NAME`

> [!danger] We need to ***ensure*** that the `.env` file has the proper permissions so passwords cannot be accessed in cleartext
> 

**Tryton ERP (open-source enterprise resource planning suite)**

# Why It Fits Atlas Industrial LLC

```ad-summary
Tryton is a modular, open-source ERP designed for managing inventory, production, supply chains, and accounting workflows. This aligns directly with Atlas Industrial’s mission to manufacture and distribute computer parts. Because Tryton includes modules for:

- Inventory and warehouse management
- Procurement and order tracking
- Production workflows
- Sales and customer management
- Supply-chain tracking
- Integrated PostgreSQL support

…it enables Atlas Industrial LLC to track goods from production to delivery, supporting the organization’s critical capabilities and critical requirements defined in Stage 1.
```

In other words, Tryton enhances the company’s ability to produce and deliver goods on time — a core mission element.
# **Why We Chose It (Technical Justification)**

1. **Open-source and cost-effective**  
    Reduces overhead compared to commercial ERPs.
    
2. **Runs reliably on Linux**  
    Fits the Linux server provided in our environment.
    
3. **Docker-ready**  
    Allows our team to deploy, test, and maintain it efficiently using containerization.
    
4. **Modular design**  
    Let’s the company only deploy the components needed for an industrial workflow.

# **Operational Due Diligence Findings**

During testing, `Tryton` was deployed successfully on Ubuntu 24.04 using Docker. The team validated:

- `Tryton` container launched and communicated with the DB
- Web interface reachable externally
- Database creation and login functional(TBD)
- ERP modules load correctly(TBD)

This confirms `Tryton` is a viable and stable business application for Atlas Industrial’s operational environment.
# **Risks and Vulnerabilities Introduced**

## Risks and Vulnerabilities Introduced

### 1. Web Application Exposure
- As a browser-based ERP, `Tryton` may be targeted by:
  - Brute-force login attempts
  - SQL injection (if improperly configured)
  - Cross-site scripting

### 2. Database Sensitivity
- Stores financial, inventory, and customer data.
- Requires strict access control, encryption, and backup policies.

### 3. **Misconfiguration Risk**
- Incorrect Docker or database configuration could expose ports publicly.
- Requires proper firewalling and network segmentation.

### 4. Legacy Integration Challenges
- `Tryton` may not fully integrate with outdated (legacy) systems, creating data inconsistency risks.

![[Tryton Command.jpg]]