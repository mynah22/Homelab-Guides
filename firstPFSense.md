
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

4. ### **Configure PFSense**
    to proceed, you are really going to want to make sure you know which pfsense interface names for all of the physical ports on the server

    Thankfully, this is easy if you have followed [the Understanding Port Assignment instructions here](firstConfig.md#understanding-port-assignments).

    Having determined the NIC and port group of each physical port, we are ready to do the post installation configuration of pfsense:

    1. #### **Determine interface mapping**
        - Type '1' at the PFSense main menu ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs16.jpg)), press ENTER
        - you should see a screen like [this one](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs28.jpg). It displays the interfaces that PFSense sees, including their name and MAC address. 
        - In the PFSense page on the ESXI web management, click a network adapter to expand it's view
        - You can see the MAC address of the network adapter. Look for the interface in the PFSense window with the matching MAC address

            ![screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs29.jpg)

        [The Understanding Port Assignment instructions](firstConfig.md#understanding-port-assignments) left you with a map of the following information for the 7 NICs we are managing with PFSense:
        - physical port label
        - NIC name
        - ESXI port group / vSwitch name

        to that map, add the interface name as PFSense sees it. For example, I had the following for one of my ports:
        - Port label: 'a'
        - NIC name: vmnic5
        - ESXI port group / vSwitch name: LAN6
        - PFSense interface: em6

        Using the above technique (expand network adapter, match to interface name in pfsense using MAC), add the interface names for all 7 ports we are managing (WAN, LAN, and LAN2-6)

        that's it - all names for ports have been mapped! Your future self will thank you for taking the time to understand and write these details down :)

    2. #### **Assign Interfaces**
        Back to the PFSense console:

        Before we mapped interface names to port groups, we had entered '1' in the PFSense console main menu

        That option ('assign interfaces'), is used to assign the role of the network interfaces that PFSense sees. One of PFSense's primary functions is as a NAT router - to perform that function, it has to know which port is designated for the WAN connection, and which port(s) are on the 'inside' of the NAT (LAN ports)

        - N for no vlan set up([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs34.jpg)), ENTER
        - type the interface name that corresponds to the WAN port group (see Determine Interface Mapping above), ENTER to assign that interface as WAN in PFSense ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs30.jpg))
        - type the interface name that corresponds to the LAN port group, ENTER to assign as LAN in PFSense ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs31.jpg))
        - You could assign optional interfaces (LAN2-LAN6) here, but it's easier in the PFSense webgui - ENTER to skip ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs32.jpg))
        - type 'y', then ENTER to confirm assignments ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs33.jpg))
        - PFSense will take a little while to apply the interface settings. You will then return to the console main menu
    3. #### **Change LAN IP Configuration**
        The network we are building is sitting inside of our home network ("Double NAT"). To make this work, the network inside the PFSense NAT needs to operate on a different subnet than the WAN (in our case - our home network)

        By default PFSense will use the 192.168.1.0/24 subnet (255.255.255.0 mask). Your home network is likely running on a 192.168 subnet, and there is a good chance that it is running on 192.168.1.0/24

        Connect a machine to your home network, and determine your home network's subnet (`ipconfig` is a cli tool on windows that will display this info - [screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs37.jpg)), you should be able to find this info in the settings menu of any operating system
        
        There are a few IP ranges that are reserved for 'private' networks (inside at NAT router, not internet-facing IPs):
        - 192.168.0.0/16
        - 172.16.0.0/12
        - 10.0.0.0/8

        subnets are defined *per interface*, so pick a /24 network (255.255.255.0) within one of the private ranges above that does not overlap with your home network subnet. I went with 10.0.1.0/24 (10.0.1.1 - 10.0.1.255)
        
        - At the PFSense console main menu, '2' and ENTER to assign IP address on the LAN interface ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs35.jpg))
        - '2' for LAN, ENTER ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs36.jpg))
        - type the ip address ***you want PFSense to have***. I used the first IP in the range - 10.0.1.1 ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs38.jpg)). **Write this IP down - you will need it to connect to the PFSense webconfigurator**. ENTER
        - enter the bitwise subnet mask - 24 in our case ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs39.jpg)). ENTER
        - We are configuring a LAN - ENTER for no upstream gateway ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs40.jpg))
        - ENTER for no ipv6 address ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs41.jpg))
        - type 'y' to enable DHCP on the LAN interface ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs42.jpg))
        - The PFSense DHCP server we just enabled will assign IPs in a fixed range. Enter the beginning of that range (I went with 10.0.1.2  - the next available IP), ENTER ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs43.jpg))
        - Enter the end of the DHCP range. I went with 100 IPs, so 10.0.1.102 ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs44.jpg)), ENTER
        - type 'n' then ENTER to keep webconfigurator connections encrypted ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs45.jpg)) 
        - Things will reload and you will se the new ip assignments ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs46.jpg)). Press ENTER to return to the PFSense console main menu

    4. #### **Network rearrange**

        *note: we are disconnecting the ESXI management server. You can always attach port 1 / vmnic0 to the same network as your PC in order to access the ESXI webgui. **Write down the PFSense LAN ip if you have not already***

        We are going to move some cabling to accomplish the following goals:
        - ILO (hardware management) port not in use
        - port 1 (vmnic0) is used for ESXI management ONLY, and is unplugged when not configuring VMs
        - port 2 (or whichever port you have assigned to WAN in PFSense) is used for the WAN connection. This will be your home network at first, but can easily be your cable modem if / when you feel comfortable moving PFSense to the edge of your network.
        - the remaining 6 ports are managed by PFSense
        - at first, port 3 (or whichever port you have assigned to LAN in PFSense) will be the only way to connect to / configure PFSense

        ![](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/firstConfigDoubleNat.jpg)

        Here a rundown of the new cabling:
        - remove all network cabling from your server
        - disconnect your PC from all networks, including wifi
        - plug an ethernet cable into 
            1. the physical port corresponding to the LAN interface as assigned in PFSense in the [assign interfaces](#assign-interfaces) section above (aren't you glad you made a map of port to interface names?)
            2. The PC you are using to configure your homelab
        - plug another cable into
            1. the physical port corresponding to the WAN interface as assigned in PFSense in the [assign interfaces](#assign-interfaces) section above
            2. an open port in your home network

    5. ### **Finish pfsense config via webgui**

        With the above network changes, you should be able to use a browser on your PC to connect to the PFSense webgui

        -  Click through the [Certificate Error](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/certError.jpg)
        
            *when you browse to the PFSense webgui, you will be presented with a certificate error. This is because the server is using a self-signed certificate - it is still safely encrypting your communication with the server, but it's identity is not vouched for by any authorities trusted by your machine.*

        - Whaddup PFSense Webgui logon! ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/certError.jpg))

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