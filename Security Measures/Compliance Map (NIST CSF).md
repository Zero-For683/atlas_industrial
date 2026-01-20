

| **System** | **Security Control**    | **Control Description**                                                  | **Function** |
| ---------- | ----------------------- | ------------------------------------------------------------------------ | ------------ |
| Windows 10 | OS Hardening            | Remove unnecessary default services                                      | protect      |
| Windows 10 | Authentication Control  | Strong Passwords/ lock out policies                                      | Protect      |
| Windows 10 | Patch Management        | Windows updates                                                          | Protect      |
| Tryton     | Secure server configs   | minimal services/ SSH keys                                               | Protect      |
| Tryton     | Access Control          | Least Privilege/ role based access                                       | Protect      |
| Tryton     | Logs & Audits           | App logs sent to Wazuh                                                   | Detect       |
| Post       | Database Access control | Role based permissions                                                   | Protect      |
| Post       | Data Encryption         | Encryption for data in transit                                           | Protect      |
| Post       | Backup and recovery     | Encrypted back up data                                                   | Recover      |
| Wazuh      | Log Aggregation         | Logs in a centralized location- ingested from endpoint(s) and servers    | Detect       |
| Wazuh      | Threat Detection        | prompt alerts from failed logins, priv escalation, and policy violations | Detect       |
| Wazuh      | Integrity Monitoring    | File integrity tracking on servers                                       | Detect       |
| Host OS    | Endpoint Monitoring     | Host based intrusion detection with host based firewalls                 | Detect       |
| Host OS    | Incident Response       | Alert procedures defined                                                 | Respond      |
| Host OS    | System Recovery         | OS restoration from secure backups                                       | Recover      |

```ad-info

All controls follow the NIST framework(CSF)

https://csf.tools/reference/nist-cybersecurity-framework/v1-1/pr/pr-ac/?utm_source=chatgpt.com

https://www.nist.gov/cyberframework/quick-start-guides

https://www.nist.gov/cyberframework/getting-started/online-learning

```
