```
services:
  server:
    image: tryton/tryton:${VERSION:-latest}
    environment:
      # Tryton DB connection
      - DB_HOSTNAME=192.168.100.148
      - DB_PORT=5432
      - DB_NAME=trytondb
      - DB_USER=tryton
      - DB_PASSWORD=CH@ngeME_123

      # (optional) only used by your init command below
      - PASSWORD=admin
      - EMAIL="admin@mail.com"

    ports:
      - "8000:8000"
    command:
      - /bin/bash
      - -c
      - |
        # wait for Windows Postgres
        (until echo > /dev/tcp/$${DB_HOSTNAME}/$${DB_PORT}; do sleep 0.5; done) 2>/dev/null

        echo "${PASSWORD:-admin}" > /tmp/.passwd
        TRYTONPASSFILE=/tmp/.passwd /entrypoint.sh trytond-admin -d "${DB_NAME:-tryton}" --all --email "${EMAIL:-}" -vv

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
      - -c
      - |
        (until echo > /dev/tcp/$${DB_HOSTNAME}/$${DB_PORT}; do sleep 0.5; done) 2>/dev/null
        trytond-cron -d "$${DB_NAME}"
    depends_on:
      - server
```


# Steps to install (documentation)

The default port for PostgreSQL is 5432, so I changed it to that

The firewall on windows 11 server was being stubborn. Its net connection was set to public, so the private rules werent working. I set the Net-Connection in powershell to private and added an inbound rule for SQL on port 5432

Secondly, I was having some trouble connecting the tryton app to the sql, it was complaining about the `pg_hba.conf` file not having any allow-list items for the ip address 192.168.100.151 (tryton ubuntu), so I had to add this line to the bottom of it:

`host    all    tryton    192.168.100.151/32    scram-sha-256`

Then, the database wasnt initializing from the docker compose



```services:
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