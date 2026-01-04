# Proxmox VE & Ceph Homelab Automation
This repository contains an Ansible playbook to automate the configuration of a
Proxmox Virtual Environment (PVE) cluster and the deployment of Ceph storage.
It is designed to streamline the setup of a homelab environment.<br />
It is also prepared your infrastructure to be managed by Terraform/OpenTofu.<br />

# Prerequisites
On your computer:

* Python 3 installed.
* Git installed.
* Network access to the Proxmox nodes.

On your Proxmox Servers:

* Proxmox VE installed on all nodes.
* On each nodes, at least 1 partition left dedicated for ceph
* SSH Access: You must be able to SSH into the nodes as root without a password
(using SSH keys).

# Getting Started

1. Clone the Repository

Clone this repository to your local machine:

    $ git clone https://github.com/richardpct/pve-homelab.git
    $ cd pve-homelab

2. Set Up the Environment

It is recommended to use a Python virtual environment to manage dependencies
and avoid conflicts with your system packages.<br />
Navigate to the ansible directory:

    $ cd ansible

Create and activate the virtual environment:

    $ python3 -m venv venv
    $ source venv/bin/activate

3. Install Dependencies

    $ pip install -r requirements.txt

# Configuration

1. Inventory Setup

The inventory file defines your Proxmox nodes. Edit inventory/cluster.ini to
match your infrastructure.

Example inventory/cluster.ini:
```
[pve_cluster]
pve-01 ansible_host=192.168.1.11
pve-02 ansible_host=192.168.1.22
pve-03 ansible_host=192.168.1.33
```

2. Secrets and Vault

This playbook uses Ansible Vault to secure sensitive information like
passwords.<br />
The command requires a vault password file.

- Create a directory for your secrets (outside the repo is safer):

    $ mkdir -p ~/ansible_secrets

- Create the vault password file containing your vault password:

    $ echo "MY_VAULT_PASSWORD" > ~/ansible_secrets/vault
    $ chmod 600 ~/ansible_secrets/vault

(Replace MY_VAULT_PASSWORD with your actual secure password)

- Adjust some variables

Generate the encrypted Proxmox VE password:

    $ ansible-vault encrypt_string 'MY_PVE_PASS' --name pvepass --vault-password-file ~/ansible_secrets/vault

Generate the encrypted Terraform provisionner user (This user will be used for
managing the VM using Terraform/OpenTofu):

    $ ansible-vault encrypt_string 'MY_TOFU_PASS' --name terraform_prov_user --vault-password-file ~/ansible_secrets/vault

Then edit inventory/group_vars/all.yml and replace the pvepass and
terraform_prov_user passwords. In this same file, also adjust the other
variables according your environment. One particular variable is ceph_part_start,
set it to the start of the partition dedicated to ceph.

# Usage

Once your inventory and environment are ready, you can run the main playbook.<br />
Run the Proxmox Cluster Playbook:

    $ ansible-playbook -i inventory/cluster.ini pve_cluster.yml --vault-password-file ~/ansible_secrets/vault

# Access to the gui
Point your browser to https://192.168.1.11:8006<br />
The user is root and the password is the one you created before
