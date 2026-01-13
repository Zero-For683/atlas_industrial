


# An open-source application to serve as a **critical business system**.

```ad-summary
Seems like we need an ERP to solve most of our problems. Below are a list of some we can use. 
```

<<<<<<< HEAD
- `Axelor` - https://axelor.com/installation-guide/
- `Tryton` - https://www.tryton.org/download >>>
- `Dolibarr` - https://www.dolibarr.org/downloads.php
=======
- `Tryton` - https://www.tryton.org/download
>>>>>>> origin/main


# A **SIEM solution** for monitoring and logging.

- `Splunk`
- `Wazuh` 
- `Security Onion`
- `Elk Stack` (elasticsearch / logstash / kibana)

# A **database solution** to support the SIEM or critical application.

- `PostgreSQL`


# An **endpoint platform** (Windows/Linux) to represent the user environment.

> [!summary] User environment on Windows 11 may be the best choice, given that users (always the most vulnerable in any given system) will be the biggest weakness. So it'll be freshly patched and easy to log / monitor, and we can utilize this system to interact with the backend systems (SIEM, DB, and Tryton)

> [!summary] The SIEM will be hosted on Windows 10. It's not ideal, but Windows 10 is hardened and has extensive logging and hardening capabilities. 

> [!summary] For PostgreSQL, RedHat is a great choice since it'll provide long-term stability, fewer surprises, and straightforward hardening. Setup will be slightly more complicated. We're choosing PostgreSQL fits very well in this case since it's scalable, reliable, easy to use, and it has built-in functionality with Tryton, our main business application

> [!summary] For the web application, Ubuntu is the obvious choice. Ubuntu minimizes operational friction, it's easy to setup & build solutions, as well as hardening. Tryton will be used for it's extensive documentation, and clear support in every aspect of our CoG and our mission statement. It supports financing, billing, shipping, and much more. Additionally we can install plugins to further add whatever we may need in the future.

| User Environment | Windows 10 Pro         |
| ---------------- | ---------------------- |
| PostgreSQL       | Windows 11 Server      |
| SIEM             | Linux (distro?)        |
| Tryton           | Linux Ubuntu 24.04 LTS |
