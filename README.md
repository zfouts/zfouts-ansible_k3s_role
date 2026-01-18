# K3s Ansible Collection

[![CI](https://github.com/zfouts/ansible_k3s_role/actions/workflows/ci.yml/badge.svg)](https://github.com/zfouts/ansible_k3s_role/actions/workflows/ci.yml)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

Ansible collection for deploying and managing K3s Kubernetes clusters.

## Features

- ✅ Multi-node cluster deployment (server + agents)
- ✅ High Availability with embedded etcd
- ✅ Rolling upgrades with zero downtime
- ✅ Airgap installation support
- ✅ Private registry configuration
- ✅ Multiple CNI options (Flannel, Calico, Cilium)
- ✅ etcd snapshots and S3 backup
- ✅ Complete uninstall/reset capability

## Requirements

- Ansible 2.15+ (ansible-core)
- Target nodes: Debian, Ubuntu, RHEL/CentOS, Rocky, AlmaLinux, SUSE, or Arch Linux
- Passwordless SSH access to target nodes
- Root or sudo access on target nodes

## Installation

### From GitHub

```bash
ansible-galaxy collection install git+https://github.com/zfouts/ansible_k3s_role.git
```

### From requirements.yml

```yaml
collections:
  - name: https://github.com/zfouts/ansible_k3s_role.git
    type: git
    version: main
```

Then run:
```bash
ansible-galaxy collection install -r requirements.yml
```

### From Source

```bash
git clone https://github.com/zfouts/ansible_k3s_role.git
cd ansible_k3s_role
```

## Quick Start

### 1. Create Inventory

```bash
cp inventory-sample.yml inventory.yml
```

Edit `inventory.yml` with your node IPs:

```yaml
all:
  children:
    k3s_cluster:
      children:
        k3s_server:
          hosts:
            server1:
              ansible_host: 192.168.1.10
        k3s_agent:
          hosts:
            agent1:
              ansible_host: 192.168.1.20
            agent2:
              ansible_host: 192.168.1.21
  vars:
    ansible_user: root
    k3s_version: stable
```

### 2. Deploy Cluster

```bash
# If installed via ansible-galaxy
ansible-playbook k3s.cluster.site -i inventory.yml

# If running from source
ansible-playbook playbooks/site.yml -i inventory.yml
```

### 3. Access Cluster

```bash
# On the server node
kubectl get nodes
```

## Roles

| Role | Description |
|------|-------------|
| `k3s.cluster.common` | OS preparation, kernel modules, required packages |
| `k3s.cluster.k3s_server` | K3s server installation and configuration |
| `k3s.cluster.k3s_agent` | K3s agent installation and configuration |
| `k3s.cluster.k3s_reset` | Complete K3s removal and cleanup |

## Playbooks

| Playbook | Description |
|----------|-------------|
| `site.yml` | Full cluster deployment |
| `reset.yml` | Complete cluster removal |
| `upgrade.yml` | Rolling cluster upgrade |

## Configuration

### Network Configuration

```yaml
k3s_cluster_cidr: "10.42.0.0/16"  # Pod network
k3s_service_cidr: "10.43.0.0/16"  # Service network
k3s_cluster_dns: "10.43.0.10"     # CoreDNS IP
k3s_cni: flannel                   # flannel, calico, cilium, none
k3s_flannel_backend: vxlan         # vxlan, host-gw, wireguard-native
```

### Disable Components

```yaml
k3s_disable:
  - traefik       # Ingress controller
  - servicelb     # Load balancer
  - local-storage # Storage provisioner
  - metrics-server
```

### TLS SANs

```yaml
k3s_tls_san:
  - k3s.example.com
  - api.example.com
  - 10.0.0.100
```

### Node Labels and Taints

```yaml
k3s_node_labels:
  - "environment=production"
  - "node-type=worker"

k3s_node_taints:
  - "dedicated=special:NoSchedule"
```

### Private Registry

```yaml
k3s_registries:
  mirrors:
    docker.io:
      endpoint:
        - "https://mirror.example.com"
  configs:
    mirror.example.com:
      auth:
        username: myuser
        password: mypassword
```

### etcd Snapshots

```yaml
k3s_etcd_snapshot_schedule_cron: "0 */6 * * *"
k3s_etcd_snapshot_retention: 10
k3s_etcd_snapshot_dir: "/var/lib/rancher/k3s/server/db/snapshots"

# S3 backup
k3s_etcd_s3: true
k3s_etcd_s3_endpoint: "s3.amazonaws.com"
k3s_etcd_s3_bucket: "my-k3s-backups"
k3s_etcd_s3_region: "us-east-1"
```

### Airgap Installation

```yaml
k3s_airgap: true
k3s_airgap_dir: ./airgap-files  # Contains: k3s binary, k3s-airgap-images-*.tar.gz, install.sh
```

## High Availability

For HA, use 3 or 5 server nodes:

```yaml
k3s_server:
  hosts:
    server1:
      ansible_host: 192.168.1.10
    server2:
      ansible_host: 192.168.1.11
    server3:
      ansible_host: 192.168.1.12
```

The playbook automatically:
- Initializes embedded etcd on the first server
- Joins additional servers to the cluster
- Deploys servers one at a time for safety

## Upgrading

```bash
# Specify target version
ansible-playbook playbooks/upgrade.yml -i inventory.yml -e k3s_version=v1.29.0+k3s1
```

The upgrade playbook:
1. Cordons and drains each server
2. Upgrades servers one at a time
3. Upgrades all agents
4. Verifies cluster health

## Uninstalling

```bash
ansible-playbook playbooks/reset.yml -i inventory.yml
```

⚠️ **Warning**: This completely removes K3s and all cluster data!

## Architecture

```
k3s-ansible-collection/
├── galaxy.yml              # Collection metadata
├── playbooks/
│   ├── site.yml           # Full deployment
│   ├── reset.yml          # Uninstall
│   └── upgrade.yml        # Rolling upgrade
├── roles/
│   ├── common/            # OS preparation
│   │   ├── defaults/
│   │   └── tasks/
│   ├── k3s_server/        # Server role
│   │   ├── defaults/
│   │   ├── handlers/
│   │   ├── tasks/
│   │   └── templates/
│   ├── k3s_agent/         # Agent role
│   │   ├── defaults/
│   │   ├── handlers/
│   │   ├── tasks/
│   │   └── templates/
│   └── k3s_reset/         # Reset role
│       ├── defaults/
│       ├── handlers/
│       └── tasks/
└── inventory-sample.yml   # Example inventory
```

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

## License

MIT License - see [LICENSE](LICENSE) for details.

## Acknowledgments

- [K3s](https://k3s.io/) - Lightweight Kubernetes by Rancher
- [K3s Documentation](https://docs.k3s.io/)

### Community Projects

This collection was inspired by and acknowledges the excellent work of these similar K3s Ansible projects:

- [k3s-io/k3s-ansible](https://github.com/k3s-io/k3s-ansible) - Official K3s Ansible playbook (Apache 2.0)
- [techno-tim/k3s-ansible](https://github.com/techno-tim/k3s-ansible) - Popular community K3s Ansible automation (Apache 2.0)
- [PyratLabs/ansible-role-k3s](https://github.com/PyratLabs/ansible-role-k3s) - Comprehensive Ansible role for K3s (BSD 3-Clause)

**Note:** This project was developed independently. While inspired by the approaches and best practices demonstrated by these community projects, no code was directly copied. All implementation is original work.

## About This Project

This collection has been in active development and use in my personal homelab environment since 2023. After two years of refinement and real-world testing, I've decided to open source it for the broader community.

### Tested Environments

This collection has been battle-tested across a wide range of environments:

**Home Infrastructure:**
- Ubuntu Server
- Debian

**Cloud Providers:**
- OVH
- Hetzner
- Digital Ocean
- Linode
- Rackspace
- Oracle Cloud
- IONOS
- Edis Global
- RackNerd

It has also been used to deploy multi-provider K3s clusters spanning nodes across several of these providers simultaneously.

### Scale Testing

This collection has successfully managed clusters with over 60 nodes distributed globally, handling inter-node latency as high as 500ms. This is a testament to K3s's excellent engineering and lightweight design that makes geographically distributed clusters practical.

## AI Assistance Disclaimer

This project was developed with the assistance of **GitHub Copilot** (powered by **Claude**) in VS Code. AI assistance was used to:

- Improve project structure and organization
- Enhance documentation clarity and completeness
- Generate generic example configurations and templates

All code has been reviewed and tested by the maintainers. Any similarity to other projects is purely coincidental.
