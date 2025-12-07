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

## Automated Rancher deployer using Ansible [Work in Progress]

![Prairie Logo](assets/prairie.png)

![Build Status](https://img.shields.io/badge/build-stable-brightgreen?style=for-the-badge)

### Important information

**Note:** This has been tested on Rocky Linux 9/10 (minimal ISO and Digital Ocean image) and assumes you are working with a fresh install.

**Note:** This currently only installs a single node - this is meant for spinning up a quick development environment essentially.  Work will be done over time to build this out into a full-fledged deployer of multi-node, etc.

### Pre-requisites

At this time, there really are none, the ansible_init.sh will pull down everything it needs for you.  I am leaving this section though for just in case something comes up in future releases.

### Installation

1.  Clone the repository to your target server - the one you want to become the single-node Rancher cluster.
2.  Run the **ansible_init.sh** script.  This will pull down all the necessary packages, setup your virtualenv, inject a vault for you to use, and encrypt it for you.

    ```bash
    ./prairie/tools/ansible_init.sh
    ```
    
    **Note:** You may need to reboot after this completes if `kernel-modules-extra` had to be installed or a new kernel, as you'll want everything as fresh as possible.

3.  Source your virtualenv to start working on it.

    ```bash
    source ~/ansible-venv/bin/activate
    ```

4.  Modify your vault values to be correct for your environment (The ansible.cfg is already configured to let you edit the vault without specifying the decryption file).

    ```bash
    cd prairie && ansible-vault edit group_vars/cattle/vault.yml
    ```

5.  Go ahead and run the playbook at this point.

    ```bash
    ansible-playbook deploy_rancher.yml
    ```
