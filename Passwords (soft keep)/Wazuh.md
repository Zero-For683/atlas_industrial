```
atlas-user password: e8xB^FH&xnR5vTrHc69C

atlas-admin password: pv11dBqNY$phD3x%LUG#
```




```
Admin log in: UN Admin PW ((QZed?U5;7B/rwf5\b~)I@R"l0%iL^0c<jano:Z*rg;#ofD/}

Logstash : Pw - Y&0}2Wh~,3hE*'4t}J[\

snapshotrestore: Pw -cE0w83{z=`K/qb=yk9Qo

kibanaserver- no password need,  wazuh hardens this already

kibanaro -f=FR~6)DUQPsyiGQ*N;,M572%

readall -W5!yiFCLaAkkZKNv#J^G89:y<07R6z
```


```
WAZUH API USER:
wazuh-wui:5pRDSy2CBBK9gtPHmJK3B3xmAXDDg*+B
```

```
WAZUH DASHBOARD USER:
admin:c5GFHDSyPXfS%WB%2Fj^
```

```
WAZUH ADMIN TOKEN:
TOKEN:"+t7rPrBhoozjmh/tLycwO5J0uLWguRhfzT9tWGuTocco9j2KXd4rpf+pCDsdU5ZBd"
```


---

Run: `docker compose down`

Then edit the `docker-compose.yml` file, and fill in the password for the user in question. Then run the following two commands (REMOVE THE `>` symbols)

```

/home/wazuh/wazuh-docker/single-node# docker run --rm -ti wazuh/wazuh-indexer:4.12.0 \
bash /usr/share/wazuh-indexer/plugins/opensearch-security/tools/hash.sh

docker exec -it single-node-wazuh.indexer-1 bash -lc '
export INSTALLATION_DIR=/usr/share/wazuh-indexer;
export CACERT=$INSTALLATION_DIR/config/certs/root-ca.pem;
export CERT=$INSTALLATION_DIR/config/certs/admin.pem;
export KEY=$INSTALLATION_DIR/config/certs/admin-key.pem;
export JAVA_HOME=/usr/share/wazuh-indexer/jdk;
bash $INSTALLATION_DIR/plugins/opensearch-security/tools/securityadmin.sh \
  -cd $INSTALLATION_DIR/config/opensearch-security/ \
  -nhnv \
  -cacert $CACERT \
  -cert $CERT \
  -key $KEY \
  -p 9200 \
  -icl
'
```
