# K3s Cluster Collection Examples

This directory contains example configurations for deploying K3s clusters using the `k3s.cluster` collection.

## Directory Structure

```
examples/
├── inventory/
│   ├── single-node/        # Single node cluster (dev/test)
│   ├── simple-cluster/     # 1 server + N agents
│   └── ha-cluster/         # 3 servers (HA) + N agents
├── group_vars/
│   └── all/                # Example variable configurations
└── playbooks/
    └── deploy.yml          # Example wrapper playbook
```

## Quick Start

### 1. Choose an inventory template

Copy one of the inventory examples to your project:

```bash
# Single node (development)
cp -r examples/inventory/single-node ./inventory

# Simple cluster (1 server + agents)
cp -r examples/inventory/simple-cluster ./inventory

# HA cluster (3 servers + agents)
cp -r examples/inventory/ha-cluster ./inventory
```

### 2. Customize the inventory

Edit `inventory/hosts.yml` with your actual hostnames/IPs.

### 3. Customize variables

Copy and modify the example group_vars:

```bash
cp -r examples/group_vars ./
```

Edit `group_vars/all/k3s.yml` to match your requirements.

### 4. Run the deployment

```bash
# Using the collection's playbook directly
ansible-playbook k3s.cluster.site -i inventory/hosts.yml

# Or using the example wrapper playbook
ansible-playbook examples/playbooks/deploy.yml -i inventory/hosts.yml
```

## Example Scenarios

### Development/Testing (Single Node)

Perfect for local development or CI/CD testing:

- 1 node running both server and agent workloads
- Minimal resource requirements
- Quick setup and teardown

### Simple Production (1 Server + Agents)

Good for small production workloads:

- 1 control plane server
- Multiple worker agents
- Simple architecture, easy to manage

### High Availability (3 Servers + Agents)

Recommended for production:

- 3 control plane servers (etcd quorum)
- Multiple worker agents
- Tolerates 1 server failure
- Load balancer recommended for API access

## Customization

See the [defaults](../roles/) in each role for all available variables:

- `common` - OS preparation, packages, kernel modules
- `k3s_server` - Server configuration, TLS-SAN, disable components
- `k3s_agent` - Agent configuration, node labels/taints
- `k3s_reset` - Uninstall options
