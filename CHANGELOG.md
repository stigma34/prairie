# Changelog

All notable changes to **Prairie** will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),  
and this project aims to follow [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added
- TODO: Document upcoming changes here.

---

## [0.1.1] - 2025-11-22

### Added
- Introduced modular Ansible role structure:
  - `base` — OS preparation and prereqs.
  - `k3s` — future cluster bootstrap role (placeholder).
  - `rancher` — Rancher and cert-manager deployment tasks.
  - `tls` — initial scaffolding for future TLS/ACME automation.
- Multi-distro support added for both RedHat and Debian families:
  - OS-aware package installation (`dnf` vs `apt`).
  - Safe firewall handling (firewalld started only on RedHat; ufw installed but **not** auto-enabled on Debian).
  - Distro-neutral `/swapfile` creation with configurable size and swappiness.
  - Distro-neutral `br_netfilter` handling with module detection and boot-load persistence.

### Changed
- Refactored existing monolithic logic into clean role-based structure:
  - Moved k3s install, Helm repo setup, cert-manager deployment, Rancher install,
    and verification tasks into organized per-role files.
- Reverted usage of `kubernetes.core.helm` module due to instability and
  incompatibility with modern Helm flags; restored direct Helm CLI commands for predictable behavior.
- Cleaned up inventory and role execution order via the `site.yml` playbook.
- Improved variable naming and defaults to support future multi-node and TLS features.

### Notes
- TLS automation is partially implemented through the new `tls` role, but **disabled by default**.
  A full, functional ACME integration will arrive in a future release.
- No Rancher behavior changes yet for end users. This release focuses on internal stability,
  maintainability, and multi-distro readiness.
- Future releases will expand on the TLS role, multi-node support, and verification improvements.

---

## [0.1.0] - 2025-11-22

### Added
- Initial public release of **Prairie**.
- Ansible playbook `seed_rancher.yml` for end-to-end Rancher deployment on Rocky Linux 10.
- `rancher` role with tasks for:
  - k3s installation via upstream installer script.
  - Helm installation via official Helm script.
  - Rancher deployment into the `cattle-system` namespace.
  - Ingress/TLS wiring using `vault_hostname`.
- `ansible_init.sh` bootstrap script to:
  - Bootstrap Ansible installation.
  - Create `group_vars/cattle/vault.yml` with starter secrets.
  - Generate `.vault.key` and configure Ansible Vault usage.
- Support for both:
  - DigitalOcean Rocky Linux 10 images.
  - Minimal Rocky 10 ISOs / bare-metal installs.
- Documentation and Tactical Rancher dark theme for GitHub Pages.
