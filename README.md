# vaultwarden-podman
## preparation
```
sudo pacman -S podman
```
## config
```
mkdir -p vaultwarden/ssl vaultwarden/db-data vaultwarden/bitwarden 
```
```
cd vaultwarden
```
```
openssl req -x509 -newkey rsa:4096 -keyout vaultwarden/ssl/key.pem -out vaultwarden/ssl/cert.pem -sha256 -days 3650 -nodes
```
```
podman run -d \
  --name vaultwarden-db \
  -p 5432:5432 \
  --restart always \
  -e POSTGRES_DB=vaultwarden \
  -e POSTGRES_USER=vaultwarden \
  -e POSTGRES_PASSWORD=password \
  -v ./db-data:/var/lib/postgresql/data:Z \
  postgres:15-alpine
```
```
podman run -d \
  --name vaultwarden \
  -p 9445:80 \
  --restart unless-stopped \
  -v ./bitwarden:/data:rw,Z \
  -v ./ssl:/ssl:ro,Z \
  -e ROCKET_TLS='{certs="/ssl/cert.pem",key="/ssl/key.pem"}' \
  -e DOMAIN=https://ip_address \
  -e WEBSOCKET_ENABLED=true \
  -e SIGNUPS_ALLOWED=true \
  -e DATABASE_URL=postgresql://vaultwarden:password@10.88.0.1:5432/vaultwarden \
  vaultwarden/server:latest
```
## usage
access on browser
```
https://ip_address:9445
```
