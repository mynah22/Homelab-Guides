# Create a new system container in Proxmox

- # Article type: how-to
  This guide is a simple demonstration of creating an LXC container in Proxmox

  For a full, detailed reference on this process, see the [official documentation](https://pve.proxmox.com/wiki/Linux_Container). Note that the wiki page uses command line management, rather than the webgui.

# Requirements
1. A proxmox host with sufficient resources for the target container
   
# Instructions Index
1. [Create a template](#create-a-template)
2. [Create container](#create-container)
2. [start container](#start-container)
2. [Create user account](#create-user-account)
2. [Add user account to sudoers](#add-user-account-to-sudoers)
2. [SSH into container](#ssh-into-container)

This will be brief, as the [official documentation](https://pve.proxmox.com/wiki/Linux_Container) is very complete


# Create a template
In the proxmox main window, with the left panel set to 'Server View':
- datacenter/cluster > your proxmox hostname > select a storage ('local'). Click 'CT templates on the left of the storage window ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/proxLxc0.jpg))
- click 'Templates' button 
- click on a distribution, then 'Download'
- You can close the task, download will proceed in background

# Create container
- click the 'Create CT' button on the top right of the proxmox window
- give it a hostname and password, 'Next'
- select storage, click 'Template' dropdown and select the distro you downloaded, 'Next'
- adjust storage as needed, 'Next'
- adjust cpu as needed, 'Next'
- adjust memory as needed, 'Next'
- set ipv4/ipv6 to dhcp, 'Next'
- 'Next' on dns screen, 'Finish'


# start container 
- The container will appear on the left panel of proxmox
- open a console session for the container. click 'start now' to spin uup the CT
- login:
    Username = root
    Password = what you set @ container creation


# Create user account
- `adduser yourname`
- set & confirm password
- ENTER to skip details
- type Y, then ENTER to confirm

# Add user account to sudoers
- `usermod -aG sudo yourname`

# SSH into container
- use ssh / vscode to connect to yourname@containerHostname


