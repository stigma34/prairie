# Prairie Release Notes – v0.1.1  
**Release Date:** 2025-11-22

## Overview
Prairie v0.1.1 is a structural release focused on internal cleanup, multi-distro compatibility, and preparation for future features (especially TLS and multi-node clustering).  
There are **no breaking changes** and no required actions for existing single-node users.

## Key Improvements

### Modular Role Architecture
Prairie has been reorganized into dedicated Ansible roles:
- `base` — OS prep, package setup, firewall behavior, swap, br_netfilter.
- `k3s` — future home of cluster bootstrapping logic.
- `rancher` — cert-manager + Rancher deployment, now cleanly separated.
- `tls` — foundational work for ACME/TLS automation (Let’s Encrypt + self-signed).

This layout dramatically improves readability, maintainability, and future expansion.

### Multi-Distro Support
Prairie now supports **RedHat-family** and **Debian-family** systems:
- OS-aware package updates (`dnf` vs `apt`)
- Safe firewall handling (firewalld on RH; ufw installed but not auto-enabled on Debian)
- Universal `/swapfile` handling
- Universal `br_netfilter` detection and loading

### Helm Stability Improvements
The previously attempted `kubernetes.core.helm` module caused:
- `--all` flag incompatibilities
- `metadata.managedFields` errors
- CRD ownership/import conflicts

It has been rolled back in favor of **direct Helm CLI usage**, restoring predictable,
working Rancher deployments.

### TLS On-Ramp (Not Yet Enabled)
Prairie now includes a `tls` role that provides:
- Let’s Encrypt HTTP-01 ClusterIssuer template
- Self-signed ClusterIssuer template
- Rancher ingress Certificate manifest
- Configurable ACME email, staging/prod endpoints, and secret names

TLS is **disabled by default** and not yet fully integrated into the deployment flow.

## Upgrade Notes
- No change required for existing inventories.
- Users may begin experimenting with TLS by setting `tls_enabled: true` and customizing variables.
- Helm behavior should now be more reliable across all supported distros.

## Known Limitations
- TLS is not yet automatic end-to-end.
- Multi-node cluster support is not yet implemented.
- Only RedHat and Debian families are supported at this stage.

---

Thank you for using Prairie!
