# Create a new system container in Proxmox

- # Article type: how-to
  This guide is a simple demonstration of creating an LXC container in Proxmox

  For a full, detailed reference on this process, see the [official documentation](https://pve.proxmox.com/wiki/Linux_Container). Note that the wiki page uses command line management, rather than the webgui.

# Requirements
1. A proxmox host with sufficient resources for the target container
   
# Instructions Index
1. [Create a template](#create-a-template)
2. [Create container](#create-container)
2. [Start container](#start-container)
2. [Create user account](#create-user-account)


This will be brief, as the [official documentation](https://pve.proxmox.com/wiki/Linux_Container) is very complete


# Create a template
In the proxmox main window, with the left panel set to 'Server View':
- datacenter/cluster > your proxmox hostname > select a storage ('local')
- Click 'CT templates on the left of the storage window
- click 'Templates' button
    ![](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/proxLxc0.jpg)
- click on a distribution, then 'Download' ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/proxLxc1.jpg))
- You can close the task, download will proceed in background

# Create container
- click the 'Create CT' button on the top right of the proxmox window
- give it a hostname and password, 'Next'
- You can upload a key file if you want to distribute one at time of creation
    ![](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/proxLxc2.jpg)
- select storage location of the template, click 'Template' dropdown and select the distro you downloaded, 'Next' ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/proxLxc3.jpg))
- adjust storage as needed, 'Next' ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/proxLxc4.jpg))
- adjust cpu as needed, 'Next' ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/proxLxc5.jpg))
- adjust memory as needed, 'Next' ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/proxLxc6.jpg))
- set ipv4/ipv6 to dhcp, 'Next' ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/proxLxc7.jpg))
- 'Next' on dns screen([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/proxLxc8.jpg))
- You can check the bock to start up the container as soon as it's created. Review details and click 'Finish' ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/proxLxc9.jpg))


# Start container 
- The container will appear on the left panel of proxmox ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/proxLxc10.jpg))
- If you did not autostart the container, right click the container name, then 'Start' ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/proxLxc11.jpg))
- Once started, open the console ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/proxLxc12.jpg))

    Username = root

    Password = what you set during container creation
- 


# Create user account
- `adduser yourname`
- set & confirm password
- ENTER to skip details
- type Y, then ENTER to confirm
- Add user account to sudoers:  `usermod -aG sudo yourname`
  
    ![](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/proxLxc13.jpg)
