# INSTALLATION GUIDE

> [!important] For our purposes, its important we build using the `docker-compose.yml` for easy to keep and upgrade


1. Clone the repo

`git clone https://github.com/wazuh/wazuh-docker`

We will be using their single-node solution

Once cloned, `cd` into `wazuh-docker/single-node`

From here, we need to upgrade the system, so run the following:

```
docker compose down
git fetch --all --tags
git checkout v4.14.1
docker compose up -d
```

Then take it back down with `docker compose down -v`


2. Building certs

Next we need to build out certificates for `https`. 

https://documentation.wazuh.com/current/user-manual/wazuh-dashboard/certificates.html

Copy the `.yml` file, and replace the `- name:` and `ip: <indexer-node-ip>` with the images in the `docker-compose.yml` file (path is: `wazuh-docker/single-node/docker-compose.yml`) inside the `wazuh-docker/single-node/config` directory. 

![[Pasted image 20260113224855.png]]

For example, in the picture, the name AND hostname are `wazuh.dashboard`, so we fill in `config.yml`'s name and IP with `wazuh.dashboard` appropriately. 


Then, we need to download the certificate script from here: 
https://packages.wazuh.com/4.14/wazuh-certs-tool.sh

Make it executable, and run it as so:

`bash wazuh-certs-tool.sh -A`


3. Copying the certificates into the necessary directories


Now, we need to copy all of the `.pem` files into the two following directories:
`/wazuh/wazuh-docker/single-node/config/`
`/wazuh/wazuh-docker/single-node/config/wazuh_indexer_ssl_certs/`

```ad-danger
`/wazuh/wazuh-docker/single-node/config/wazuh_indexer_ssl_certs/` may contain empty directory files with the names already created. You'll have to manually delete these directories before copying, or replace them with the actual `.pem` files.
```


4. Restarting / managing

For good measure, run:

```
docker compose down -v
docker compose up -d
```

If there are any missing certificate (`.pem`) files, then you'll need to copy them to the directories it wants. 

If you get an error browsing to `https://localhost` saying: `Wazuh Dashboard Server is not Ready yet`, then you may need to restart the manager via:
`docker compose restart wazuh.manager` (or any of the other 3 services that are preventing dashboard from loading)




