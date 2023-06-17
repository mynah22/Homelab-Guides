
# Install and configure PFSense VM
This guide picks up where the [HP Server ESXI install guide](firstConfig.md) ends

We have a hypervisor up and 7 network interfaces configured so that we can attach them to VMs

The first order of business is to set up the VM that will be managing the network; PFSense

My steps are based on the official guide [here](https://docs.netgate.com/pfsense/en/latest/recipes/virtualize-esxi.html), but is specific to this build.

## **Create PFSense Virtual Machine**
1. ### **Add installation ISO to server**
    * Storage > datastores > Datastore Browser ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/esxiSetupDatastoreBrowser.jpg))
    * create a folder in datastore1 names 'OS ISOs' (screenshots
    [1](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/esxiSetupCreateDir.jpg)
    [2](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/esxiSetupCreateOSISOFolder.jpg))
    * Click upload and upload the PFSense 2.6.0 installation .ISO ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/esxiSetupUploadISO.jpg))
    * you can see the progress in the top right of the datastore browser ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/esxiSetupUploadProgress.jpg))

3. ### **Create PFSense VM**
    * Virtual Machines > Create / Register VM ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/esxiSetupCreateVM.jpg))
    * Create a new virtual machine, next ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/esxiSetupCreateVM2.jpg))
    * Select a name and guest OS screen ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/esxiSetupCreateVM3.jpg))
        * Name - up to you, 'PFSense' is an obvious choice
        * Compatibility - ESXI 6.0 Virtual machine
        * Guest OS family - other
        * Guest OS Version - FreeBSD (64 bit)
        * Next
    * Select the datastore you will install this VM onto, then click Next ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/esxiSetupCreateVM4.jpg))
    * Customize settings screen
        * *note, these are simply my recommendations for resource allocation. I've never had any issues with the following settings for PFSense*
        * CPU : 4 (cores)
        * Memory : 2048 MB
        * Hard Disk 1 : 25 GB
        * CD/DVD drive 1 : datastore ISO file (select the pfsense iso you uploaded)
        * Change 'Network adapter 1' to WAN
        * Click 'Add Network Adapter' ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/esxiPFSenseNetAdapt.jpg)) 6 times (so you end up with 7 adapters)
            * ***note: don't worry about assigning Port Groups on this screen - you will certainly end up with mixed up port assignments. We will make sure they are correct shortly***
        * Click [Next](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/esxiPFSenseCustomizeSettingsNext.jpg)
        * Scroll thorugh confirmation screen. click [Finish](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/esxiPFSenseNetworkSetup.jpg)
3. ### **Map virtual NICs**

    This is a very important step!

    1. After you create your VM (and a brief delay), you will see your virtual machine show in the Virtual Machines section of the ESXI webgui. Click it to bring up that VM's detail ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/esxiPFSenseVMSuccess.jpg))
    
   2. click 'edit' ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs13.jpg)), and map the network adapters in this order: WAN, LAN, LAN2-LAN6 ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs14.jpg))
    
4. ### **Install PFSense onto VM**

    At this point you have a virtual machine set up with the following:
    * resources allocated (disk, cpu, memory)
    * virtual network interfaces mapped to port groups
    * a blank virtual hard drive
    * an installation ISO inserted into the virtual DVD drive

    We will boot the installation ISO, install PFSense onto the virtual hard drive, and step through the basic post-installation configuration of PFSense
    
    * Virtual machines > Click your VM name > click the 'play' button ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/esxiPFSenseLuanchVM.jpg))
    * after a delay, a console window will pop up. This is the output of your VM. Nothing is on the hard drive, so it will boot from the ISO inserted into the virtual DVD drive (screenshot
    [1](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/esxiPFSenseLuanchVM2.jpg)
    [2](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/esxiPFSenseLuanchVM3.jpg))
    * After a delay, you will be presented with a copyright notice - at this point the PFSense set up is essentially the same as PFSense Basic Guide, but is listed here since it is really simple

        1. ENTER to accept the agreement ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs0.jpg))
        1. ENTER to install PFSense ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs1.jpg))
        1. ENTER to continue with default keymap ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs2.jpg))
        1. ENTER for Auto (ZFS) disk config ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs3.jpg))
        1. ENTER to confirm disk config ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs4.jpg))
        1. ENTER to proceed with ZFS in stripe mode ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs5.jpg))
        1. SPACE to select VMware Virtual Disk, ENTER to proceed ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs6.jpg))
        1. LEFT, ENTER to acknowledge disk overwrite ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs7.jpg))
        1. Installation will commence ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs8.jpg))
        1. in a couple of minutes you will be at [this screen](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs9.jpg). ENTER to skip a manual shell
        1. ENTER again to reboot the VM. This may take a couple of minutes ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs10.jpg))

 

    PFSense will now boot from the Virtual Hard drive rather than the installation CD ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs11.jpg))

    - *don't worry if it stalls [here](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs15.jpg), PFSense is just looking for a DHCP address on the WAN interface*

    Booting has completed once you are at [this screen](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs12.jpg)

    - In the ESXI webgui, you can interact with the hypervisor while the VM console window is open, just drag it out of the way. The VM will also keep running if you [close](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs17.jpg) the window - click the [console preview image](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs18.jpg) to open the VM output again

    PFSense has been installed and is ready to be configured!
## **Understanding port assignments**
Port names can be confusing:

The port number physically listed on the server, the hardware NIC name, port group/vswitch in ESXI, and interface name in PFSense are all different. 

We are going to go through all of these mappings so that you are able to document / label your ports with confidence

1. ### **Physical ports & NIC names**
    There are 9 total ethernet ports on this server. Four are unlabeled, and the other five are labeled 1-4 and 'ILO'. 
    - the ILO port is for hardware management, and will not be available for our use
    - the four labeled and four unlabeled NICs are named vmnic0-vmnic7
    - vmnic0 is the NIC on the port labelled '1'
    - vmnic1 is the NIC on the port labelled '2'
    - vmnic2 is the NIC on the port labelled '3'
    - vmnic3 is the NIC on the port labelled '4'
    - vmnic4-7 need to be mapped to physical ports

    #### **ID physical ports**
    go to Networking > Physical NICs ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs24.jpg))


    ![](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs23.jpg)
    
    We are going to ID unknown ports by plugging an ethernet cable into a known port and an unknown port:
    
    1. plug an ethernet cable into a known port (I chose the one labeled '4') on one side, and one of the unknown ports on the other 
    2. Refresh the Physical NICs page ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs22.jpg))
    3. You should see three NICs with a link: 
        - vmnic0 (esxi hypervisor - the network where you are connected to the webgui)
        - the known port you plugged into (port '4' in my example)
        - one of the unknown ports

        in this example, we know that the unknown port we plugged into corresponds to vmnic7 

        ![](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs25.jpg)
    4. repeat steps for all unknown ports, you now know the NIC names of all physical ports!



2. ### **Port Group Names**
    you now should be able to look at a physical port and say it's NIC name (vmnic0-7) 

    We will now determine which NICs are attached to the port groups in ESXI

    1. Double check the vSwitch / port group assignments

        We have set things up in ESXI so that each NIC is assigned to a single, isolated vSwitch and port group

        vswitch0 should ONLY be used for the default 'VM Network' and 'Management Network' port groups - this is for security reasons

        There should be 7 vSwitches, named WAN, LAN, and LAN2-6. These should each be the sole members of 7 identically named port groups. Here's what is should look like in Networking>Port groups: 
        
        ![screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs26.jpg)
    
    2. Check which NIC is on each port group

        If you click on a port group you can see what NIC is attached: (screenshots 
        [1](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/esxiPortMap2.jpg)
        [2](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/esxiPortMap.jpg)
        )

        
- if you click on a physical NIC it will display the MAC address of the NIC ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/esxiNICMAC.jpg))


### **Check Interface Assignments**

    we're going to take a take a second to identify the following:
    
    - The mapping of physical ports to vSwitches/port groups in ESXI
    - What the corresponding interfaces are named in PFSense

    This will allow us to firmly map the PFSense interfaces to physical ports, and is an excellent time to take some quick notes / screenshots, and to label ports



    **Physical ports to vSwitches / port groups:**

    - Under Virtual Machines, [click](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs20.jpg) the PFSense VM to open it's configuration page
    - Under 'Hardware Configuration' you will see the network adapters assigned to the pfsense vm. [Click](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs20.jpg the network name and you will see the [port group details page](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs21.jpg). Record this information for all 7 interfaces assigned to PFSense (WAN, LAN, LAN2-6) - I like to take a screenshot (greenshot is an excellent free utility) of each page
    -  

    **Physical ports to NIC names**
    
    network interfaces are numbered starting at 0 (vmnic0, vmnic1, vmnic2 etc). The first four (vmnic0 - vmnic3) are the built in ports, while 

4. **Configure PFSense**
    1. Type '1' at the PFSense main menu ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs16.jpg)), ENTER
    2. N for no vlan set up([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs16.jpg)), ENTER
    3. type 'em0', then ENTER to assign em0 to WAN
    4. type 'em1', then ENTER to assign em1 to LAN
    5. leave prompt empty then ENTER to skip optional interfaces
    6. type 'y', then ENTER to confirm
    7. wait for a couple of minutes for config to be applied, then you will be at the main menu
    8. At the main menu, '2' and ENTER to assign IP address on the LAN interface
    * type '2', ENTER to configure LAN
    * enter a new LAN IP address. MAKE SURE THIS IS NOT THE SAME IP RANGE AS YOUR CURRENT HOME NETWORK (usually 192.168.1.1), (I used 10.0.0.1), then ENTER
    * enter 24 as subnet mask (corresponds to 255.255.255.0), ENTER
    * don't enter an upstream gateway, ENTER
    * ENTER for no ipv6 address
    * type 'y', ENTER for DHCP server, type '10.0.0.2' ENTER for start of DHCP range, then '10.0.0.255' ENTER for end of DHCP range
    * type 'n', ENTER to not revert to http
    * you will recive a confirmation after your changes are applied. ENTER to return to main menu
* okay, we are now ready to enter the PFsense webgui in order to finish our basic configuration.
5. Finish pfsense config via webgui
    * A note on port names:
        - if you followed the steps above exactly, then your port names will match what I list here. Otherwise, you may have mapped them to different physical ports. You can check which physical nic is mapped to which port group by clicking the port group name under Networking. Here is an example. the vmnic numbering starts at 0, so vmnic0 is the port labeled '1;, vmnic1 is the port labeled '2' etc.
    * There are currently a few services listening on multiple ports of the server, and it can be easy to get turned around. Here is a summary:
        * ILO (hardware access) port is not shared with ESXI, and should remain unplugged always
        * the first physical port (labeled '1', vmnic0) has the ESXI web management server listening. It will obtain an ip via DHCP if plugged into a network with a DHCP server (like your home router), but can also be connected to via an APIPA / Link Local address on a network without DHCP (like plugging direct into your PC). Our port group config made sure this port is NOT shared with any VMs for security reasons
        * physical ports 2 - 8 (7 NICs total) have been shared with the PFSense VM, using separate virtual switches (so every port is isolated)
        * the second physical port (labeled '2', vmnic1, em0) is assigned as the WAN interface for PFSense - this will either be your home network (while you get started), or your cable modem / 'the internet' if you move your server to the edge of your network.
        * the third physical port (labeled '3', vmnic2, em1) is assigned as the LAN interface for PFSense. Since we started inside our home network, we set an IP on the LAN interface that does NOT match the subnet of the home network (this is so NAT routing can function). A DHCP server is running on the LAN interface, so any devices plugged into the LAN server port (labeled '3', vmnic2, em1) will obtain an IP address
        * the 4th-8th physical ports are visible to PFSense, but not yet configured (easiest to do so in PFSense webgui below)
    * What we are going to do is create a flat LAN, with WAN plugged into your home network, and all other ports grouped into one 'LAN' interface. This will give you a bunch of ports you can plug into while you get started, but as you build more advanced networks you can start to split ports into seperate, isolated networks as needed (and manage traffic between them with firewall rules)
    * For now, disconnect all cables from the server, and disconnect your PC from all networks (including Wi-Fi). 
    * Then connect am ethernet cable to your PC on one side, and the server port assigned to LAN (labeled '3', vmnic2, em1) on the other. 
        - This will prevent your PC from being able to communicate with the ESXI web management console. That's okay, we will be able to reconnect after a short configuration on PFSense
    * Your PC will grab a DHCP IP from PFSense after connecting the ethernet cable
    * Browse to the IP you configured in step 8 above (also listed as the Gateway ip in `ipconfig` or your OS's equivilent)
    * you will be presented with an HTTPS certificate error. This is because the PFSense server is encrypting your connection (very good!), but is using a self signed certificate that is not known to your PC. Installing the self signed certificate on your computer, or adding a CA to PFSense are options to get around this issue (and come witht the benefit of making a Man in the Middle attack more obvious), but are not neccesary and will not be covered in my guides. 
    * Click through the HTTPS error and you will be presented with the PFSense logon screen
        - Username: admin
        - Passowrd: pfsense
    * Click 'Next' on the first screen
    * Click 'Next' on the Netgate support screen
    * PFSense general information:
        * Set a hostname (hostname that will resolve to the pfsense server) and domain (use your WAN domain if you own one, or name your network whatever you want - just don't use `.local` or a domain that resolves on the WAN!)
        * Primary / seconday DNS setting: 
            * by default, PFSense will use 127.0.0.1 (ie the loopback IP - use the PFSense server for DNS lookups) first, THEN use the primary / secondary DNS server. There is no need to set the primary to 127.0.0.1 - the local DNS server where cached lookups are are stored & Host / Domain Overrides and Aliases are configured is always consulted first, THEN the remote DNS servers you define as primary / secondary here. DNS Blackhole works because it actually resolves an IP address (that does not respond) - if it did not resolve an IP then it would defer the the DNS servers defined here. 
            * Set the primary / secondary DNS as you like - I like to steer clear of my ISPs DNS, and use alternates (common ones are the google, opendns, and cloudflare dns services like 8.8.8.8, 4.4.4.4 & 1.1.1.1). This tool can help determine the fastest DNS servers for your network if you are serious about finding the server with the best performance. If you care about DNS security then you probably already have your preferences and I will not expound further. 
        * Uncheck the Override DNS box - otherwise your ISP DNS can overwrite your config, which I think is garbage
        * click 'next'
    * NTP : leave default NTP server unless you have a preference. Select your time zone.'Next' to continue
    * WAN: 
        * top of the page: selectedtype = DHCP 
        * bottom of the page: **uncheck the 'blvck private networks from entering via WAN'** if the WAN side of PFSense is your home network (reccomended config while you get started). This is because your home network uses a reserved private address as defined in RFC 1918 (192.168.x.x, 172.16/12 and 10.x.x.x), so to allow communications through the WAN side, you will have to allow RFC1918 IP traffic through. **PUT A STICKY NOTE TO CHANGE THIS ON YOUR WAN PORT** if you do this, as you will definetely want to uncheck then if / when you move to the edge of your network
        * leave all other settings, click 'Next'
    * LAN:
        * you will see the config you set earlier (10.0.0.1/24 in my case)
        * leave alone, click 'next'
    * Set PFSense password, click 'next'
    * Click 'relaod' to reload with the settings you configured
    * After a little bit, you will be presented with a congratulations page. click 'finish''
    * Accept the webgui eula
    * congrats, you are done with the initial config of PGSense! Advanced configs are at your fingertips, but let's take a moment to leverage all the darn ports you have to get a flat LAN 
6. You are going to want to build a bridge network in PFSense 
    * in the pfsense webgui, click Interfaces > assignments. Then click '+ Add' five time to add the remaining interfaces. Click Save. 
    * These will be named OP1 - OPT5 by default. if you click the interface name, you can rename them (go with LAN2 thorugh LAN5 to match our esxi names)
    * With all interfaces added in pfsense and renamed, you will build a Bridge network over the LAN ports using This Guide https://eengstrom.github.io/musings/configure-pfsense-bridge-over-multiple-nics-as-lan - it exactly expains the process, I have nothing to add, except that our interfaces will be named differently than his example.

6. Reconfigure physical cabling
    * You should have a single ethernet cable plugged into your Server's port configured as LAN in PFSense on one end, and your PC on the other
    * plug an ethernet cable from your server port configued as WAN in pfsense (labeled '2', vmnic1, em0) to your home network. PFSense should grab a DHCP address and allow devices on the bridged LAN you created (LAN - LAN6) to connect to the internet
    * plug an ethernet cable from your computer into the bridged netowrk (ports LAN - LAN6 should all behave the same)
    * if you need to connect to ESXI, you can plug a ethernet cable into the ESXI management port (labeled '1') on one end, and the bridged network on the other. Connect to it using any device on the bridged network. Not sure of the ESXI web configurator's IP? In pfsense, check status > DHCP leases
    * now, you can connect to pfsense, esxi, and the internet while plugged into the bridge
    * you can split ports off to create isolated networks as needed

7. DNS blackhole set up
    * A DNS blackhole is a very effective way of blocking ads / trackers  / malware on the web. I works for all devices on the network, including devices that cannot install an adblocker locally. 
    * this is definately not required, but only takes a few clicks in PFSense, and is one of the biggest benefits I get out of managing my own network, so I do it right away
        1. There is one, noteable drabwack to running a DNS blackhole: occasionally, it will affect the performance of internet applications / websites. This is actually really easy to deal with if you follow the PFSense DNS Blackhole Allowlist Guide, but if you get frustrated you can always uninstall the PFBlockerNG-Devel package and keep seeing horrible ads :(
        2. System > Package Manager
        3. Available Packages > search for 'pfblockerng'> click 'install' ON PFBLOCKERNG-DEVEL PACKAGE (2nd search result)
        4. click confirm
        5. after a moment you should see the success message
        6. click the PFSense logo at the top left left of the PFSense webgui to return to the main menu. The pfBlockerNG status area on the main page will go green oncew the blocklists load 

8. DDNS
    * free first
    * name.com