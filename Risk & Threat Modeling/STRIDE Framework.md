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
 
   
|                                                                                                                                                           |              |               |                 |                            |                       |                            |
| --------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------ | ------------- | --------------- | -------------------------- | --------------------- | -------------------------- |
| **Scenario**                                                                                                                                              | **Spoofing** | **Tampering** | **Repudiation** | **Information Disclosure** | **Denial of Service** | **Elevation of Privilege** |
| **Sending a spoofed email, wherein the mail gateway lacks email security and logging configuration.**                                                     | ✔            |               | ✔               |                            |                       |                            |
| **Flooding a web server with many requests that lack load-balancing capabilities.**                                                                       |              |               |                 |                            | ✔                     |                            |
| **Abusing an SQL injection vulnerability.**                                                                                                               |              | ✔             |                 | ✔                          |                       |                            |
| **Accessing public cloud storage (such as AWS S3 bucket or Azure blob) that handles customer data**.                                                      |              |               |                 | ✔                          |                       |                            |
| **Exploiting a local privilege escalation vulnerability due to the lack of system updates and modifying system configuration for a persistent backdoor.** |              | ✔             |                 |                            |                       | ✔                          |
