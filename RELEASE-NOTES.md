# Prairie v0.2.0 — Green Horizon
**Release Date:** 2025-12-07  
**This is a major upgrade. **

Prairie now delivers **production-ready Rancher** on both K3s and RKE2 — complete with **automatic Let's Encrypt TLS** and strict **version pinning** for stability. Just run the playbook and enjoy a secure UI with a valid certificate.

---

### Highlights
- Deploy Rancher on **K3s or RKE2** (configurable via `prairie_kubernetes_distribution`)
- **Browser-trusted HTTPS** using Let’s Encrypt
- **Pinned versioning** across the entire stack (Rancher, Helm, cert-manager, k3s/RKE2)
- Hardened cert-manager installation to prevent webhook race conditions
- Ingress intelligence based on chosen distribution

---

### 🛠 Updated Configuration Options
Example host/group vars for fully trusted TLS:

```yaml
prairie_kubernetes_distribution: rke2
prairie_tls_mode: "letsencrypt"

# The following are in the vault
vault_hostname: rancher.dev-ide.com
vault_lets_encrypt_email: "you@example.com"
```

# Prairie Release Notes – v0.1.2  
**Release Date:** 2025-11-22

### Summary

This release fixes a regression in `v0.1.1` where fresh installs would bring K3s and Rancher up, but fail with `502 Bad Gateway` at the Rancher endpoint. Root cause ended up being a combination of:

- cert-manager install path changes
- Rancher ingress TLS configuration
- host firewall rules interfering with Traefik / LoadBalancer traffic

`v0.1.2` gets us back to a clean, reproducible “from bare OS to working Rancher UI” path.

### What changed

- Repaired cert-manager installation:
  - Apply CRDs via `cert-manager.crds.yaml`
  - Install `jetstack/cert-manager` via Helm
  - Wait for CRDs + pods before touching Rancher

- Simplified Rancher TLS:
  - Use `ingress.tls.source=rancher` so the chart manages the `tls-rancher-ingress` secret directly
  - Ingress is now correctly wired to the Rancher service on port 80 with working HTTPS termination

- Disabled host firewall management:
  - Removed automatic `firewalld` install/config from the base role
  - Added `prairie_manage_firewall` flag (default: `false`)
  - Avoids host-level firewall rules breaking Traefik / K3s `LoadBalancer` behavior

### Notes

- If you were relying on Prairie to manage host firewall rules, that behavior is currently disabled. Use your existing perimeter firewall / security groups, or manage host firewall state manually until a more K3s-aware firewall role is added back.
- If you see `Bad Gateway` again, the quickest sanity checks are:
  - `kubectl get pods -A`
  - `kubectl get ingress -A`
  - `kubectl port-forward -n cattle-system svc/rancher 8443:443` and hit `https://localhost:8443` to confirm Rancher itself is healthy.

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
