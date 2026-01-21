```
services:
  server:
    image: tryton/tryton:latest
    environment:
      - DB_HOSTNAME=192.168.100.148
      - DB_PORT=5432
      - DB_NAME=trytondb
      - DB_USER=tryton
      - DB_PASSWORD=CH@ngeME_123
      - PASSWORD=CH@ngeME_123      # Tryton admin password you want
      - EMAIL=admin@mail.com
    ports:
      - "8000:8000"
    command:
      - /bin/bash
      - -lc
      - |
        set -e

        # Wait for Postgres
        until echo > /dev/tcp/$${DB_HOSTNAME}/$${DB_PORT}; do sleep 0.5; done

        # Use libpq env vars (no URI parsing issues with '@')
        export PGHOST="$${DB_HOSTNAME}"
        export PGPORT="$${DB_PORT}"
        export PGUSER="$${DB_USER}"
        export PGPASSWORD="$${DB_PASSWORD}"

        # Initialize Tryton DB only if not initialized yet
        if ! psql -d "$${DB_NAME}" -tAc "select 1 from pg_class where relname='ir_module' limit 1" | grep -q 1; then
          echo "$${PASSWORD}" > /tmp/.passwd
          TRYTONPASSFILE=/tmp/.passwd /entrypoint.sh trytond-admin -d "$${DB_NAME}" --all --email "$${EMAIL}" -vv
        else
          echo "Tryton DB already initialized; skipping trytond-admin"
        fi

        # Start app
        if command -v uwsgi &>/dev/null; then
          uwsgi --ini /etc/uwsgi.conf
        else
          gunicorn --config=/etc/gunicorn.conf.py
        fi

  cron:
    image: tryton/tryton:latest
    environment:
      - DB_HOSTNAME=192.168.100.148
      - DB_PORT=5432
      - DB_NAME=trytondb
      - DB_USER=tryton
      - DB_PASSWORD=CH@ngeME_123
    command:
      - /bin/bash
      - -lc
      - |
        until echo > /dev/tcp/$${DB_HOSTNAME}/$${DB_PORT}; do sleep 0.5; done
        export PGHOST="$${DB_HOSTNAME}"
        export PGPORT="$${DB_PORT}"
        export PGUSER="$${DB_USER}"
        export PGPASSWORD="$${DB_PASSWORD}"
        trytond-cron -d "$${DB_NAME}"
    depends_on:
      - server
```
# Steps to install (Documentation)

The default port for PostgreSQL is set to 5432, so It was  changed it to that.

The firewall on windows 11 server was being stubborn. Its net connection was set to public, so the private rules weren't working stopping a stable connection. I set the Net-Connection in powershell to private and added an inbound rule for SQL on port 5432

Secondly, there was some trouble connecting the Tryton app to the sql, it was complaining about the `pg_hba.conf` file not having any allow-list items for the ip address 192.168.100.151 (tryton ubuntu), so I had to add this line to the bottom of it:


`host    all    tryton    192.168.100.151/32    scram-sha-256`

Per post's documentation - The system view `pg_hba_file_rules` can be helpful for pre-testing changes to the `pg_hba.conf file.`

Additionally , the database wasn't initializing from the docker compose. This occurred  because I was trying to connect to the database `trytondb` and not `tryton`. So I had to initialize both and set default passwords for both.

Lastly, I  ran `docker compose down -v` and `docker compose up -d`. following I accessed the web portal. Default creds for the `trytondb` database is `admin:CH@ngeME_123`.



