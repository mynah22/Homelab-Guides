# Homelab on HP DL380 G7 and ESXI

As part of decommisinoning my old homelab, I have rebuilt and documented most of the major components here

## Hardware:
HP DL380 G7 2u rack server (Server, ESXI hyperisor)
Cable modem
WAP
Archaic desktop (FreeNAS host via ISCSI) (not needed)


## Big picture architecture:
HP Rack server has ESXI installed. ESXI hosts PFSense and Ubuntu VMS

The Ubuntu VM directly runs a Wireguard instance, providing VPN access (PFSense configuration required)
The Ubuntu VM does not run any other services - instead, it is a docker host using the following containers:
1. Traefik
    - Reverse proxy & load balancer
    - Manage free SSL certs to encrypt traffic for other services
2. Minecraft server
    - Remarkably easy to set up containerized minecraft server
3. Bitwarden
    - Self hosted password manager
4. Nextcloud
    - Self hosted storage cloud
5. Calibre web
    - Self hosted Ebook library

Additionally, I had set up a very old desktop as a NAS.
This is really not neccessary - the server I had was equipped with 8 SATA slots and harware RAID. I indlude this information mostly for my reference. 

NAS details:
- OS: FreeNAS 
- Filesystem: ZFS (Software RAID 1)
- Access via ISCSI


## Getting Started
If you are building your first home network / homelab, you will run into difficulty at some point. Since you are experimenting on your home network, any errors you make affect not just your lab, but your entire family. 

I highly reccomend looking at the Basic Config Network guide - this guide describes a very easy way to test your homelab / network while keeping your existing network functioning. If you follow the you should not run into any trouble, and may safely migrate to the edge of your network without any additional configuration 


With that said, let's get to the build steps:

## Server setup
- Initial set up requires the following:
    * Server and VGA capable monitor plugged into power
    * VGA Cable plugged into monitor and server
    * Keybord plugged into server
    * At least one 2.5" SATA disk installed into caddy / slot
    * ESXI 7 (ESXI 8+ is not compatible with the g7 380!) bootable USB (See Rufus and Ventoy guides) inserted into USB slot 
    * VMWare account & ESXI license (free)



1. Initialize disk
    * You must register all disks with the RAID controller in order for them to work
    * This inlcudes disks that are not being used with other disks for a RAID array
    * 
    1. Enter RAID config:
        * Turn server on. Hold power button for about a second, and you will be presented with the Boot Screen 
        * Once this screen finishes loading, start pressing F8
        * F8 will initialy get you into the ILO configuration (hardware level management console)
        * exit ILO config, keep spamming f8 during RAID loading screen
        * congrats you have gotten to the secret RAID menu!
        * Configure a RAID device
        * 