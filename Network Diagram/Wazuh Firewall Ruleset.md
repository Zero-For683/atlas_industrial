

 ```ad-important
 Allow: 
 
 - AD & Kerberos --> From VLAN30 (172.168.30)
 - PostgreSQL --> From VLAN20 (172.168.20)
 - windows 10 --> From IP 192.168.1 (Agent data)
 - Tryton Web App & Customer portal --> VLAN 20 (172.168.20)
 
 ```

```ad-info
Port 1514- Wazuh Manager establishes communication with the wazuh agent.  Allowing the manager to communicate with the clients via 1514 TCP or UDP



```