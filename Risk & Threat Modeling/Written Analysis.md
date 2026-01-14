
# Executive Summary
- Atlas Industrial LLC operates as an online manufacturer and distributor of high-quality computer components, with a mission to provide reliable, efficient technology solutions to businesses and consumers. The organization's Center of Gravity as identified in the organizational analysis is "operational reliability that sustains customer trust." This means that any disruption to system availability, data integrity, or confidentiality directly threatens the core value proposition that keeps customers choosing Atlas Industrial over competitors.

# Threat Landscape Overview
- **External Threats:** Financially motivated cybercriminals targeting customer data and payment information, opportunistic attackers exploiting public facing web services like our Tryton ERP system, and potential supply chain attackers that want to disrupt Atlas's technology manufacturing and distribution operations. 
- I**Internal Threats:**  Malicious insiders with privileged access to sensitive systems (data center, legal team, auditors), employees susceptible to common but neglected attacks such as phishing, social engineering, and potential policy violations or log tampering that could make malicious activity go unseen.

# Critical Systems at Risk
The organization's operational capability depends on several interconnected systems identified in the threat model. 
- **Tryton ERP System (Ubuntu):** Manages Inventory, production workflows, customer orders, and business data.
- **PostgreSQL Database (TCP 5432):** Stores customer data, financial records, and ERP information.
- **Wazuh SIEM/Ubuntu:** Collects security logs and provides visibility into threats.
- **Active Directory/Kerberos:** Controls authentication and authorization across the database.
- **Windows 10 User Workstation:** Entry point with little to no support, becomes an easy target for phishing and credential theft attacks. 
