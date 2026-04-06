# homelab-docker
Docker compose files to setup homelab

# Goal
- Setting up services running on a local or virtual server, using docker compose and enabling public and/or remote access for chosen services.
- All services shall be secured using LetsEncrypt certificates.
- No configuration files. Everything should be part of docker compose.
- No open port on the server if possible.
- Don't use docker socket directly, use a proxy for security reasons.
- Use own OIDC provider for authorization and authentication, use SSO whenever possible.
- Secure services that do not natively support OIDC as well.

# Prerequisites
- Docker host
- cloudflare account (for now)
- DNS with cloudflare (for DNS-01 challenge for LetsEncrypt certificates)
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
- whoami (example and template for other services)
- docker proxy
TODO: add links to the projects

# Common environment variables
To create a common stack, the following environment variables are used and have to be set in an `.env` file for each container. `docker compose config` will let you know if you missed one.


```bash
 NAME=whoami
 ROOT_DOMAIN=domain.com
 VIRTUAL_HOST=${NAME}.${ROOT_DOMAIN}
 WHITELIST=user@${ROOT_DOMAIN}
```

# Explanation of some of the used labels
- `"traefik.http.routers.${NAME}.tls.domains[1].main=${VIRTUAL_HOST}"` used for a certificate for this specific subdomain. This needs a DNS entry (CNAME, A, AAA) for the subdomain, a wildcard is not sufficient
- `"traefik.http.routers.${NAME}.tls.domains[0].main=${ROOT_DOMAIN}"` and `"traefik.http.routers.${NAME}.tls.domains[0].sans=*.${ROOT_DOMAIN}"` for usage of the wildcard certificate.
- `"traefik.docker.network=traefik"` for docker compose files using more than one network, this label tells traefik to use the correct network for the proxied container.
- `"traefik.http.services.$NAME.loadbalancer.server.port=${PORT}"` if more than one port is exposed, use this one for traefik.
- `"traefik.http.routers.${NAME}.middlewares=tinyauth"` secure this container with tinyauth. Should be used of the application does not OIDC itself.
- `"tinyauth.apps.${NAME}.oauth.whitelist=${WHITELIST}"` only allow users on the whitelist to access this container. Be aware that `${NAME}` must correspond to the subdomain.