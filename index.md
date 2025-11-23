<link rel="stylesheet" href="prairie.css">

# Prairie

<!-- Prairie / Tactical Rancher Badges -->

<p align="left">

  <!-- Ansible -->
  <img src="https://img.shields.io/badge/Ansible-2.17+-30ba78?style=for-the-badge&logo=ansible&logoColor=white" />

  <!-- Rancher -->
  <img src="https://img.shields.io/badge/Rancher-2.9+-2453ff?style=for-the-badge&logo=rancher&logoColor=white" />

  <!-- k3s -->
  <img src="https://img.shields.io/badge/k3s-Latest-2453ff?style=for-the-badge&logo=kubernetes&logoColor=white" />

  <!-- Helm -->
  <img src="https://img.shields.io/badge/Helm-3+-192072?style=for-the-badge&logo=helm&logoColor=white" />

  <!-- Rocky Linux -->
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

Prairie is a lightweight Ansible-driven deployer for standing up a fully functional Rancher environment on Rocky Linux 10.

It’s designed to take a clean Rocky 10 box (cloud image or bare-metal/VM), harden it just enough, install k3s, deploy Rancher via Helm, and leave you with a working Rancher UI at a known URL — without you having to click through a single web installer screen.

Prairie grew out of a real need: repeatable Rancher installs that work consistently across **DigitalOcean Rocky 10** images and **minimal ISOs**, and keep secrets out of logs.

---

## Goals

Prairie is built to:

- Automate **end-to-end Rancher deployment** on Rocky 10 using Ansible.
- Handle annoying platform differences (like missing `br_netfilter` on some cloud images).
- Keep sensitive data out of logs using **Ansible Vault** and `no_log: true`.
- Be **clone → init → run** simple for future-you and other operators.

---

## High-Level Flow

1. **Bootstrap secrets & vault**
   - Run:

     ```bash
     ./prairie/tools/ansible_init.sh
     ```

   - Script:
     - Creates `group_vars/cattle/vault.yml` with starter values.
     - Generates `.vault.key` (if missing) next to `ansible.cfg`.
     - Encrypts `vault.yml` with vault ID `default` via Ansible Vault.

2. **Prepare the host**
   - Ensure `br_netfilter` is present and loaded.
   - Configure it to load at boot for k3s/Rancher networking.
   - (Optional) Handle swap, basic hardening, etc.

3. **Install k3s**
   - Installs k3s using the recommended install script.
   - Ensures idempotency so reruns don’t break the cluster.

4. **Install Rancher via Helm**
   - Installs Helm (via the official Helm install script).
   - Adds required Helm repos.
   - Deploys Rancher into `cattle-system`.
   - Configures ingress/TLS based on your `vault_hostname`.

5. **Print the Rancher URL**
   - At the end of the Rancher role, Prairie prints:
     - `Rancher is available at: https://{{ "vault_hostname" }}/`

---

<div class="incoming-features-card">
  <h2>Incoming Features <span>(SitRep)</span></h2>

  <div class="features-grid">

    <!-- Completed -->
    <div class="feature-item completed">
      <div class="feature-header">
        <h3>Multi-Distro Compatibility Package</h3>
        <span class="completed-badge">Completed ✔ — v0.1.1</span>
      </div>
      <p>
        Prairie now deploys seamlessly across Rocky, RHEL, Fedora, Debian, and Ubuntu.
        Unified logic. Zero guesswork. One playbook, any AO.
      </p>
    </div>

    <div class="feature-item completed">
      <div class="feature-header">
        <h3>Role-Oriented Architecture Overhaul</h3>
        <span class="completed-badge">Completed ✔ — v0.1.1</span>
      </div>
      <p>
        Prairie is now compartmentalized into clear operational modules:
        Base Ops, K3s Provisioning, Rancher Deployment, and TLS.
        Cleaner structure. Easier maintenance. Better ops flow.
      </p>
    </div>

    <!-- In Progress / Future Work -->
    <div class="feature-item">
      <h3>Hardened TLS Integration</h3>
      <p>
        Cert-manager or certbot-driven LE certs. Zero warnings. Zero nonsense.
        Full green-lock readiness.
      </p>
    </div>

    <div class="feature-item">
      <h3>Cluster Force Multiplication</h3>
      <p>
        Promote Prairie from single-node Recon to full multi-node Operations.
        Controller + worker nodes deployed with precision.
      </p>
    </div>

    <div class="feature-item">
      <h3>Prairie Command Pod</h3>
      <p>
        An Ansible-loaded, Kubernetes-resident control unit.
        Fire off cluster expansions and updates straight from inside the wire —
        no external operator required.
      </p>
    </div>

    <div class="feature-item">
      <h3>Automated Node Enrollment</h3>
      <p>
        Drop a new server into the field and let Prairie pull tokens, push configs,
        and slot it into the cluster automatically.
      </p>
    </div>

    <div class="feature-item">
      <h3>Security Posture Enhancement</h3>
      <p>
        Unified firewall doctrine, SSH lockdown, sysctl hardening,
        and distro-specific quirks neutralized on contact.
      </p>
    </div>

  </div>
</div>

## Directory Layout

```text
prairie/
.
├── ansible.cfg
├── assets
│   └── prairie.png
├── CHANGELOG.md
├── collections
│   └── requirements.yml
├── deploy_rancher.yml
├── group_vars
│   └── cattle
│       └── vault.yml
├── inventory
│   └── inventory.ini
├── LICENSE
├── README.md
├── RELEASE-NOTES.md
├── roles
│   ├── base
│   │   ├── defaults
│   │   │   └── main.yml
│   │   └── tasks
│   │       ├── main.yml
│   │       └── swap.yml
│   ├── k3s
│   │   ├── defaults
│   │   │   └── main.yml
│   │   └── tasks
│   │       └── main.yml
│   ├── rancher
│   │   ├── defaults
│   │   │   └── main.yml
│   │   └── tasks
│   │       ├── cert_manager.yml
│   │       ├── helm.yml
│   │       ├── main.yml
│   │       ├── rancher_install.yml
│   │       └── verify.yml
│   └── tls
│       ├── defaults
│       │   └── main.yml
│       ├── tasks
│       │   └── main.yml
│       └── templates
│           ├── cluster-issuer-letsencrypt-http01.yaml.j2
│           ├── clusterissuer-selfsigned.yaml.j2
│           └── rancher-certificate.yaml.j2
└── tools
    └── ansible_init.sh

```