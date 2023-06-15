# Homelab on HP DL380 G7 and ESXI

As part of decommissioning my old homelab, I have rebuilt and documented most of the major components here. 

### Caveat
The newest version of ESXI that runs on the server used in this build is 6.0.0 - This is an old version, and is unsupported. 6.5.0 will install, but crashes as soon as it tries to write to the filesystem. 

The license for 6.0.0 is free, but I cannot find a way to obtain license keys or installation ISOs any longer. If you do not already have them, then will need to either use a different server compatible with ESXI 7+, or a different hypervisor like proxmox
### Materials
Initial set up requires the following:
* Server and VGA capable monitor plugged into power
* VGA Cable plugged into monitor and server
* Keyboard plugged into server
* At least one 2.5" SATA disk installed into caddy / slot
    * Mount your SATA disk into a drive caddy
    * insert it fully into slot
    * best to attach all disks you plan to use at first, that way you do not need to get into the RAID menu when you add them later
* ESXI 6.0 HP custom image installed on a bootable USB (See Rufus and Ventoy guides) 
* Bootable USB inserted into USB slot - see [Rufus](rufus.md) and [Ventoy](ventoy.md) guides
* VMWare account & ESXI license (free)
* Copy of PFSense 2.6.0 and Ubuntu server 22.04 disk images (.ISO) on your computer
* an ethernet cable ready for use
* no ethernet cables plugged into server
* an open port on your home network (or an unmanaged switch to add more ports)

### Hardware utilized in this build
- HP DL380 G7 2u rack server
- A few ethernet cords (I count 3, but 4 couldn't hurt)
- A PC with ethernet port (for configuration of hypervisor / VMs)
- Archaic desktop (FreeNAS host via ISCSI) (not needed)


## Services overview
### Hypervisor & guest VMs
HP Rack server has the ESXI hypervisor installed. ESXI hosts 2 Virtual Machines -  PFSense and Ubuntu Server

1. PFSense provides a variety of standard network services, as well as a DNS blackhole for adblocking, and custom DNS / DHCP configuration. 

2. The Ubuntu VM directly runs a Wireguard instance, providing VPN access, and hosting other services via docker

### Docker containers
The Ubuntu VM does not run any other services directly - instead, it is a docker host running the following containers:
1. Traefik
    - Reverse proxy & load balancer
    - Manage free SSL certs to encrypt traffic for other services
2. Minecraft server
    - Minecraft server container
3. Bitwarden
    - Self hosted password manager
4. Nextcloud
    - Self hosted storage cloud
5. Calibre web
    - Self hosted Ebook library

Additionally, I had set up a very old desktop as a NAS.

This is really not necessary - the server I had was equipped with 8 SATA slots and hardware RAID, but will be documented for my reference.

NAS details:
- OS: TrueNAS 
- Filesystem: ZFS (Software RAID 1)
- Access via ISCSI
