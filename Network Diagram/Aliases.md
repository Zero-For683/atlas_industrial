```ad-summary
To make building firewalls a little easier, I build this out so we can keep everything organized so our heads arent spinning

I'll define the IPs we'll use here, and keep them static
```

- `NET_USERS = 192.168.1.0/24`
- `NET_SIEM = 172.168.10.0/24`
- `NET_APP = 172.168.20.0/24`
- `NET_DB = 172.168.30.0/24`
- `HOST_TRYTON = 172.168.20.1`
- `HOST_POSTGRES = 172.168.30.2` We will make the docker instance this IP for segmentation
- `HOST_WAZUH = 172.168.10.1`
- `HOST_ADDC = 172.168.30.1`
- `PORT_TRYTON_HTTPS = 443` When we enforce HTTPS
- `PORT_POSTGRES = 5432`
- `PORT_WAZUH_AGENT = 1514` (TCP/UDP depending on your setup), `1515/tcp`, `55000/tcp` (agent/API), `514/udp` (syslog, if used)
- `PORT_KERBEROS = 88/tcp,88/udp` and (if needed) `464/tcp,464/udp`
- `PORT_DNS = 53/tcp,53/udp`
- `PORT_NTP = 123/udp`