# **1. Rule Purpose**

Allow internal authenticated users on the **Windows 10 Pro workstation (VLAN30)** to access the **Tryton Web App** hosted on **172.168.20.x:8000**.

This supports business operations while maintaining network segmentation.

# **2. Network Zones Involved**

|Zone|Description|Subnet|
|---|---|---|
|**VLAN30 – Workstations**|Internal user endpoints|192.168.30.0/24|
|**VLAN20 – Server Network**|Tryton server + PostgreSQL|172.168.20.0/24|
|**OPNsense LAN Interfaces**|Enforces segmentation and rules|—|
# **3. Final Firewall Rule Table (Submission-Ready)**

| Priority | Rule Name                  | Source          | Destination  | Protocol/Ports | Action | Log? | Purpose                      |
| -------- | -------------------------- | --------------- | ------------ | -------------- | ------ | ---- | ---------------------------- |
| 1        | ALLOW_VLAN30_TO_TRYTON_WEB | 192.168.30.0/24 | 172.168.20.x | TCP/8000       | ALLOW  | Yes  | Access Tryton Web App        |
| 2        | ALLOW_VLAN30_KERBEROS      | VLAN30          | AD Server    | TCP 88,389,445 | ALLOW  | Yes  | Domain auth                  |
| 3        | ALLOW_DNS                  | VLAN30          | DNS Server   | UDP/53         | ALLOW  | Yes  | Hostname resolution          |
| 4        | ALLOW_WEB_OUTBOUND         | VLAN30          | ANY          | TCP/80,443     | ALLOW  | No   | Optional → Internet browsing |
| 5        | BLOCK_VLAN30_TO_SERVER_LAN | VLAN30          | VLAN20       | ANY            | BLOCK  | Yes  | Prevent server access        |
| 6        | DENY_ALL_VLAN30            | VLAN30          | ANY          | ANY            | BLOCK  | Yes  | Zero-trust enforcement       |
