# homelab-docker
Docker compose files to setup homelab

# Goal
- Setting up services running on a local or virtual server, using docker compose and enabling public and/or remote access for chosen services.
- All services shall be secured using LetsEncrypt certificates.
- No open port on the server if possible.
- Use own OIDC provider for authorization and authentication, use SSO whenever possible.
- Secure services that do not natively support OIDC as well.

# Prerequisites
- Docker host
- cloudflare account (for now)
- Own domain name

# Different levels
1. Using cloudflared tunnels for remote access, only local access for more private services.
2. Using cloudflared tunnels for remote access, tailscale for access to more private services.
3. Using cloudflared tunnels for remote access, netbird for access to more private services. (to be evaluated)
4. Using netbird proxy for remote access, netbird for access to more private services. (to be evaluated)

# Tech Stack
- PocketID
- tinyauth
- traefik
- cloudflared (optional)
- tailscale (optional)
- netbird (optional)