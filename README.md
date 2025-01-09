# Setting-Up-Nginx-Proxy-Manager-on-Docker-with-macvlan
This guide outlines setting up an Nginx Proxy Manager using Docker, specifically leveraging `macvlan` networking. This configuration is particularly suitable for devices with dual Ethernet interfaces.

##Prerequisites
- Synology NAS or equivalent with at least two Ethernet interfaces.
- Docker and Docker Compose installed.
- An account on DuckDNS.org

##Key Highlights of this Setup
- macvlan Networking: Allows the Nginx Proxy Manager container to use its own IP address on the local network (e.g., 192.168.0.254), avoiding port conflicts with the host.
- Let's Encrypt Certificates: Proper configuration to avoid common errors when generating certificates for yourdomain.duckdns.org and *.yourdomain.duckdns.org.
