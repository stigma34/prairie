<link rel="stylesheet" href="prairie.css">

# Prairie

<p class="version-tag">
  Current Release: <strong>v0.2.0</strong> &mdash; TLS + Longhorn Ready
</p>

<p class="version-details">
  <strong>v0.2.0 pinned component stack:</strong><br>
  Ansible: 8.7.0<br>
  Rancher: 2.12.3<br>
  k3s: v1.31.14+k3s1<br>
  RKE2: v1.31.14+rke2r1<br>
  Helm: v3.18.2<br>
  cert-manager: v1.19.1<br>
</p>

<p align="left">

  <!-- Ansible -->
  <img src="https://img.shields.io/badge/Ansible-8.7.0-30ba78?style=for-the-badge&logo=ansible&logoColor=white" />

  <!-- Rancher -->
  <img src="https://img.shields.io/badge/Rancher-2.12.3-2453ff?style=for-the-badge&logo=rancher&logoColor=white" />

  <!-- k3s -->
  <img src="https://img.shields.io/badge/k3s-v1.31.14%2Bk3s1-2453ff?style=for-the-badge&logo=kubernetes&logoColor=white" />

  <!-- rke2 -->
  <img src="https://img.shields.io/badge/RKE2-v1.31.14%2Brke2r1-2453ff?style=for-the-badge&logo=kubernetes&logoColor=white" />

  <!-- Helm -->
  <img src="https://img.shields.io/badge/Helm-v3.18.2-192072?style=for-the-badge&logo=helm&logoColor=white" />

  <!-- Cert-Manager -->
  <img src="https://img.shields.io/badge/cert--manager-v1.19.1-326ce5?style=for-the-badge&logo=letsencrypt&logoColor=white" />

  <!-- Let's Encrypt -->
  <img src="https://img.shields.io/badge/Let%27s%20Encrypt-Supported-003A70?style=for-the-badge&logo=letsencrypt&logoColor=white" />

  <!-- Rocky Linux 9 -->
  <img src="https://img.shields.io/badge/Rocky%20Linux-9-0c322c?style=for-the-badge&logo=rockylinux&logoColor=white" />

  <!-- Rocky Linux 10 -->
  <img src="https://img.shields.io/badge/Rocky%20Linux-10-0c322c?style=for-the-badge&logo=rockylinux&logoColor=white" />

  <!-- DigitalOcean -->
  <img src="https://img.shields.io/badge/DigitalOcean-Supported-006aff?style=for-the-badge&logo=digitalocean&logoColor=white" />

  <!-- License -->
  <img src="https://img.shields.io/badge/License-Apache--2.0-30ba78?style=for-the-badge&logo=apache&logoColor=white" />

</p>


<div class="tactical-button-wrapper">
  <a class="tactical-button" href="https://github.com/stigma34/prairie" target="_blank">
    Proceed to the Repository <span>// Begin Deployment</span>
  </a>
</div>

<br />

Prairie is a **tactical, Ansible-driven deployer** for bringing up a fully functional Rancher control plane on **Rocky Linux 10 and other major distros** with **green-lock TLS** and **optional Longhorn storage**.

It’s built for operators who want a **repeatable, low-friction Rancher install**: take a clean host (cloud image or bare metal/VM), run Prairie, and end up with:

- k3s installed and wired up
- Rancher online at a **known HTTPS URL**
- Longhorn (optionally) deployed for persistent storage
- Secrets locked down with Ansible Vault

No web installer click-throughs. No one-off bash scripts. One flow, any AO.

Prairie grew out of a real need: consistent Rancher deployments across **DigitalOcean Rocky 10** images and **minimal ISOs**, without leaking secrets into logs or having to remember “that one weird kernel module fix” every time.

---

## What Prairie Does

Prairie is built to:

- Automate **end-to-end Rancher deployment** on Rocky 10 (and friends) using Ansible.
- Smooth over distro quirks (e.g., missing `br_netfilter` or odd cloud image defaults).
- Deliver **TLS out of the box** using cert-manager with Let’s Encrypt HTTP-01 or a self-signed issuer.
- Provide **optional Longhorn integration** so you get a clean, opinionated storage story.
- Keep sensitive data out of logs via **Ansible Vault** and `no_log: true`.
- Stay **clone → init → run** simple for future-you and other operators.

---

## High-Level Flow

1. **Bootstrap secrets & vault**
   - Run:

     ```bash
     ./prairie/tools/ansible_init.sh
     ```

   - Script:
     - Creates `group_vars/cattle/vault.yml` with starter values (hostname, email, Rancher admin seed, etc.).
     - Generates `.vault.key` (if missing) next to `ansible.cfg`.
     - Encrypts `vault.yml` with vault ID `default` via Ansible Vault.

2. **Prepare the host**
   - Ensures `br_netfilter` and friends are present and loaded.
   - Configures sysctl so k3s/Rancher networking behaves.
   - Handles baseline prep and optional swap tweaks.

3. **Install k3s**
   - Installs k3s using the recommended upstream install script.
   - Keeps the run **idempotent** so reruns don’t wreck your cluster.

4. **Install Rancher via Helm**
   - Installs Helm using the official Helm script.
   - Adds the required Helm repos.
   - Deploys Rancher into `cattle-system`.
   - Binds ingress to your `vault_hostname` and preps for TLS.

5. **Wire Up TLS**
   - Deploys cert-manager.
   - Creates a **ClusterIssuer** (Let’s Encrypt HTTP-01 or self-signed).
   - Applies a Rancher `Certificate` resource and waits for the cert to go Ready.
   - Leaves you with a **green-lock Rancher UI** at your hostname.

6. **(Optional) Deploy Longhorn**
   - Installs Longhorn into the cluster.
   - Provides a clean, Kubernetes-native storage layer for workloads you’ll run under Rancher.

7. **Print the Rancher URL**
   - At the end of the Rancher/TLS flow, Prairie prints:

     ```text
     Rancher is available at: https://<your-vault-hostname>/
     ```

---

<div class="incoming-features-card">
  <h2>Capabilities & Roadmap <span>(SitRep)</span></h2>

  <div class="features-grid">

    <!-- Completed -->
    <div class="feature-item completed">
      <div class="feature-header">
        <h3>Multi-Distro Compatibility Package</h3>
        <span class="completed-badge">Completed ✔ — v0.1.1</span>
      </div>
      <p>
        Prairie deploys cleanly across Rocky, RHEL, Fedora, Debian, and Ubuntu.
        Unified logic. One playbook, multiple operating areas.
      </p>
    </div>

    <div class="feature-item completed">
      <div class="feature-header">
        <h3>Role-Oriented Architecture Overhaul</h3>
        <span class="completed-badge">Completed ✔ — v0.1.1</span>
      </div>
      <p>
        Base ops, k3s provisioning, Rancher deployment, and TLS split into
        focused roles. Easier to read, extend, and debug under pressure.
      </p>
    </div>

    <div class="feature-item completed">
      <div class="feature-header">
        <h3>Hardened TLS Integration</h3>
        <span class="completed-badge">Completed ✔ — v0.2.0</span>
      </div>
      <p>
        cert-manager-driven certificates with Let’s Encrypt or self-signed
        issuers. No more “Not Secure” banners — just a proper green lock on first contact.
      </p>
    </div>

    <div class="feature-item completed">
      <div class="feature-header">
        <h3>Longhorn Storage Pack</h3>
        <span class="completed-badge">Completed ✔ — v0.2.0</span>
      </div>
      <p>
        Optional Longhorn deployment so your Rancher-managed cluster has a
        battle-ready, replicated storage layer from day one.
      </p>
    </div>

    <!-- Roadmap -->
    <div class="feature-item">
      <h3>Cluster Force Multiplication</h3>
      <p>
        Promote Prairie from single-node Recon to full multi-node Operations:
        controller + worker node orchestration with repeatable inventory patterns.
      </p>
    </div>

    <div class="feature-item">
      <h3>Prairie Command Pod</h3>
      <p>
        An Ansible-loaded, Kubernetes-resident control unit. Trigger expansions,
        updates, and remediation jobs from inside the cluster — no jump host required.
      </p>
    </div>

    <div class="feature-item">
      <h3>Automated Node Enrollment</h3>
      <p>
        Drop a new server on the network and let Prairie pull tokens, push configs,
        and join it to k3s/Rancher automatically with minimal operator touch.
      </p>
    </div>

    <div class="feature-item">
      <h3>Security Posture Enhancement</h3>
      <p>
        Unified firewall doctrine, SSH lockdown, sysctl hardening, and distro-specific
        quirks neutralized on contact — with toggles for stricter profiles.
      </p>
    </div>

  </div>
</div>