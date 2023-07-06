# Creating a fresh proxmox system container

## Create a template
In the proxmox main window, with the left panel set to 'Server View':
- datacenter/cluster > your proxmox hostname > select a storage ('local')
- click 'CT templates on the left of the storage window
- click 'Templates' button 
- click on a distribution, then 'Download'
- You can close the task, download will proceed in background

## Create container
- click the 'Create CT' button on the top right of the proxmox window
- give it a hostname and password, 'Next'
- select storage, click 'Template' dropdown and select the distro you downloaded, 'Next'
- adjust storage as needed, 'Next'
- adjust cpu as needed, 'Next'
- adjust memory as needed, 'Next'
- set ipv4/ipv6 to dhcp, 'Next'
- 'Next' on dns screen, 'Finish'


## start container 
- The container will appear on the left panel of proxmox
- open a console session for the container. click 'start now' to spin uup the CT
- login:
    Username = root
    Password = what you set @ container creation


## Create user account
- `adduser yourname`
- set & confirm password
- ENTER to skip details
- type Y, then ENTER to confirm

## Add user account to sudoers
- `usermod -aG sudo yourname`

## SSH into container
- use ssh / vscode to connect to yourname@containerHostname


