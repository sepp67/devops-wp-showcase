## Project Evolution

- **Level 1 (v1.0-level1)**: Single VM deployment (Caddy + WordPress + MariaDB)
- **Level 2 (planned)**: Dedicated proxy VM + multiple WordPress backend VMs
- **Level 3 (planned)**: Vault, backups/restore, CI, monitoring, IaC (Terraform Proxmox)


DevOps WordPress Showcase – Level 1
Overview
This project demonstrates a reproducible DevOps deployment of:
    • Proxmox VM
    • Docker Engine
    • Caddy reverse proxy (automatic TLS)
    • WordPress + MariaDB
Deployment is fully automated using Ansible.

Architecture
                    Internet
                        |
                        |
                Public IP (VPS)
                        |
                    WireGuard
                        |
                    vm-prod
                        |
        ---------------------------------
        | Docker Engine                |
        |                               |
        |  Caddy (Reverse Proxy + TLS)  |
        |  WordPress                    |
        |  MariaDB                      |
        ---------------------------------
Networking model
    • YOURDOMAIN.tech points to the public IP of the VPS
    • The VPS forwards traffic (80/443) through WireGuard
    • vm-prod receives the traffic internally
    • Caddy automatically provisions Let's Encrypt certificates

Infrastructure
Component
Description
Hypervisor
Proxmox
Control node
vm-ansible
Target node
vm-prod
OS
Debian 12
Reverse proxy
Caddy v2
Application
WordPress
Database
MariaDB 11

Deployment
From vm-ansible:
cd ansible
ansible-playbook playbooks/level1.yml
This playbook:
    1. Configures base system (timezone, firewall)
    2. Installs Docker
    3. Deploys Docker Compose stack
    4. Generates Caddy configuration
    5. Starts WordPress in HTTPS

Domain configuration
DNS configuration:
YOURDOMAIN.tech → Public IP (VPS)
Ports required:
    • 80/tcp
    • 443/tcp
Firewall rules on vm-prod allow:
    • SSH (22)
    • HTTP (80)
    • HTTPS (443)

Services
Caddy
    • Automatic HTTPS (Let's Encrypt)
    • HTTP → HTTPS redirection
    • Gzip enabled
WordPress
    • Running in container
    • Persistent volume for:
        ◦ /var/www/html
MariaDB
    • Persistent volume
    • Credentials managed via environment file

Persistent data
Docker volumes:
    • wp_data
    • db_data
    • caddy_data
    • caddy_config
To inspect:
docker volume ls

Operational commands

Check containers
docker ps

View logs
docker logs caddy
docker logs wordpress
docker logs wp_db

Restart stack
cd /opt/devops-wp-showcase
docker compose down
docker compose up -d

Full reset (destroy data)
docker compose down -v

## Known Limitations

While this Level 1 setup demonstrates a reproducible and secure containerized deployment, it intentionally remains simple.

Current limitations:

- Single VM architecture (proxy + application on same host)
- No infrastructure-as-code for VM provisioning (manual Proxmox creation)
- Secrets stored in Ansible variables (no Vault yet)
- No centralized logging or monitoring
- No automated backups
- No CI/CD validation pipeline
- Docker Compose used instead of orchestrator (e.g. Swarm/K8s)

This design is suitable for a small production workload or showcase environment, but not yet optimized for high availability or horizontal scalability.

## Future Improvements

Planned evolution of this project:

### Level 2
- Dedicated reverse proxy VM
- Multiple WordPress backend nodes
- Automated site provisioning via Ansible
- Internal network isolation between proxy and backends

### Level 3
- Secrets management using Ansible Vault
- Automated backups with restore testing
- CI pipeline (ansible-lint, yamllint, docker validation)
- Monitoring stack (Prometheus / Uptime Kuma)
- Infrastructure provisioning via Terraform (Proxmox provider)
- Log centralization
- Staging vs Production inventory separation

The goal is to progressively move from a reproducible deployment to a production-grade DevOps architecture.

