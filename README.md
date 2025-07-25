
# Pi-hole + Caddy Proxy Docker Setup

This repository contains a Docker Compose configuration to run Pi-hole with a Caddy reverse proxy that handles HTTPS using Let's Encrypt via Cloudflare DNS challenge.

## Prerequisites

- Docker and Docker Compose installed on your host system
- Cloudflare API token with DNS edit permissions for your domain
- A registered domain managed by Cloudflare
- Basic knowledge of Docker and Docker Compose

## Setup

### 1. Create Docker volumes

To persist Caddy data and configuration, create the necessary Docker volumes manually:

```bash
docker volume create caddy_data
docker volume create caddy_config
```

### 2. Prepare your `.env` file

Create a `.env` file in the project root with the following variables (replace dummy values with your real credentials):

```
CLOUDFLARE_API_TOKEN=your_cloudflare_api_token_here
EMAIL_ADDRESS=your_email@example.com
PIHOLE_WEB_PASSWORD=your_secure_password
TIMEZONE=Europe/London
```

> **Important:** This file contains sensitive credentials. It is included in `.gitignore` to avoid accidental commits.

### 3. Configure Caddy volumes for staging first

To prevent hitting Let's Encrypt rate limits or accidentally locking your Cloudflare account during initial setup, it is highly recommended to test using the staging environment first.

In your `docker-compose.yml` file, use the staging Caddyfile volume like so:

```yaml
volumes:
  - ./Caddyfile-staging:/etc/caddy/Caddyfile
  # Once you have verified your setup works, comment the staging line above
  # and uncomment the production Caddyfile line below to get real certificates
  # - ./Caddyfile:/etc/caddy/Caddyfile
  - caddy_data:/data
  - caddy_config:/config
```

This way, Caddy will request test certificates that don't count towards rate limits.

### 4. Run the stack

Start the containers with:

```bash
docker-compose up -d
```

This will launch Pi-hole and Caddy. Pi-hole will listen on DNS ports 53 TCP/UDP, and Caddy will handle HTTPS traffic on port 443.

## File Structure

- `docker-compose.yml` - Docker Compose config for Pi-hole and Caddy
- `.env` - Environment variables (excluded from Git)
- `Caddyfile` - Production Caddy configuration for your domain
- `Caddyfile-staging` - Staging Caddy configuration for testing
- `etc-pihole/` - Persistent Pi-hole config volume
- `etc-dnsmasq.d/` - Persistent Pi-hole DNS config volume

## License

This project is licensed under the MIT License.

## .gitignore

Make sure your `.gitignore` contains:

```
.env
```

to keep your environment variables secure.

---
