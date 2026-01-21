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
    image: tryton/tryton:${VERSION:-latest}
    environment:
      - DB_HOSTNAME=WIN11_IP
      - DB_PORT=5432
      - DB_NAME=${DB_NAME:-tryton}
      - DB_USER=${DB_USER:-tryton}
      - DB_PASSWORD=${DB_PASSWORD:-password}
    volumes:
      - "tryton-data:/var/lib/trytond/db"
    command:
      - /bin/bash
      - -c
      - |
        # wait for the Tryton app, then run cron against the same DB
        (until echo > /dev/tcp/server/8000; do sleep 0.5; done) 2>/dev/null
        trytond-cron -d "${DB_NAME:-tryton}"
    depends_on:
      - server

volumes:
  tryton-data:
```