# DevOps WordPress Platform (Proxmox + Ansible + Docker + Caddy)

A reproducible Infrastructure-as-Code showcase demonstrating how to build and evolve a WordPress platform using:

- Proxmox virtual machines
- Ansible provisioning
- Docker Compose application stacks
- Caddy reverse proxy with automatic HTTPS (Let’s Encrypt)
- WireGuard network forwarding

This project evolves in structured maturity levels.

---

## Level 1 – Single VM Architecture

Tag: `v1.0-level1`

A complete WordPress stack deployed on a single VM:

- Docker
- WordPress + MariaDB
- Caddy reverse proxy
- Basic host hardening (UFW, base packages)

Architecture:
Internet → VPS Public IP → WireGuard Tunnel → VM → Caddy → WordPress

How to deploy:
cd ansible
ansible-playbook -i inventory/production.ini playbooks/level1.yml

## Level 2 – Dedicated Proxy + Multiple Backends

Tag: v2.0-level2

A production-like split architecture:

    Proxy VM (Caddy entrypoint)

    Dedicated WordPress backend VMs

    Firewall segmentation (proxy → backend only)

    Per-site logging & PHP tuning via Ansible

Architecture:
Level 2 – Dedicated Proxy + Multiple Backends
Internet
   ↓
VPS Public IP
   ↓
WireGuard
   ↓
Proxy VM (Caddy)
   ↓
WordPress Backend VM(s)

How to deploy
cd ansible
ansible-playbook -i inventory/production.ini playbooks/level2.yml

### Roadmap – Level 3

Planned improvements:

Ansible Vault for secrets

Automated backups + restore validation

CI pipeline (ansible-lint, yamllint)

Monitoring stack (Prometheus/Grafana)

Terraform provisioning for Proxmox

Centralized logging

## Documentation

Detailed technical documentation is available in the /docs directory:

Architecture & Networking

Ansible structure & roles

Inventory & configuration model

Operations & maintenance

Security & hardening strategy

Roadmap evolution

## Objective

This project demonstrates:

Infrastructure evolution from simple to segmented architecture

Idempotent automation with Ansible

Network segmentation & security mindset

Platform-oriented DevOps thinking

It is designed as a technical showcase and learning platform.
