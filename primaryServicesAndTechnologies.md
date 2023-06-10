# Primary Services and Technologies 


This document is a brief description of the technologies and services we will be configuring in the other guides in order to give you an overview of what we will be accomplishing, and maybe some inspiration!

## Services
Arguably, the most valuable part of setting up a homelab is gaining useful skills and knowledge, but there are very useful things you can deploy with low effort. 

Lets make sure your homelab is not a toy; here are some of the services you can build out using the other guides: 

- PFSense (Network appliance / firewall) services:
    * DNS Blackhole:
        - The most effictive advertisement and tracking blocking method. Works for all devices on your network without client side configuration, saving bandwidth and your eyes
    * Network Isolation:
        - Keep untrusted devices away from sensitive parts of your network
        - Optimize bandwidth utilization on your network
    * NAT Port Forwarding:
        - Allow internet traffic to communicate with services on your network
        - Allow anyone to access your deployed services from outside your network
    * VPN:
        - Securely tunnel into your network to access resources without public availibility (not wireguard)
    * Firewall:
        - Restrict or allow traffic to your heart's desire
    * DNS services:
        - Create records to map hostnames to ip addresses
    * DDNS
        - Free mapping of your non-static WAN address to your IP
        - Keep your domain name tied to your IP

- Docker
    * Host containerized services 
  
- Minecraft server
    * Play minecraft with your family / friends on your hardware

- Nextcloud
    * A private storage cloud for you and your family 

- Bitwarden
    * Self hosted password managed for you and your family 

- Wireguard (VPN)
    * VPN 
    * Securely access your home network from the outside world 

- Calibre-web
    * E-Book server with web interface
  

- Traefik (Reverse proxy, load balancer for containers)
    * Route traffic to multiple hosts / containers from a single ingress point
    * Encrypt traffic via publically trusted SSL certificates with no configuration needed to the service







## Technologies
    These are the technologies utilized / configured in the other guides


### Hypervisors
- ESXI 
    * VMWare product widely used in Enterprise environments
    * We will leverage the Free Version
- Proxmox
    * Open source


### Operating systems
- PFSense 
    * General purpose network appliance
    * A general purpose network appliance - be the master of your network!
    * Very secure, robust, and flexible 
    * Easy to use interface
    * It takes mintutes to set up a basic PFSense configuration with a DNS Black hole!
    * That basic configuration is extensible to handle very complex network architechtures - you will not outgrow PFSense for your home lab

- Ubuntu server
    * Widely used linux server distribution
    * No gui
    * Used as the docker host in our examples
- Freenas
    * Open source NAS server


### Utilities
- GParted
    * Swiss army knife for parititioning disks
- Rufus
    * Create bootable USBs
- Ventoy
    * Boot from multiple OSes on the same USB. Slightly more complicated than Rufus

### Storage
- ISCSI
    * Disk access over TCP/IP
- RAID
    * Leverage multiple disks for redundancy and / or speed
- ZFS
    * Advances filesystem allowing for software RAID
- VMFS
    * File system used by ESXI
- VMDK 
    * Widely used virtual disk format
- EXT4
    * Common linux file system

### Networking
- VLANs
    * Switch traffic among ports to create isolated LANs virtually
- VLAN Trunking
    * Bring traffic from multiple vlans together
- NAT Routing
    * Create a private network within the outside WAN
- NAT Port forwarding
    * Allow traffic from a WAN port to pass to a LAN port through a NAT router
- DNS
    * Provide an IP address for a given hostname when asked by a device on the network
- DDNS
    * Map your non-static WAN IP address to a hostname publically
- DHCP
    * Allow client devices to obtain IP addresses dynamically. Lease and reserve as needed
- DNS Blackhole
    * Primarily used to block ads and tracking - block any traffic at the dns level by resolving it to an address that never responds


