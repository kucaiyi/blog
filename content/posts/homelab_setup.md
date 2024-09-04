+++
title = "Homelab Setup"
date = 2024-09-03T15:42:18-07:00
+++

## 🚀 Introduction

My homelab is more than just a collection of servers and services—it's a dynamic environment where I explore the limits of what can be achieved with technology. By leveraging Proxmox as my personal cloud, I've created an efficient and organized setup. One virtual machine runs multiple applications within Docker containers, simplifying management and enhancing performance.

## 🌐 Network Layout

At the core of my homelab is a meticulously organized network, managed by a virtualized OPNsense router that handles traffic for all the networks, including VLAN traffic. I’ve segmented the network using VLANs to segregate different types of traffic, enhancing both security and performance. Here’s how it’s structured:

- **Management Network**: Dedicated to controlling my Proxmox and OPNsense setups, ensuring secure access to critical systems.
- **Home Network**: Used for everyday devices, maintaining a smooth and reliable experience for regular activities.
- **Web Network**: Specifically for services like Bitwarden, Nextcloud, and Syncthing, keeping them isolated from other traffic.

All of this is connected through a managed switch, where VLANs are carefully tagged and untagged based on the type of traffic. The virtualized OPNsense router, running as a "router on a stick," efficiently handles inter-VLAN routing through a single trunk link to the managed switch. This setup simplifies network management while providing the flexibility to expand and adapt as my homelab grows.

## 🖥️ Virtualization with Proxmox

Proxmox VE (PVE) is the backbone of my virtualization strategy. It’s running multiple VMs, each isolated and secured using VLANs. A key component of this setup is the virtualized OPNsense router, which handles network management and firewall duties.

- **OPNsense**: Virtualized within Proxmox, it manages network operations, including firewall, VPN services, and VLAN segmentation.
- **Minio**: Provides scalable object storage, ensuring my data is accessible and secure.

By leveraging VLANs, each service operates in its own secure environment, reducing the risk of interference and improving overall system stability.

## 🔧 Services

My homelab runs a variety of essential services, each tailored to specific needs:

- **Nextcloud**: Serves as a central hub for large document storage, online viewing, and file sharing.
- **Bitwarden**: Manages passwords centrally, though it has some limitations with offline editing.
- **Syncthing**: Synchronizes my knowledge base, which is built with Markdown files and edited in Neovim.

These services are containerized using Docker, which keeps them organized, easy to manage, and efficient.

## 🔒 Security

### 🔑 Remote Access

I use Tailscale for VPN access, which is installed on my virtualized OPNsense router. This setup allows me to securely connect to my homelab from anywhere, without exposing my services to the internet.

### 💾 Backup Strategy

A comprehensive backup strategy is critical for maintaining the integrity of my homelab:

- **Storage Solutions**:
  - **Local**: Minio and Proxmox Backup Server (PBS) handle local backups.
  - **Off-site**: Backing up docker volumes to Backblaze B2 provides an additional layer of redundancy, ensuring that my data is safe even in case of local failures.
- **Backup Policies**: Daily and weekly snapshot retention policies keep my backups manageable and up-to-date.
- **Docker Volume Backups**: I run cron jobs to back up Docker volumes, temporarily shutting down containers to ensure data consistency.
- **VM Backups**: A daily schedule integrates with PBS, providing automated, reliable backups of my virtual machines.

### 🛠️ System Updates

Keeping systems up to date is crucial for security and stability:

- **Unattended-Upgrades**: For Debian-based systems, this ensures critical updates are applied automatically.
- **Cron Jobs**: Regular updates for Proxmox VE and OPNsense are managed through cron jobs, ensuring they remain secure and functional.
