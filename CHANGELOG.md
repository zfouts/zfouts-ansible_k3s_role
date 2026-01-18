# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.0.0] - 2026-01-18

### Added
- Initial release of K3s cluster Ansible collection
- Role for K3s server deployment with HA support
- Role for K3s agent deployment
- Role for cluster reset/cleanup
- Prerequisite role for system preparation
- Support for multiple K3s installation methods (online, airgap)
- Configurable networking (Flannel, custom CNI)
- TLS SAN configuration for HA clusters
- etcd snapshot configuration
- Container registry configuration
- Example inventories for different deployment scenarios
- Sample playbooks for deployment, upgrade, and reset operations

### Features
- High availability cluster support with embedded etcd
- Airgap installation support
- Custom CNI plugin support
- WireGuard backend support for Flannel
- Kernel parameter tuning
- SELinux support
- Systemd service management
- Token-based authentication between nodes
- Kubectl configuration management
