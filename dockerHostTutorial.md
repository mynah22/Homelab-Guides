# Docker Host tutorial
- ## Article type: Tutorial
    This is a tutorial where a Docker container host is built, and several containers created, with networks managed by Traefik. 

# Overview

This tutorial will have you build a docker environment on an Ubuntu host. Creation, management, and backup / recovery of containers will be discussed.  

Several containers will be built:

**note to self:think about what containers are useful for this tutorial. This is not a full build guide, just a demo**

- Traefik
  - Very powerful tool for managing / routing traffic to containers
  - Will be generating SSL certs for other containers via the ACME protocol
- Vaultwarden
  - Community implementation of Bitwarden (password manager)
- Nextcloud
  - Self hosted cloud storage and more
- Minecraft server
- MSSQL database server

Additionally, this tutorial leverages docker-compose files to make deployment and management of containers simpler. 

# Prerequisites
1. A fresh Ubuntu server installation (this tutorial uses 22.04), with sufficient resources to run the containers (not much, I will test later).
    - This could be located anywhere: as a VM  on a hypervisor, an LXC container, or on bare metal. See the [proxmox ubuntu tutorial]() for a guide on configuring an Ubuntu server VM on proxmox.
2. 

# Instructions Index
This guide will cover:
- [Install and configure PFSense](#install-and-configure-pfsense)
- [Requirements](#requirements)
- [Instructions Index](#instructions-index)
- [Configure PFSense via terminal](#configure-pfsense-via-terminal)
- [Finish pfsense config via webgui](#finish-pfsense-config-via-webgui)

# Configure PFSense via terminal

- ## Installation