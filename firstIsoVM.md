# Windows server VM on isolated network 
When it comes to managing the core of your network, PFSense is an excellent choice. It's simple to configure, easy to understand, and does everything you'll need.

However, in a laboratory environment, you might want to experiment with different network stacks and technologies. This guide focuses on deploying a Windows Server in a way that allows it to provide network services (such as DHCP and DNS), while still being managed by the firewall within your PFSense network.

We are also building a IIS webserver on Windows Server to help demonstrate the firewall rules

*It's important to note that isolating Windows Server it in this manner is **Not Required** (you could just place it on LAN). The example provided here specifically addresses scenarios where the server is responsible for services that are typically handled by PFSense (such as DHCP and DNS).*
# Requirements
1. Completed the [Server & Hypervisor Setup Instructions](firstConfig.md) 
2. Completed the [PFSense Setup instructions](firstPFSense.md)
3. Windows server installation ISO on your PC

# Instructions Index
We will be doing the following:
1. [Configure PFSense interface](#configure-pfsense-interface)
    - [Interface Settings](#interface-settings)
    - [Firewall Rules](#firewall-rules)
    - [DHCP Config](#dhcp-config)
1. [Build Windows Server VM](#build-windows-server-vm)
    - [Upload .iso](#upload-windows-server-iso-to-esxi)
    - [Create Virtual Machine](#create-virtual-machine)
2. [Install & Configure Windows Server](#install--configure-windows-server)
    - [Install Windows Server](#install-windows-server-to-vm)
    - [Install Services](#install-services)
    - [Configure Networking](#configure-networking)
    - [Configure DHCP server](#configure-dhcp-server)
    - [Configure IIS server](#configure-iis-server)
5. [Configure PFsense firewall rules](#configure-pfsense-firewall-rules)
    - [Goals](#goals)
    - [Browse to firewall rules](#browse-to-firewall-rules-page)
    - [LAN3 rule](#lan3-rule)
    - [LAN rules](#lan-rules)
6. [Testing the  network](#testing-the-network)
    - [LAN to LAN3 - HTTP(s)](#lan-to-lan3---https-traffic-passed)
    - [LAN to LAN3 - block other traffic](#lan-to-lan3---other-ports-blocked)
    - [LAN3 to WAN - pass](#lan3-to-wan)
    - [LAN3 to internal IP - block](#lan3-to-other-lans-blocked)
    - [Windows DHCP Server](#dhcp)


# Configure PFSense interface
- ## Interface settings
    LAN3 should be unused so far, but we are going to quickly make sure everything is ready on the PFSense side before we build our Windows Server

    1. browse to the IP address / hostname of your PFSense VM
    2. on the top menu bar, Interfaces > LAN3 ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso48.jpg))
    1. Options
        - Enable interface
        - Static IPv4
            - *Devices on this subnet attempting to send packets outside of the subnet will need the IP of the gateway (router). Since PFSense will be the router, we are setting it's IP here.*
        - Set static IP and subnet (I went with 10.0.3.0/24)
        - Click 'save'
        ![screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso46.jpg)
    1. Click 'apply' to save interface configuration ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso47.jpg))
- ## Firewall rules
    1. Firewall > rules ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso49.jpg))
    1. Click 'LAN3' ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso50.jpg))
    1. You should see no rules under LAN3. If you created any, delete them now ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso51.jpg))
    1. we will be creating firewall rules once the windows server is up and running
- ## DHCP config
    1. Services > DHCP Server ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso52.jpg))
    1. Click 'LAN3' ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso53.jpg))
    1. Make sure 'Enable DHCP server on LAN3 interface' is UNCHECKED. Scroll down and click 'Save' ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso54.jpg))


# Build Windows server VM
- ## upload Windows Server ISO to ESXI

    1. Browse to the ESXI server IP, and click on 'storage' > datastore browser ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso1.jpg))

    2. Browse to the folder where you are storing your ISOs, and click upload. Select your copy of Windows Server ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso2.jpg))

    3. You will see the upload progress in the datastore browser ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso3.jpg))

    4. and you will see the file when it has uploaded successfully ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso4.jpg))

- ## Create Virtual Machine
    1. Virtual machines > create / register vm ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso0.jpg))
    2. create a new virtual machine,  next ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso5.jpg))
    3. Select name and guest os
        - Name: your choice
        - Guest OS family: Windows
        - Guest OS Version: Windows server 2016
        - Click 'Next' 
        
            ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso6.jpg))
    4. Select the datastore for the VM's virtual hard drive, click 'Next' ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso7.jpg))
    5. customize settings screen:
        - CPU- 16
        - memory- 16GB (16384 MB)
        - hard disk 1- 100 GB
        - network adapter 1- LAN3
        - CD/DVD Drive 1- Datastore ISO file (select the windows server .iso you uploaded) ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso10.jpg)) 
        - click 'Next 
        
            ![screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso8.jpg)
    6. Review details, click 'Finish' ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso9.jpg))
    7. after a short delay you will see the new VM under Virtual Machines. Click the VM name to open it's configuration page ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso11.jpg))
    8. click the console preview window to start the VM ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso12.jpg))

# Install & Configure Windows Server
- ## Install windows server to VM
    1. windows server should boot (screenshots [1](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso13.jpg) [2](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso14.jpg))
    2. change language, time, and keyboard settings if needed, click 'Next' ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso15.jpg))
    3. clcik 'Install now' ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso16.jpg))
    4. setup will take a moment to start ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso17.jpg))
    5. select the 'standard evaluation - desktop experience' OS, click 'Next' ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso18.jpg)) 
    6. After a moment, you will see an 'Applicable notices and license terms' screen. Check the 'I accept the license terms' box, click 'Next' ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso19.jpg))
    7. Click 'Custom: install windows only (advanced)' ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso20.jpg))
    8. Click 'Next' ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso21.jpg))
    9. Windows should install ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso22.jpg))
    10. after installation, windows should boot (screenshots [1](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso23.jpg) [2](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso24.jpg))
    9. Set admin password, click 'Finish' ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso25.jpg))
- ## Install services
    1. You need to press Ctrl + Alt + Delete on the guest (windows server) in order to log in. To do this, Right Click the top bar of the VM console window, then Guest os > send keys > Ctrl-Atl-Delete ([screenshot](https://github.com/mynah22/Homelab-Guides/ raw/main/screenshots/firstConfig/iso26.jpg))
    9. Type the administrator password you set above, ENTER to log in ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso27.jpg))
    9. It will take a moment for Windows Server to load. Server Manager will be automatically launched, and you will be presented with a pop-up informing you about Windows Admin Center. Check 'Don't show this message again' and click X to close the pop up  (screenshots [1](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso28.jpg) [2](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso29.jpg) [3](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso30.jpg))
    9. click 'add roles and features' (it may take a minute for this to be available) ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso31.jpg))
    10. click 'next' on the 'Before you begin' screen ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso32.jpg))
    10. click 'next' to proceed with role-based installation ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso33.jpg))
    10. click 'next' to install to this windows server instance ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso34.jpg))
    10. click the checkbox next to the DHCP server role, then click 'add features' (screenshots [1](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso35.jpg) [2](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso36.jpg))
    10. click the checkbox next to the Web Server (IIS) role, then click 'add features' (screenshots [1](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso57.jpg) [2](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso58.jpg)
    11. click 'Next' 
        ![screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso37.jpg)
    12. click 'Next' without adding any features ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso38.jpg))
    13. click 'Next' on the DHCP server screen ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso39.jpg))
    13. click 'Next' on the Web Server (IIS) screen ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso60.jpg))
    14. click 'Next' on the Role Services screen ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso61.jpg))
    13. click 'Install' on the confirmation screen. click 'Close' after installation completes (screenshots [1](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso40.jpg) [2](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso41.jpg) [3](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso42.jpg))
- ## Configure Networking
    We are going to set a static IP for the windows server on the same subnet that we configured for PFSense above

    1. right click start menu > network connections (screenshots [1](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso62.jpg)) [2](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso63.jpg)))
    2. click 'change adapter options'. Then, right click the Ethernet network and click 'Properties' (screenshots [1](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso65.jpg) [2](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso64.jpg)) 
    1. click 'Internet Protocol Version 4', then 'Properties' ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso66.jpg))
    1. IPV4 options ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso67.jpg))
        - Select the 'use the following IP address' radio button
        - IP address: this will be the IP of the DNS, DHCP, and IIS server. It must be on the same subnet as the PFSense IP you configured for this interface
        - Subnet mask: must match the subnet of the PFSense IP
        - DNS servers: 127.0.0.1, 8.8.8.8
        - Click 'OK'
    1. Close windows. You may see a pop up asking if you want to be discoverable on this network - click 'Yes' (screenshots [1](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso68.jpg) [2](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso68.jpg))
- ## Configure DHCP server

    1. In the Server Manager window, click the flag icon, then 'complete DHCP configuration' ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso43.jpg))
    2. Commit, close (screenshots [1](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso44.jpg) [2](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso45.jpg))
    3. under 'Roles and Server Groups', click 'DHCP' ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso70.jpg))
    4. Right click your server name, then DHCP Manager ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso71.jpg))
    4. Clicking your server will reveal IPv4 - right click, add new scope([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso73.jpg))
    4. Click 'Next' to begin wizard ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso72.jpg))
    4. Type a name, click 'Next' to continue ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso74.jpg))
    5. Enter the IP range you would like the DHCP to assign to,  and subnet settings ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso75.jpg))
        - *Note: PFSense (the gateway) is on 10.0.3.1, and this server is on 10.0.3.2, so my range starts at 10.0.3.3, and I've given it 100 IPs*
    5. We are not configuring any exclusions or a delay. Click 'Next' ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso76.jpg))
    5. Leave lease duration at default. Click 'Next' ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso77.jpg))
    5. Leave 'Yes I want to configure these options now' selected. Click 'Next' ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso78.jpg))
    2. Type the IP of PFSense on LAN3, click Add. Click Next (screenshots [1](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso79.jpg) [2](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso80.jpg))
    5. type a private domain name (NOT the same domain as your home network or pfsense domain). Click Next([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso81.jpg))
    5. Leave WINS servers empty, click next' ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso82.jpg))
    5. click 'next' to activate scope ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso83.jpg))
    5. click 'finish' ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso84.jpg))
    5. You will see your new scope under IPv4 in DHCP manager
    
        ![screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso85.jpg)

- ## Configure IIS server
    1. Default settings for IIS will work fine for this demo

# Configure PFSense firewall rules
- ## Goals
    We are attempting to set up the following:
    - LAN can communicate with LAN3, but only on port 80 and 443
    - LAN3 can communicate with WAN, but not any internal resources
- ## Browse to firewall rules page
    1. browse to the ip / hostname of your pfsense VM and log in
    1. Firewall > rules ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso49.jpg))
- ## LAN3 rule
    1. Click 'LAN3' ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso50.jpg))

        Click to add a rule to the top ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso100.jpg))

    1. LAN3 to WAN rule ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso101.jpg))

        - Action: Pass
        - Interface: LAN3
        - Address Family: IPv4
        - Protocol: Any
        - Source: LAN3 net
        - Destination: invert match. Network 10.0.0.0/8
            - *Meaning, pass all traffic from LAN3 that is not going to a 10.0.0.0/8 address. Since all of our internal subnets are within 10.0.0.0/8 , this prevents traffic from LAN3 to going to any other internal networks, but does allow WAN connections to be established from LAN3*
        - Click Save
    1. You should have one rule on LAN3. Click 'Apply Changes' ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso111.jpg))

- ## LAN rules

    1. Click 'LAN' ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso108.jpg))

        The order of these rules matters greatly! 

        Click to add a rule to the **top** ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso100.jpg)) for each of the following:
    2. LAN to LAN3 block rule ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso109.jpg))
        - Action: Block
        - Interface: LAN
        - Address Family: IPv4
        - Protocol: Any
        - Source: LAN net
        - Destination: LAN3 net
        - Click Save
    2. LAN to LAN3 HTTP rule 

        ![screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso102.jpg)
        - Action: Pass
        - Interface: LAN
        - Address Family: IPv4
        - Protocol: TCP/UDP
        - Source: LAN net
        - Destination: LAN3 net
        - Destination port range: HTTP (80) (pick from drop down)
        - Click Save
    1. LAN to LAN3 HTTPS rule ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso103.jpg))
        - Click 'LAN' ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso108.jpg))
        - Action: Pass
        - Interface: LAN
        - Address Family: IPv4
        - Protocol: TCP/UDP
        - Source: LAN address
        - Destination: LAN3 net
        - Destination port range: HTTPS (443) (pick from drop down)
        - Click Save
    1. You should have three new rules set on LAN. MAke sure they are in this order, click 'apply changes' ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso110.jpg))

# Testing the network
- ## LAN to LAN3 - HTTP(S) traffic passed
    We are hoping to be able to send port 80 and 443 traffic from LAN to LAN3, lets give it a try
    1. Ensure PC is plugged into the PFSense LAN interface
    2. your IP configuration should have details matching the configuration of the PFSense DHCP server on LAN  ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso105.jpg))
    3. browse to the static IP you assigned to the windows server (10.0.3.2 in my example) - you should see the default windows server page! congrats, LAN devices can open HTTP(S) connections to LAN3 ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso106.jpg))
- ## LAN to LAN3 - other ports blocked
    We know that port 80 and 443 are being passed to LAN3, but let's make sure that no other ports are working
    1. With your PC still on LAN, ping the IP of the windows server - the ping packets should time out ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso107.jpg))
    2. This is because icmp echo packets (the packets sent when you 'ping' are) not using TCP/UDP port 80 or 443, so they do not match our first two LAN firewall rule, but do match the third one which blocks the packets
- ## LAN3 to WAN
    We would like the LAN3 devices to communicate with the internet. Let's make sure our firewall rules allow this
    1. Go back to ESXI and open your windows server console (screenshots [1](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso112.jpg) [2](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso113.jpg))
    4. To test your WAN connection on the windows server VM, open powershell and ping a public IP (like 1.1.1.1), or open internet explorer and browse to a webpage ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso114.jpg))

- ## LAN3 to other LANs blocked
    We do not want LAN3 to be able to open communications with other internal networks. 
    1. Your PC should still be plugged into LAN - use powershell on the windows server vm to ping the IP of your PC - requests should all time out, as they are being blocked by the PFSense firewall ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso115.jpg))

- ## DHCP 
    now that we know traffic is being routed as desired, let's make sure the windows DHCP server is working
    1. Plug your PC into LAN3. After a short while it should obtain DHCP configuration from your windows server ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso115.jpg))
    2. Plug your PC back into a network where you can connect to your ESXI managment webpage (LAN), browse to the ESXI server iP,  and open the windows server VM console
    3. Open DHCP Manager ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso116.jpg))
    4. you should see the lease of your PC under your server name> IPv4> the scope you created> address leases
        ![screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso117.jpg)