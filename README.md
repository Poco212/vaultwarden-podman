# vaultwarden-podman
## preparation
```
sudo pacman -S podman
```
## config
```
mkdir vaultwarden
```
```
mkdir vaultwarden/ssl
```
```
openssl req -x509 -newkey rsa:4096 -keyout vaultwarden/ssl/key.pem -out vaultwarden/ssl/cert.pem -sha256 -days 3650 -nodes
```
```
nvim vaultwarden/docker-compose.yml
```
add value
```
services:
  vaultwarden:
    image: vaultwarden/server:latest
    container_name: vaultwarden
    restart: unless-stopped
    ports:
      - 9445:80
    volumes:
      - ./bitwarden:/data:rw
      - ./ssl:/ssl:ro
    environment:
      - ROCKET_TLS={certs="/ssl/cert.pem",key="/ssl/key.pem"}
      - DOMAIN=https://ip_address  
      - WEBSOCKET_ENABLED=true
      - SIGNUPS_ALLOWED=true
      - DATABASE_URL=postgresql://vaultwarden:password@db:5432/vaultwarden
    depends_on:
      - db

  db:
    image: postgres
    container_name: vaultwarden-db
    restart: always
    environment:
      - POSTGRES_DB=vaultwarden
      - POSTGRES_USER=vaultwarden
      - POSTGRES_PASSWORD=password
    volumes:
      - ./db-data:/var/lib/postgresql/data

```
## usage
```
podman compose -f vaultwarden/docker-compose.yml up -d
```
access on browser
```
https://ip_address:9445
```
