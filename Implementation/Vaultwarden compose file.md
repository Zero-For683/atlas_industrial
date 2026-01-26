```
services:
  vaultwarden:
    image: vaultwarden/server:latest
    container_name: vaultwarden
    restart: unless-stopped
    environment:
      WEBSOCKET_ENABLED: "true"
      SIGNUPS_ALLOWED: "true"
      ADMIN_TOKEN: "+t7rPrBhoozjmh/tLycwO5J0uLWguRhfzT9tWGuTocco9j2KXd4rpf+pCDsdU5ZBd"
      DOMAIN: "https://vaultwarden.corp.atlas.com"
    volumes:
      - ./data:/data
    expose:
      - "80"

  caddy:
    image: caddy:latest
    container_name: caddy
    restart: unless-stopped
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./Caddyfile:/etc/caddy/Caddyfile:ro
      - caddy_data:/data
      - caddy_config:/config
    depends_on:
      - vaultwarden

volumes:
  caddy_data:
  caddy_config:
```

```Caddyfile
vaultwarden.corp.atlas.com {
  tls internal
  reverse_proxy vaultwarden:80
}

wazuh-dashboard.corp.atlas.com {
  tls internal
  reverse_proxy https://192.168.100.149:8443 {
    transport http {
      tls_insecure_skip_verify
    }
  }
}

tryton.corp.atlas.com {
  tls internal
  reverse_proxy 192.168.100.151:8000
}
```


```ad-note
The use of vaultwarden requires HTTPS (for obvious reasons I hope). To implement such a thing, we can use `Caddy` to force HTTPS. It works well for us, because we can enforce HTTPS across all web applications when we implement DNS as well. 
```

