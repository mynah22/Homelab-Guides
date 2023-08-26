# Install and configure Proxmox VE
- ## Article type: how-to
  This guide is a how-to guide for the basic installation of proxmox

# Requirements
1. Able to boot to proxmox on target (see rufus and ventoy guides for usb boot stick creation)

# Instructions Index
This guide will cover:
- [Install and configure Proxmox VE](#install-and-configure-proxmox-ve)
- [Requirements](#requirements)
- [Instructions Index](#instructions-index)
- [Installation](#installation)
- [Configure updates](#configure-updates)

# Installation 
once you have proxmox on a bootable USB, the install process should go pretty quick 

1. ENTER to install via GUI ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/proxBasic0.jpg)) 
2. Click to accept the EULA ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/proxBasic1.jpg))
3. Select target disk for installation, then next ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/proxBasic2.jpg))
4. Set localization settings as appropriate, next ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/proxBasic3.jpg))
5. Set proxmox root user password, and enter your email.([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/proxBasic4.jpg)) *(note: the email is used to that proxmox devs can let you know if your server is exposed to new zero day exploits. Very good idea to enter an address you monitor.)*
6. Set network configuration 
   - If proxmox was connected to a network with a DHCP server, it will fill in details obtained via DHCP
   - you can set a static IP later (node>system>network), but now is a great time if you already know what the static will be
   - Fill in desired FQDN
   
     ![](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/proxBasic5.jpg)
7. Review the summary screen, click install ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/proxBasic6.jpg)). The device will reboot - remove usb boot device
8. After booting, you will see the logon screen, indicating the webgui IP. Point your browser to that ip (including the the port `:8006`)
   - It's highly recommended to use the webgui for everything, including shell commands. If you desire you can enter the shell on the device itself (screenshots [1](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/proxbasic7.jpg)[2](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/proxBasic8.jpg))
9. login to webgui using `root` as username, and password set during installation ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/proxBasic9.jpg)).
10. you will be presented with a license notice, click ok ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/proxBasic10.jpg))

# Configure updates

Proxmox has a free update channel for non-production hypervisors. It must be manually added. 

1. Click your node, then under updates click repositories, then add ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/proxBasic11.jpg))
2. Click ok to clear the license notice ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/proxBasic12.jpg))
3. Select 'No-Subscription' from the repository dropdown. Click Add ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/proxBasic13.jpg))
4. Remove the ceph and enterprise repos (screenshots [1](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/proxbasic14.jpg)[2](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/proxBasic15.jpg))

proxmox will now install hypervisor-specific security patches