# Create a new VM in Proxmox

- ## Article type: how-to
  This guide is a simple demonstration of creating a VM in Proxmox

  For a full, detailed reference on this process, see the [official documentation](https://pve.proxmox.com/wiki/Qemu/KVM_Virtual_Machines)

# Requirements
1. A proxmox host with sufficient resources for the target OS
2. A datastore where ISO files can be uploaded
   
# Instructions 
This will be brief, as the [official documentation](https://pve.proxmox.com/wiki/Qemu/KVM_Virtual_Machines) is very complete
1. log into your proxmox server / cluster
2. click 'create VM' on the top bar ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/proxVm0.jpg))
3. General screen
    - Select node (if in a cluster)
    - Name the VM
    - You can elect to have this VM auto start when the hypervisor boots by checking the 'Start at boot' box
    - click 'Next'
        ![](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/proxVm1.jpg)
4. Select storage location and .iso file you would like to install. Make sure the guest OS type and version match what you are installing. Click next. ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/proxVm2.jpg))  
5. Defaults are fine on the System tab. Click Next. ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/proxVm3.jpg))
6. Make sure the storage location and disk size are appropriate for your VM. You can add other disks if your VM requires. Click Next. ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/proxVm4.jpg))
7. Set the number of vCPUs you would like the VM to have. Click Next. ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/proxVm5.jpg))
8. Enter the amount of memory the VM will have, click Next. ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/proxVm6.jpg))
9. Configure the network settings for the VM, click Next. ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/proxVm7.jpg))
10. Review the VM details. You can check the 'start after created' box to start the VM as soon as you close the window. Click Finish to complete the VM creation ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/proxVm8.jpg))


