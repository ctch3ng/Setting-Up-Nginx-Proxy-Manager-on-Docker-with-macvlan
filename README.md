# Setting-Up-Nginx-Proxy-Manager-on-Docker-with-macvlan
This guide outlines setting up an Nginx Proxy Manager using Docker, specifically leveraging `macvlan` networking. This configuration is particularly suitable for devices with dual Ethernet interfaces.

## Prerequisites
- Synology NAS or equivalent with at least two Ethernet interfaces.
- Container Manager or Docker Compose installed.
- An account on DuckDNS.org

## Key Highlights of this Setup
- `macvlan` Networking: Allows the `Nginx Proxy Manager` container to use its IP address on the local network (e.g., 192.168.0.254), avoiding port conflicts with the host.
- `Let's Encrypt` Certificates: Proper configuration to avoid common errors when generating certificates for `yourdomain.duckdns.org` and `*.yourdomain.duckdns.org`.

## Step 1: Set Up the Docker Compose File with a `macvlan` Network

On the NAS, create the following folders under the default docker directory (e.g. `/docker`).
- /docker/npm
  - /docker/npm/data
  - /docker/npm/letsencrypt

`macvlan` requires a dedicated parent interface. In this example, we use `eth1` (instead of `eth0`) as the secondary network interface. Below is the full Docker Compose configuration to deploy the `Nginx Proxy Manager` container and the `macvlan` network.

```
services:
  app:
    image: 'jc21/nginx-proxy-manager:latest'
    restart: unless-stopped
    ports:
      - '80:80'  # Public HTTP Port
      - '443:443'  # Public HTTPS Port
      - '81:81'  # Admin Web Port
    environment:
      DISABLE_IPV6: 'true'  # Disable IPv6 if not supported
    networks:
      vlan:
        ipv4_address: 192.168.0.254
    volumes:
      - ./data:/data
      - ./letsencrypt:/etc/letsencrypt

networks:
  vlan:
    driver: macvlan
    driver_opts:
      parent: eth1
    ipam:
      config:
        - subnet: "192.168.0.0/24"
          ip_range: "192.168.0.1/32"
          gateway: "192.168.0.1"
```
## Notes:
- Replace `eth1` with the appropriate interface for your setup.
- Ensure that `192.168.0.254` is not already used on your network.

## Step 2: Configure DuckDNS
- Sign up for an account on [DuckDNS](http://www.duckdns.org).
- Create a subdomain (e.g., yourdomain.duckdns.org).
- Copy your `token`

## Step 3: Add `Let's Encrypt` Certificates to `Nginx Proxy Manager`
- Save and build the project in Container Manager or run `docker-compose up -d`.
- Access `Nginx Proxy Manager` via [http://192.168.0.254:81](http://192.168.0.254:81). The default username is `admin@example.com`, and the password is `changeme`. Once logged in, update your credentials. 

## Important:
- When configuring `Let's Encrypt` certificates, add `yourdomain.duckdns.org` and `*.yourdomain.duckdns.org` in <ins>two</ins> individual requests. <ins>Combining them often results in errors</ins>.
- Use the `DNS challenge` to validate ownership via DuckDNS.
- Paste your `token` into the `Credentials File Content` box like `dns_duckdns_token=token`.
- Set the timeout value to 120 seconds or more.

## Step 4: Adding Proxy Hosts

- When adding a proxy host, ensure you configure WebSocket support for applications such as `Unraid` and `OpenWeb UI`, which rely on WebSocket connections for features such as live updates, real-time monitoring, or interactive interfaces. This setting is essential for these applications to function correctly through the Nginx Proxy Manager.

- To enable Web Socket support:

  - Navigate to Proxy Hosts in the admin interface.

  - Edit or create a new proxy host for the desired application.

  - Under the `Details` tab, check the option to enable Web Socket support.
