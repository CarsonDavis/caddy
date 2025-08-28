# Caddy HTTPS Reverse Proxy

Simple Docker setup to add HTTPS to any web application using Caddy's automatic Let's Encrypt certificates.

## Quick Start

1. **Clone this repo** to your server
2. **Edit the Caddyfile** - replace `your-domain.com` with your domain and `:3000` with your app's port
3. **Run**: `docker compose up -d`

That's it! Caddy will automatically obtain and renew SSL certificates.

## Prerequisites

- Docker and Docker Compose installed
- Domain pointing to your server's IP address
- Ports 80 and 443 open in your firewall
- Your application running on localhost (any port)

## Configuration

Edit the `Caddyfile` to match your setup:

```
your-domain.com {
    reverse_proxy host.docker.internal:3000
}
```

### Multiple domains/apps

```
app1.example.com {
    reverse_proxy host.docker.internal:3000
}

app2.example.com {
    reverse_proxy host.docker.internal:8080
}
```

### Custom configurations

```
your-domain.com {
    reverse_proxy host.docker.internal:3000 {
        header_up Host {upstream_hostport}
        header_up X-Real-IP {remote_host}
        header_up X-Forwarded-For {remote_host}
        header_up X-Forwarded-Proto {scheme}
    }
}
```

## Commands

```bash
# Start
docker compose up -d

# View logs
docker logs -f caddy

# Stop
docker compose down

# Restart (after config changes)
docker compose restart
```

## Verify Setup

```bash
# Check certificate acquisition
docker logs caddy

# Test HTTPS
curl -I https://your-domain.com
```

## Troubleshooting

- **Certificate issues**: Ensure domain points to server and ports 80/443 are accessible
- **502 errors**: Check if your app is running on the specified port
- **Permission issues**: Ensure Docker has access to bind ports 80/443

## How it works

- Caddy runs in a Docker container with ports 80/443 exposed
- `host.docker.internal` allows the container to reach your host applications
- Let's Encrypt certificates are automatically obtained and renewed
- All data persists in Docker volumes