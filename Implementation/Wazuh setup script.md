```bash
#!/usr/bin/env bash
set -euo pipefail

# deploy_wazuh_single_node.sh
# - Runs everything under /home/wazuh
# - Clones wazuh-docker, checks out v4.14.1
# - Writes config.yml in the format you want (indexer/server/dashboard)
# - Downloads + runs wazuh-certs-tool.sh -A
# - Replaces cert directories with generated PEMs (file-only)
# - Forces root-ca-manager.pem to be a FILE copied from root-ca.pem
# - Patches docker-compose.yml:
#     * WAZUH_API_URL=https://wazuh.manager:55000
# - Starts stack
# - Restarts manager at end

WAZUH_USER="wazuh"
WAZUH_PASS="1234"
BASE_DIR="/home/${WAZUH_USER}"
REPO_DIR="${BASE_DIR}/wazuh-docker"
SINGLE_NODE_DIR="${REPO_DIR}/single-node"
CERT_TOOL_URL="https://packages.wazuh.com/4.14/wazuh-certs-tool.sh"
TAG="v4.14.1"

need_root() {
  if [[ "${EUID}" -ne 0 ]]; then
    echo "Run as root: sudo $0"
    exit 1
  fi
}

install_deps() {
  export DEBIAN_FRONTEND=noninteractive
  apt-get update -y
  apt-get install -y --no-install-recommends \
    ca-certificates curl git docker.io docker-compose-plugin
  systemctl enable --now docker || true
}

create_user() {
  if ! id -u "${WAZUH_USER}" >/dev/null 2>&1; then
    useradd -m -s /bin/bash "${WAZUH_USER}"
  fi
  echo "${WAZUH_USER}:${WAZUH_PASS}" | chpasswd
  usermod -aG docker "${WAZUH_USER}" || true
}

as_wazuh() {
  sudo -H -u "${WAZUH_USER}" bash -lc "$*"
}

write_config_yml() {
  as_wazuh "mkdir -p '${SINGLE_NODE_DIR}/config'"
  as_wazuh "cat > '${SINGLE_NODE_DIR}/config/config.yml' <<'EOF'
nodes:
  indexer:
    - name: wazuh.indexer
      ip: \"wazuh.indexer\"

  server:
    - name: wazuh.manager
      ip: \"wazuh.manager\"

  dashboard:
    - name: wazuh.dashboard
      ip: \"wazuh.dashboard\"
EOF"
}

clone_and_checkout() {
  as_wazuh "cd '${BASE_DIR}' && rm -rf '${REPO_DIR}'"
  as_wazuh "cd '${BASE_DIR}' && git clone https://github.com/wazuh/wazuh-docker.git"
  as_wazuh "cd '${REPO_DIR}' && git fetch --all --tags"
  as_wazuh "cd '${REPO_DIR}' && git checkout '${TAG}'"
}

patch_compose_api_url_port() {
  # Ensure WAZUH_API_URL includes :55000
  as_wazuh "cd '${SINGLE_NODE_DIR}' && \
    sed -i 's#^\\s*-\\s*WAZUH_API_URL=https://wazuh\\.manager\\s*\$#      - WAZUH_API_URL=https://wazuh.manager:55000#g' docker-compose.yml"
}

run_cert_tool() {
  as_wazuh "cd '${SINGLE_NODE_DIR}/config' && curl -fsSL '${CERT_TOOL_URL}' -o wazuh-certs-tool.sh"
  as_wazuh "cd '${SINGLE_NODE_DIR}/config' && chmod +x wazuh-certs-tool.sh"
  as_wazuh "cd '${SINGLE_NODE_DIR}/config' && bash ./wazuh-certs-tool.sh -A"
}

replace_cert_dirs_with_pems() {
  local src="${SINGLE_NODE_DIR}/config/wazuh-certificates"
  local dest1="${SINGLE_NODE_DIR}/config"
  local dest2="${SINGLE_NODE_DIR}/config/wazuh_indexer_ssl_certs"

  # Ensure dest2 exists
  as_wazuh "mkdir -p '${dest2}'"

  # Delete any directories that have .pem names in dest1 and dest2 (your earlier issue)
  as_wazuh "find '${dest1}' -maxdepth 1 -type d \\( -name '*.pem' -o -name '*.key' \\) -exec rm -rf {} + || true"
  as_wazuh "find '${dest2}' -maxdepth 1 -type d \\( -name '*.pem' -o -name '*.key' \\) -exec rm -rf {} + || true"

  # Copy PEM/KEY files from wazuh-certificates into both locations
  as_wazuh "find '${src}' -maxdepth 1 -type f \\( -name '*.pem' -o -name '*.key' \\) -print0 | xargs -0 -I{} cp -f {} '${dest1}/'"
  as_wazuh "find '${src}' -maxdepth 1 -type f \\( -name '*.pem' -o -name '*.key' \\) -print0 | xargs -0 -I{} cp -f {} '${dest2}/'"
}

fix_root_ca_manager_pem() {
  # Your requirement:
  # replace the root-ca-manager.pem DIRECTORY (if it exists) with a FILE copied from root-ca.pem
  as_wazuh "cd '${SINGLE_NODE_DIR}' && \
    rm -rf ./config/wazuh_indexer_ssl_certs/root-ca-manager.pem && \
    cp -f ./config/wazuh_indexer_ssl_certs/root-ca.pem ./config/wazuh_indexer_ssl_certs/root-ca-manager.pem && \
    chmod 0644 ./config/wazuh_indexer_ssl_certs/root-ca-manager.pem"
}

compose_up_and_restart_manager() {
  as_wazuh "cd '${SINGLE_NODE_DIR}' && docker compose down -v || true"
  as_wazuh "cd '${SINGLE_NODE_DIR}' && docker compose up -d"
  # Requirement #3
  as_wazuh "cd '${SINGLE_NODE_DIR}' && docker compose restart wazuh.manager"
}

main() {
  need_root
  install_deps
  create_user

  clone_and_checkout
  write_config_yml
  patch_compose_api_url_port

  run_cert_tool
  replace_cert_dirs_with_pems
  fix_root_ca_manager_pem

  compose_up_and_restart_manager

  echo "[+] Done. Run from: ${SINGLE_NODE_DIR}"
  echo "    Dashboard: https://<host-ip>/  (port 443 -> 5601)"
}

main "$@"

```


# Agents

## Ubuntu 24.04 WAZUH

```
wget https://packages.wazuh.com/4.x/apt/pool/main/w/wazuh-agent/wazuh-agent_4.14.1-1_amd64.deb && sudo WAZUH_MANAGER='localhost' WAZUH_AGENT_GROUP='default' WAZUH_AGENT_NAME='Wazuh-Ubuntu' dpkg -i ./wazuh-agent_4.14.1-1_amd64.deb
```

## Ubuntu 24.04 TRYTON

```
wget https://packages.wazuh.com/4.x/apt/pool/main/w/wazuh-agent/wazuh-agent_4.14.1-1_amd64.deb && sudo WAZUH_MANAGER='192.168.100.149' WAZUH_AGENT_GROUP='default' WAZUH_AGENT_NAME='Tryton-Ubuntu' dpkg -i ./wazuh-agent_4.14.1-1_amd64.deb
```

```ad-important
After running the commands for linux, run this:

sudo /var/ossec/bin/agent-auth -m 192.168.100.149 -A "Agent-Name"
sudo systemctl enable --now wazuh-agent
sudo systemctl restart wazuh-agent
```

---



## Windows 11 Server (2025)

```
Invoke-WebRequest -Uri https://packages.wazuh.com/4.x/windows/wazuh-agent-4.14.1-1.msi -OutFile $env:tmp\wazuh-agent; msiexec.exe /i $env:tmp\wazuh-agent /q WAZUH_MANAGER='192.168.100.149' WAZUH_AGENT_GROUP='default' WAZUH_AGENT_NAME='WSSRV-2025'
```


## Windows 10 Pro User Endpoint

```
Invoke-WebRequest -Uri https://packages.wazuh.com/4.x/windows/wazuh-agent-4.14.1-1.msi -OutFile $env:tmp\wazuh-agent; msiexec.exe /i $env:tmp\wazuh-agent /q WAZUH_MANAGER='192.168.100.149' WAZUH_AGENT_GROUP='default' WAZUH_AGENT_NAME='W10-Pro'
```


```ad-important
After running the commands for windows, run this:

cd "C:\Program Files (x86)\ossec-agent\"
.\agent-auth.exe -m 192.168.100.149 -A <Name_Of_Agent>
Restart-Service WazuhSvc
```

```ad-note
Firewall had to allow outbound for 1515/1514
```