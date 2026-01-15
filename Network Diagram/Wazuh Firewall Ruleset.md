

 ```ad-important
 Allow: Inbound Traffic
 
 - AD & Kerberos --> From VLAN30 (172.168.30)
 - PostgreSQL --> From VLAN20 (172.168.20)
 - windows 10 --> From IP 192.168.1 (Agent data)
 - Tryton Web App & Customer portal --> VLAN 20 (172.168.20)
 
 ```

```ad-important

Allow: OutBound Traffic

- Kerberos, Wazuh manager need to communicate with kerberos to establihs a ticket for proper sessions. Connecting the SIEM to the Domain Controller.
This will ensure the process of session renewals. Overall allowing scope and controll

Ports: 
UPD/TCP 88 -> Kerberos ticket request
TCP 636 -> LDAPS, allowing directories to be identified securley
UDP 123 -> Time sync  



```






```ad-info
- Wazuh Manager establishes communication with the wazuh agent.  Allowing the manager to communicate with the clients via 1514 TCP or UDP
  
  



```

