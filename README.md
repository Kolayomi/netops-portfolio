# NetOps Portfolio (EVPN lab + Ansible safe-change)

This repo gives you a runnable FRR leaf–spine lab (via Containerlab/Docker) and a vendor-agnostic
Ansible "safe change" role (pre-check → backup → change → post-check → rollback).

## Quick start
1) Install Docker + Containerlab (Ubuntu):
   ```bash
   sudo apt update && sudo apt install -y ca-certificates curl gnupg lsb-release
   # Docker
   sudo install -m 0755 -d /etc/apt/keyrings
   curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
   echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
   sudo apt update && sudo apt install -y docker-ce docker-ce-cli containerd.io
   sudo usermod -aG docker $USER  # log out/in after this or: newgrp docker
   # Containerlab
   bash -c "$(curl -sL https://get.containerlab.dev)"
   clab version && docker info
   ```

2) Deploy the lab:
   ```bash
   cd containerlab
   sudo containerlab deploy -t clab-evpn-lab.yml
   # check BGP/OSPF
   docker exec -it clab-evpn-demo-sp1 vtysh -c "show bgp summary"
   docker exec -it clab-evpn-demo-leaf1 vtysh -c "show ip ospf neighbor"
   ```

3) Run the Ansible safe change:
   ```bash
   sudo apt -y install ansible
   cd ../ansible
   ansible-galaxy install -r requirements.yml
   ansible-playbook -i inventories/lab.yml playbooks/safe_change.yml -vv
   ```

4) Tear down:
   ```bash
   cd ../containerlab
   sudo containerlab destroy -t clab-evpn-lab.yml -c
   ```

## Notes
- Inventory/role is wired to the lab containers by name (`clab-evpn-demo-<node>`).
- Swap FRR `docker exec ... vtysh` commands for `napalm_install_config` or vendor modules to target real gear.
- Add CI/CD and NetBox later for full NetDevOps workflow.
