Proxmox PFSense deployment on a device with a single nic

I have demonstrated how to use a single nic dvice and an inexpensive managed switch to deploy a robust, segregated network via PFSense in the [One NIC PFSense guide]

However, this leaves quite a bit of unused resources - PFSense only uses a couple of GB of disk, and my old pfsense VM was always happy with 1GB of RAM. 
We will be leveraging a hypervisor in order to allocate the unused resources to other VMs. In an example usecase, I will be deploying a VM to use the unused disk space to store backups from other nodes on my network in an attempt to reduce the impact of any single disk failing. 
Using a hypervisor also allows us to take VM snapshots (huge gain for ease of management), and leverage advanced features like High Availability and Clustering. 

The cost of being able to access those unutilized resources is a USB NIC. This will be used to handle the proxmox management interface.  
I am **not** a fan of using a USB NIC to handle 100% of my trafic (as with some folks who use a laptop+usb nic to acheive a WAN and LAN interface for PFSense). However, in this configuration, the USB NIC will only handle the traffic to and from the proxmox instance hosting my pfsense VM. This is well within the limitations of a USB NIC, and if service diminishes / stops it will only affect access to that single proxmox host (which should be getting minimal traffic anyway) - the rest of my network will be running without issue.

- Required materials:
    * Some type of computer with a single NIC
    * A ***Managed*** switch that supports 802.1q. I chose the TP-Link TL-SG108e
    * A USB NIC 
    * A few ethernet cables will also be required to get through testing

- Before you get started, you will have to have already configured your managed switch (and preferably tested the configuration). See the [One NIC PFSense guide] for an overview using a TP-Link TL-SG108E switch. 
    * for this reason, I reccomend that you either 
        1) Very thoughourly plan your switch config / subnets / vlans /  firewall rules, 
        2) or better yet install pfsense onto bare metal and configure / test there. This will ensure that you have the correct network information for the proxmox web management interface, and that your switch does what you expect when you plug PFSense into it. You can always move to a VM from a bare metal install later (which will force you to rebuild everything and document your architecture after you probably made changes to your initial design :)
- Create a bootable USB for proxmox. Rufus is a good choice for building the USB stick, as is Ventoy.
- Plug the USB stick into the device you inend to install proxmox / pfsense on. Enter the boot menu and select the bootable usb
- Once you boot to Proxmox, installation requires no explanation besides the following **important note:** 
    * On the network set up section, ensure that you have selected the USB NIC, and enter the network configuration for the subnet that this proxmox management host will reside on (this is why it is best to have already decided on and tested your subnet & vlan & switch configuration)
- After installation, you can access the proxmox web interface this way:
    * plug an ethernet cable into the USB NIC attached to the proxmox server on one end, and your computer on the other
    * manually configure your IP settings on your computer to match the subnet you configured for the proxmox host
    * browse to the ip address:port (it's printed on the proxmox console - you set the ip during the proxmox installation, default port is 8006)
- At this point you can configure and install a pfsense VM on the proxmox host via the web management portal
    * add the pfense iso to the proxmox host:
        1) browse to the local storage, and click on 'ISO images'
        2) click 'upload'
        3) select the pfsense iso on your machine
        4) enter the file hash into 'checksum' - this can be produced via `get-filehash` in powershell, or `md5sum` in linux
            * *note: you can also determine the hash by entering a bogus checksum and looking at the details after it fails*
    * create the virtual network for PFSense
        1) click your proxmox hostname under datacenter
        2) browse to system > network, click Create > Linux Bridge
        3) name the network vmbr1 (vmbr0 through vmbr9999 are valid)
        4) ***CHECK THE VLAN AWARE BOX***
        5) under Bridge ports: type the interface name corresponding to the built-in NIC - this can be found in the system > network page. (Hint: the usb NIC will be listed under ports/slaves of vmbr0; choose the other one listed as 'network device')
        6) leave autostart checked and all other fields blank
    * create the pfsense VM
        1) click the 'create VM' button on the top right of the management window
        2) general tab: enter a name, and check 'start at boot'. leave other fields default
        3) OS tab: click the ISO Image field and select the pfsense .iso you uploaded. Chznge guest OS type to 'other'. Click Next
        4) System tab: all defaults. Disk tab: change disk size to 10- 15GB (My old PFSense VM only took about 8GB of disk). Click Next.
        5) CPU tab: leave sockets at 1. Use at least 2 cores - this choice depends greatly on the power of your proxmox machine's cpu power, and what else you plan to use it for. Start high, you can always reduce this later. All others at default. Click Next
        6) Memory tab: 2GB is *tons* (I used 1GB for years without ever seeing utilization at 100%), but this again depends on how much memory is available, and how you plan to use the device. Start high and scale back as you deploy other VMs. Click Next
        7) Network tab: under bridge, choose the virtual network you created above (vmbr1, or the number you chose). Uncheck firewall. Others at default. Click Next
        8) Confirm tab: review summary. check the 'start after created' box, and click 'Finish'
    * pfsense configuration
        1) Please see the [One NIC PFSense guide] for a reference in the initial set up process with VLANS
        2) Once you have assigned the WAN and LAN interfaces to the appropriate VLANs, and set the IP adress on LAN, you are ready to move cabling:
            * plug your computer into a port assigned to the LAN VLAN
            * plug the WAN into a port assigned to the WAN VLAN
            * plug the USB NIC into a port on the switch as appropriate (corresponding to the 'Management' VLAN in my configuration)
        3) Initial set up must be completed via the webconfigurator- browse to the pfsense LAN IP. Configuration will be required to set firewall rules (see the [One NIC PFSense guide]), as well as any VLANS not configured during the cli set up.
        4) note: if your WAN network is a private network (say, your existing home network), you will not be able to obtain a WAN DHCP lease or connect to the internet until you uncheck 'block private networks and loopback addresses' under interfaces>WAN. You really want this checked if WAN is the internet, so put a sticky note on the server to check the box 'block private networks and loopback addresses' again if you plan to move the device to the edge of your network. 
- After installing PFSense and configuring VLANs / firewall rules, test to make sure you can connect to the proxmox managment console as expected
- done!
