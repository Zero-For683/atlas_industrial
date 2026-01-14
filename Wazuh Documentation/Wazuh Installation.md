# INSTALLATION GUIDE

> [!important] For our purposes, its important we build using the `docker-compose.yml` for easy to keep and upgrade


# Clone the repo

`git clone https://github.com/wazuh/wazuh-docker`

We will be using their single-node deployment

Change into the single-node directory:
`cd wazuh-docker/single-node`

# Update to the latest stable version

```
docker compose down
git fetch --all --tags
git checkout v4.14.1
docker compose up -d
```

Then take it back down with `docker compose down -v`


# Creating certs

`Wazuh` requires HTTPS certificates for the dashboard and indexer.

https://documentation.wazuh.com/current/user-manual/wazuh-dashboard/certificates.html


Place the following `config.yml` file in the following directory: `wazuh-docker/single-node/config/config.yml`
```config.yml
nodes:
  # Wazuh indexer nodes
  indexer:
    - name: node-1
      ip: "<indexer-node-ip>"
    #- name: node-2
    #  ip: "<indexer-node-ip>"
    #- name: node-3
    #  ip: "<indexer-node-ip>"

  # Wazuh server nodes
  # If there is more than one Wazuh server
  # node, each one must have a node_type
  server:
    - name: wazuh-1
      ip: "<wazuh-manager-ip>"
    #  node_type: master
    #- name: wazuh-2
    #  ip: "<wazuh-manager-ip>"
    #  node_type: worker
    #- name: wazuh-3
    #  ip: "<wazuh-manager-ip>"
    #  node_type: worker

  # Wazuh dashboard nodes
  dashboard:
    - name: dashboard
      ip: "<dashboard-node-ip>"
```

Replace `- name:` and `ip: <indexer-node-ip>` with the images in the `docker-compose.yml` file (path is: `wazuh-docker/single-node/docker-compose.yml`). 

 

![[Pasted image 20260113224855.png]]

```ad-example
For example, in the picture, the name AND hostname are `wazuh.dashboard`, so in `config.yml` under each section we'd fill it in as so:

`dashboard:`
`    - name: wazuh.dashboard`
`    ip: "wazuh.dashboard"` 
```

---

Once the `config.yml` file is properly filled in, we need to download the script that generates the certs here, and place it in the same directory as the `config.yml` file:
https://packages.wazuh.com/4.14/wazuh-certs-tool.sh

Make it executable, and run it as so:

`bash wazuh-certs-tool.sh -A`

This will place all of the certificates in the directory: `wazuh-docker/single-node/config/wazuh-certificates/*`
# Copying the certificates into the necessary directories


Now, we need to copy all of the `.pem` files into the two following directories:
`/wazuh/wazuh-docker/single-node/config/`
`/wazuh/wazuh-docker/single-node/config/wazuh_indexer_ssl_certs/`

```ad-danger
`/wazuh/wazuh-docker/single-node/config/wazuh_indexer_ssl_certs/` may contain empty directory files with the names already created. You'll have to manually delete these directories before copying, or replace them with the actual `.pem` files.
```


# Troubleshooting

If the dashboard does not come up in the browser automatically, or there are errors, troubleshoot via restarting

```
docker compose down -v
docker compose up -d
```

If there are any missing certificate (`.pem`) files, then you'll need to copy them to the directories it wants. 

If you get an error browsing to `https://localhost` saying: `Wazuh Dashboard Server is not Ready yet`, then you may need to restart the manager via:
`docker compose restart wazuh.manager` (or any of the other 3 services that are preventing dashboard from loading)




