```ad-note
Spoofing, Tampering, Repudiation, Information Disclosure, Denial of Service (DoS), and Elevation of Privilege.

Stride full form:

The full form of “STRIDE” in the context of security is a mnemonic representing a model used to identify computer security threats
```

```ad-important
- threat modeling process should answer the following four questions:
  
1. What are we working on?
2. What can go wrong?
3. What are we going to do about it?   
4. Did we do a good enough job, at securing our threat landscape? 
```
 
   
|                                                           |              |               |                 |                            |                       |                            |
| --------------------------------------------------------- | ------------ | ------------- | --------------- | -------------------------- | --------------------- | -------------------------- |
| **Scenario**                                              | **Spoofing** | **Tampering** | **Repudiation** | **Information Disclosure** | **Denial of Service** | **Elevation of Privilege** |
| **web application attack.**                               |              | X             |                 | X                          | X                     |                            |
| **Database Service Exposure.**                            |              | X             | X               | X                          |                       |                            |
| **Misconfiguration Risks (Docker, SIEM, Database, ERP)**  | X            | X             | X               | X                          | X                     |                            |
| **Certificate Misconfiguration (Wazuh)**.                 |              | X             | X               | X                          | X                     | X                          |
| **User Endpoint Vulnerability (Windows 10 Workstation).** | X            | X             |                 | X                          |                       | X                          |
