
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

# Vulnerability Capability Overlap
The Venn diagram analysis shows a critical problem: several critical requirements (CR) fall within the critical vulnerability (CV) area, which means essential business functions become exposed to threats.
- **Inventory Management:** The Tryton ERP system is vulnerable to web attacks. Compromised inventory data leads to disrupted order fulfillment and scheduled production.
- **Supplier Relationships:** Supply chain communication and data in the ERP are exposed to database attacks and possible credential theft, causing a chance of disrupting deliveries. 
- **Company Employee Portal:** Phishing attacks (Attack Vector #1) target employee credentials, creating unauthorized access and lateral movement to critical systems.
- **Auditors, Call Center, Legal Team:** These roles require access to sensitive data, making them high value targets for credential theft and social engineering attacks.
- **Data Center:** Houses all critical infrastructure (Tryton, PostgreSQL, Wazuh, Active Directory, Kerberos). Insider threats and privileged access vulnerabilities could enable system wide compromise.
 Atlas Industrial's most critical business functions are exposed to its most significant vulnerabilities. Protecting the inventory system, employee authentication, database integrity, and data center security is not just an IT priority it's a business survival imperative. Any compromise to these systems directly undermines the organization's Center of Gravity: operational reliability that sustains customer trust.


# Risk Severity
Atlas Industrial's Risk Register identifies **five HIGH severity threats** that pose significant danger to the organization's operational capabilities and Center of Gravity. All five risks exhibit high impact across the CIA triad, with varying likelihood ratings that inform prioritization decisions.

| Risk                           | System                   | Likelihood | Impact | CIA Impact                         | Risk Level |
| ------------------------------ | ------------------------ | ---------- | ------ | ---------------------------------- | ---------- |
| Web Application Attack Surface | Tryton ERP               | High       | High   | C: High, <br>I: High, <br>A:Medium | HIGH       |
| Database Service Exposure      | PostgreSQL               |            |        |                                    |            |
| Misconfiguration Risks         | Docker/SIEM/Database/ERP |            |        |                                    |            |
| Certificate Misconfiguration   | Wazuh SIEM               |            |        |                                    |            |
| User Endpoint Vulnerability    | Windows 10 Workstation   |            |        |                                    |            |

