
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

    5. #### **Finish pfsense config via webgui**

        With the above network changes, you should be able to use a browser on your PC to connect to the PFSense webgui

        -  Click through the [Certificate Error](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/certError.jpg)
        
            *when you browse to the PFSense webgui, you will be presented with a certificate error. This is because the server is using a self-signed certificate - it is still safely encrypting your communication with the server, but it's identity is not vouched for by any authorities trusted by your machine.*

        - Whaddup PFSense Webgui logon! 
            - Username: admin 
            - Password: pfsense 
        
            (![screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs47.jpg))
        
        #### **PFSense setup wizard**
        1. click 'Next' on the [first screen](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs48.jpg)
        2. click 'Next' on the [support page](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs49.jpg)
        2. **General information page**
            - Hostname: the hostname that PFSense will respond to. I like the default 'pfSense'
            - Domain: the domain of the internal network. If you own a Domain Name, then it should the domain.TLD (ie google.com). Otherwise the only rules are:
                - do not use a domain that resolves on WAN (don't use ebay.com)
                - do not use a domain with .local as the TLD
                - apparently these are the recommended TLDs to use for an internal network. I've included them with an example domain name:
                    - .home (yourDomainName.home) - homelab.home
                    - .lan (yourDomainName.lan) - joebob.lan
                    - .private (yourDomainName.private) - testnetwork.private
                    - .internal (yourDomainName.internal) - janesnet.internal
            - Primary / Secondary DNS Servers

                - *note: by default, PFSense will use 127.0.0.1 (ie the loopback IP - use the PFSense server for DNS lookups) first, THEN use the primary / secondary DNS server. There is no need to set the primary to 127.0.0.1 - the local DNS server where cached lookups, Host / Domain Overrides & Aliases are kept is always consulted first, THEN the remote DNS servers you define as primary / secondary here. DNS Blackhole works because it actually resolves an IP address (that does not respond) - if it did not resolve an IP then it would defer to the DNS servers defined here.* 
                * Set the primary / secondary DNS as you like - I like to steer clear of my ISPs DNS, and use alternates (common ones are the google, opendns, and cloudflare dns services like 8.8.8.8, 4.4.4.4 & 1.1.1.1). [This tool](https://grc.com/dns/benchmark.htm) can determine the fastest DNS servers for your network if you are serious about finding the server with the best performance. If you care about DNS security then you probably already have your preferences and I will not expound further. 
            - uncheck 'override DNS'
            - click 'Next'

            ![](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs50.jpg)
        4. Set your timezone as appropriate. Leave the ntp server alone unless you have a preference. ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs51.jpg))
        2. On the WAN config page, leave everything at default (DHCP), except for the Block RFC1918 checkbox- uncheck this (screenshots [1](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs52.jpg) [2](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs53.jpg))
            - *note: the reason we are unchecking this box is because our WAN port is plugged into another private network - our home network.*
            - *for double NATting to work, **This box must be unchecked**. you will want to recheck it if you move PFSense to the edge of your network*
        2. We already configured the LAN port IP, click 'Next' ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs54.jpg))
        2. set a new admin password for pfsense ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs55.jpg))
        2. click 'reload' to load configuration ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs56.jpg))
        2. the system will reload, and you will be presented with a success screen. click 'Finish' (screenshots 
        [1](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs57.jpg)
        [2](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs58.jpg))
        2. click 'Accept' on the copyright notice ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs60.jpg))
        3. click 'close' on thank you ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs61.jpg))'
        4. you should see a WAN IP in your home network subnet, and a LAN ip matching what you set the PFSense lan IP to ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs62.jpg))
        5. if all of that checks out, your PC should be able to access internet resources - try browsing to an internet website on your PC. If you connect, you have successfully configured a double NAT network! 

    6. #### **Rename OPT interfaces**

        1. In PFSense webgui: Interfaces> assignments ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs63.jpg))
        2. Click 'Add' 5 times. This should assign all available interfaces as OPT1-OPT4. Click 'Save'  (screenshots 
        [1](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs67.jpg)
        [2](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs68.jpg)) 
        3. Rename interfaces:
            Click through all of the OPT interfaces, taking the following steps:
            
            1. click the OPT interface name ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs64.jpg))
            2. check 'enable interface'.  change description to match the ESXI port group it's mapped to - for me 'OPT1' interface was using the em2 network port, which corresponds to the LAN2 port group in ESXI, so I will change the description to 'LAN2' ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs65.jpg))
                - *isn't it nice that you took the time to map port names? I can also say that em2/LAN2 is using vmnic3 / the port labelled '4' because I wrote my mapping down*
            4. click 'save' , then apply changes ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs66.jpg))
            5. Interfaces > assignments and click the next OPT interface
            6. repeat steps 1-5 for all OPT interfaces
            6. you should end up with all OPT interfaces renamed as LAN2-LAN6 ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs69.jpg))
    7. #### **Understanding firewall / interfaces**
        Currently, our network is doing the following:
        - PFSense is working as a NAT router, providing IP translation between our home network (WAN) and homelab network (LAN)
        - PFSense is using DHCP to obtain a WAN IP - this means that your home router is assigning the PFSense IP on it's WAN interface
        - PFSense is providing DHCP services on the LAN port, for a subnet and range we specified
        - all other interfaces are disabled, with no IP/subnet assigned & no dhcp services.

        Let's take a second to understand the default firewall rules and some basics about how traffic is routed

        - **What are the rules**
            
            PFSense's main job is routing traffic between the various interfaces. To accomplish this, every packet is inspected and either 
            - Passed (allowed to transit to the destination port)
            - Blocked (packet is dropped with no message to sender)
            - Rejected (packet is dropped but sender is informed)

            Firewall rules are used to decide whether to Pass, Block or Reject packets

            The firewall works on some basic principles:
            1. ALL traffic is blocked by default. If no rule matches a given packet, it is blocked. 
            2. rules are set to pass/block/reject packets based on various characteristics 
            3. Packets are checked against rules **top to bottom**
            4. The action (pass/block/reject) of the **First matching rule** is taken, all rules below it are ignored

            with that in mind, let's take a look at the default firewall rules of PFSense to understand how our network is operating:

        - **WAN firewall rules**
            
            go to firewall > rules ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs73.jpg))

            ![](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs74.jpg)

            All packets on WAN will go through the following assessments to determine if it will be routed to another interface:
            - If the packet is from an 'IP addressed reserved/ not assigned by IANA' IP - block the packet (We do not expect bogus IPs on the WAN, so these are **blocked** - this is a checkbox in the interface settings)
            - otherwise, **block** the packet. Since firewalls block packets by default, packets will be blocked if no rules apply to them

            As you can see, there is no possibility for packets to be allowed, so ALL WAN traffic wanting to be routed to a LAN host will be blocked *(you certainly wouldn't want internet devices to be able to communicate / connect to your equipment)*

            - *note: firewall rules are all about what can OPEN communications - once a communication has been established, return traffic is allowed. WAN traffic **is** routed to LAN networks, but only if the communication was initiated by a LAN IP*

        - **LAN rules**

            firewall > rules ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs73.jpg))

            click LAN

            ![](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs77.jpg)

            All packets on LAN will go through the following assesments to determine if they will be routed to another interface:
            - ALLOW if the packet is destined for a LAN address on port 443 or 80
                - these are the HTTPS / HTTP ports, and is the first rule to ensure you can always connect to the PFSense webconfigurator (anti lock out rule)
            - ALLOW if the packet is ipv4 and from the LAN network
            - ALLOW if the packet is ipv6 and from the LAN network
            - BLOCK if the packet does not match any of the above rules (firewall default)

            As you can see, any traffic from LAN will be routed. There are no Block rules, and the ipv4 and ipv6 Allow rules will match all packets, so the default Block will never occur, and all packets will be routed as requested

        - **LAN2 - LAN6 rules**

            If you click LAN2 under firewall > rules, you will see no rules

            This means that all packets will go through a straightforward assessment:

            - since no rules are set, the firewall will default to Blocking all packets

        - **Summary**
            - All traffic trying to get from WAN to another interface is blocked
            - All traffic trying to get from LAN to another interface is explicitly allowed
            - All traffic trying to get from LAN2-LAN6 to another interface is blocked
        
    7. #### **Network expansion**

        If you take a look at the summary of firewall rules above, our network currently has a single physical port enabled, and that interface is allowed to connect to any other interface - this is not exactly the most advanced architecture.

        Thankfully you have just set up a feature-complete network appliance and have all the tools you need to build the network of your dreams!

        This section is a brief description of a few of the most obvious / simple ways to expand the network we have built
    8. #### **Net expansion: Enable other interfaces**
        The easiest way to expand on our network is to get another port to behave in the manner that LAN does currently. 

        To do this, we will:
        - Enable the interface
        - configure ip & subnet settings for the interface
        - spin up a DHCP server on the interface, and configure the DHCP range
        - Create a firewall rule to allow traffic from this port to be routed  elsewhere

        You will build more firewall rules as you segment your network, but this configuration is a quick way to get your interfaces up and running 

        1. *Configure interface*

            interfaces > LAN2 ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs76.jpg)) 
        
            - Enable interface,
            - set ipv4 to static
            - set an ipv4 address and subnet that does not overlap with an internal network (LAN subnet was 10.0.1.0/24, so I set LAN2 as 10.0.2.0/24)
            - click 'save' ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs75.jpg))
        - click 'apply' ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs79.jpg))

        2. *Configure DHCP server*
            - Services > DHCP Server ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs78.jpg))
            - click 'Enable DHCP server'. set the range of DHCP addresses (I chose 100 IPs) ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs82.jpg))
            - click 'save ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs83.jpg))
        3. *Configure firewall rules*

            There are no firewall rules on this interface. This means that all packets originating on this interface will be treated with the default action - they will be blocked

            We are going to create a firewall rule that will allow all ipv4 traffic to be routed

            firewall > rules ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs73.jpg))
            
            Click the interface you are configuring

            add a rule ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs84.jpg))

            rule settings:
            - Action: Pass (if a packet matches this rule, allow it to be routed)
            - address family: IPv4 (this rule applies to ipv4 packets)
            - Protocol: Any (this rule applies to any protocol)
            - Source & destination : any (packets can match this rule regardless of source or destination ip)
            - click Save

                ![](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs85.jpg)

            - click 'apply changes' ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs86.jpg))
        
        alright, LAN2 is ready to rock! Now, any device you plug into LAN2 can send packets to LAN or WAN

        Quick note about DHCP and subnets:
        - We have enabled individual DHCP servers on each interface
        - Connected devices will obtain DHCP IP configuration from the server on that interface. With the settings I used in my example, here are the IP configurations I obtained after plugging into [LAN](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs88.jpg) and [LAN2](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs87.jpg) 


    8. #### **Net Expansion: add unmanaged switch**
        This method allows the connection of multiple devices to a single internal network. 
        
        Start by plugging an ethernet cable into a LAN port on one end, and connect the other end to an unmanaged switch. By doing this, any devices you connect to the unmanaged switch will become part of the LAN network to which the switch is connected.
            
    8. #### **Net Expansion: vLAN trunk port**

        This is a technique that allows you to route / firewall traffic between ports on a **managed** switch

        Here is a [diagram](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/vlanTrunk1.jpg)

        I will link my documentation on this process here when it is complete.
    
    9. #### **Net Expansion: WAP**

         When you connect a Wireless Access Point (WAP) to a LAN port, all devices that connect to the WAP's SSID will have their traffic managed by PFSense. This means that the firewall rules set for that interface in PFSense will control and route the traffic of those devices.

        
    7. #### **Connecting to hypervisor**
        I leave the ESXI management port unmanaged by pfsense, and unplugged when not in use - the hypervisor version we are using has known vulnerabilities and preventing any connection is the simplest way to keep it safe.

        - *If you create firewall rules that safely limit access to the hypervisor, you can leave the management interface connected.*

        Assuming you followed the [instructions to enable a secondary interface](#net-expansion-enable-other-interfaces) and have not added any additional firewall rules, connecting to the hypervisor is as simple as:
        1. Your PC should be plugged in to LAN. WAN port on the server should be plugged into your home network. Your PC has a DHCP IP on the LAN subnet, and is able to communicate with WAN (both your home network and the internet) and LAN2 due to our  firewall configuration
        2. plug an ethernet cable into the ESXI management port (vmnic0) on one end, and a LAN2 on the other
        2. the esxi hypervisor will obtain a DHCP lease from the PFSense DHCP server on that interface
        4. Since LAN and LAN2 have a firewall rule allowing all ipv4 packets, traffic can pass between them
        4. on your PC, browse to the IP of the ESXI server. This IP will be listed on the server display (if you connect a vga monitor), but an easy way to determine this remotely is by looking for the hypervisor's DHCP lease:
            - Status - dhcp leases ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs89.jpg))
            - This will show dhcp leases for *all interfaces*. Since we have a single device on LAN2, it should be easy to identify using the DHCP ip range on LAN2 ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs90.jpg))

        ![]()
        5. Your browser should connect to the ESXI web management server, where you can manage Virtual Machines

         
        
        - 






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