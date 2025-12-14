# pve-homelab
Deploying a PVE cluster

# install pip environment
cd ansible
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt

# configure pve
ansible-playbook -i inventory/cluster.ini pve_cluster.yml --vault-password-file ~/ansible_secrets/vault
