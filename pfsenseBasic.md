# Install and configure PFSense
- ## Article type: how-to
    This guide is a how-to guide for the basic installation of pfSense

    it does not cover specific installation stituations (bare metal, virtualized, command line)

# Requirements
1. Able to boot to pfsense on target
2. Sufficient resources on target

# Instructions Index
This guide will cover:
- [Install and configure PFSense](#install-and-configure-pfsense)
- [Requirements](#requirements)
- [Instructions Index](#instructions-index)
- [Configure PFSense via terminal](#configure-pfsense-via-terminal)
- [Finish pfsense config via webgui](#finish-pfsense-config-via-webgui)

# Configure PFSense via terminal

- ## Installation
The following steps are a generic example of the initial installation process starting after booting to pfSense. Command line installation uses a TUI that looks very much like the GUI in the screen shots below - steps are the same
1. ENTER to accept the agreement ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs0.jpg))
2. ENTER to install PFSense ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs1.jpg))
3. ENTER to continue with default keymap ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs2.jpg))
4. ENTER for Auto (ZFS) disk config ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs3.jpg))
5. ENTER to confirm disk config ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs4.jpg))
6. ENTER to proceed with ZFS in stripe mode ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs5.jpg))
7. select appropriate disk, ENTER to proceed ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs6.jpg))
8. LEFT, ENTER to acknowledge disk overwrite ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs7.jpg))
9. Installation will commence (screenshots [1](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs8_2.jpg) [2](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs8.jpg))
10. When installation is complete, you will be at [this screen](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs9.jpg). ENTER to skip a manual shell
11. ENTER again to reboot. This may take a couple of minutes ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs10.jpg))

PFSense has been installed and is ready to be configured!


- ## Assign interfaces
    1. Type '1' at the PFSense main menu ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs16.jpg)), press ENTER
        - Option 1 ('assign interfaces'), is used to assign the role of the network interfaces that PFSense sees. One of PFSense's primary functions is as a NAT router - to perform that function, it has to know which port is designated for the WAN connection, and which port(s) are on the 'inside' of the NAT (LAN ports)
    2. you should see a screen like [this one](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs28.jpg). '1' in the PFSense console main menu
    3. You can choose to set up VLANs here. You can always do so later (it's a little easier in the webgui).([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs34.jpg)). Make your selection, then ENTER
    4. type the interface name that corresponds to the WAN port, ENTER to assign that interface as WAN in PFSense ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs30.jpg))
    5. type the interface name that corresponds to the LAN port group, ENTER to assign as LAN in PFSense ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs31.jpg))
    6. You could assign optional interfaces (LAN2-LAN6) here, but it's easier in the PFSense webgui - ENTER to skip ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs32.jpg))
    7. type 'y', then ENTER to confirm assignments ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs33.jpg))
    8. PFSense will take a little while to apply the interface settings. You will then return to the console main menu
- ## Change LAN IP Configuration
    - At the PFSense console main menu, '2' and ENTER ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs35.jpg))
    - if asked if you want to use DHCP to pick the LAN IP, type 'n', then press ENTER 
      - There are a few IP ranges that are reserved for 'private' networks (inside at NAT router, not internet-facing IPs):
        - 192.168.0.0/16
        - 172.16.0.0/12
        - 10.0.0.0/8

    - '2' for LAN, ENTER ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs36.jpg))
    - type the ip address ***you want PFSense to have***. ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs38.jpg)). **Write this IP down - you will need it to connect to the PFSense webconfigurator**. ENTER
    - enter the bitwise subnet mask - 24 in our case ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs29.jpg)). ENTER
    - We are configuring a LAN - ENTER for no upstream gateway ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs40.jpg))
    - if asked if you want to use DHC6P to pick the LAN IPv6 address, type 'n', then press ENTER 
    - ENTER for no ipv6 address ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs41.jpg))
    - type 'y' if you wish to enable DHCP on the LAN interface ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs42.jpg)). ENTER
    - The PFSense DHCP server we just enabled will assign IPs in a fixed range (must be within subnet scope). Enter the beginning of that range,  ENTER ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs43.jpg))
    - Enter the end of the DHCP range ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs44.jpg)), ENTER
    - type 'n' then ENTER to keep webconfigurator connections encrypted (you will get a certificate error in your browser) ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs45.jpg)) 
    - Things will reload and you will see the new ip assignments ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs46.jpg)). Press ENTER to return to the PFSense console main menu

# Finish pfsense config via webgui

With the above network changes, you should be able to use a browser on your PC to connect to the PFSense webgui

- connect the port corresponding to LAN in pfSense to your PC 

-  Click through the [Certificate Error](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/certError.jpg)

    *when you browse to the PFSense webgui, you will be presented with a certificate error. This is because the server is using a self-signed certificate - it is still safely encrypting your communication with the server, but it's identity is not vouched for by any authorities trusted by your machine.*

- Whaddup PFSense Webgui logon! 

    default credentials:
    - Username: admin 
    - Password: pfsense 

    ![screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs47.jpg)

- ## **PFSense setup wizard**
    1. click 'Next' on the [first screen](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs48.jpg)
    2. click 'Next' on the [support page](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs49.jpg)
    3. **General information page**
        - Hostname: the hostname that PFSense will respond to. I like the default 'pfSense'
        - Domain: the domain of the internal network. If you own a Domain Name, then it should the domain.TLD (ie google.com). Otherwise the only rules are:
            - do not use a domain that resolves on WAN (don't use ebay.com)
            - do not use a domain with .local as the TLD
            - apparently these are the recommended TLDs to use for an internal network. I've included them with an example domain name:
                - .home 
                  - yourDomainName.home 
                  - homelab.home
                - .lan 
                  - yourDomainName.lan
                  - joebob.lan
                - .private 
                  - yourDomainName.private
                  - testnetwork.private
                - .internal 
                  - yourDomainName.internal
                  - agreatdomain.internal
        - Primary / Secondary DNS Servers

            - *note: by default, PFSense will use 127.0.0.1 (ie the loopback IP - using the PFSense server for DNS lookups) first, THEN use the primary / secondary DNS server. There is no need to set the primary to 127.0.0.1 - the local DNS server where cached lookups, Host / Domain Overrides & Aliases are kept is always consulted first, THEN the remote DNS servers you define as primary / secondary here. DNS Blackhole works because it actually resolves an IP address (that does not respond) - if it did not resolve an IP then it would defer to the DNS servers defined here.* 
            * Set the primary / secondary DNS as you like - I like to steer clear of my ISPs DNS, and use alternates (common ones are the google, opendns, and cloudflare dns services like 8.8.8.8, 4.4.4.4 & 1.1.1.1). [This tool](https://grc.com/dns/benchmark.htm) can determine the fastest DNS servers for your network if you are serious about finding the server with the best performance. If you care about DNS security then you probably already have your preferences and I will not expound further. 
        - uncheck 'override DNS' 
        - click 'Next'

        ![](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs50.jpg)
    4. Set your timezone as appropriate. Leave the ntp server alone unless you have a preference. ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs51.jpg))
    5. On the WAN config page, leave everything at default (DHCP)
       - Unless you are installing pfSense inside a private network (ie double NAT)  
       - uncheck the Block RFC1918 checkbox (screenshots [1](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs52.jpg) [2](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs53.jpg))
        - *for double NATting addressing to work, **This box must be unchecked**. you will want to recheck it if you move PFSense to the edge of your network later*
    6. We already configured the LAN port IP, click 'Next' ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs54.jpg))
    7. set a new admin password for pfsense ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs55.jpg))
    8. click 'reload' to load configuration ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs56.jpg))
    9.  the system will reload, and you will be presented with a success screen. click 'Finish' (screenshots 
    [1](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs57.jpg)
    [2](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs58.jpg))
    10. click 'Accept' on the copyright notice ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs60.jpg))
    11. click 'close' on thank you ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs61.jpg))'
    12. On the main page, under interfaces, you should see a WAN IP (your ISP if on edge, or home network subnet if double NATting), and a LAN ip matching what you set the PFSense lan IP to ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs62.jpg))
    13. if all of that checks out, your PC should be able to access internet resources - try browsing to an internet website on your PC

- ## **Rename OPT interfaces**

1. In PFSense webgui: Interfaces> assignments ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs63.jpg))
2. Click 'Add' to add the remaining interfaces. This will create all available interfaces and name them OPT1, OPT2, etc. Click 'Save'  (screenshots 
[1](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs67.jpg)
[2](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs68.jpg)) 
3. Rename and enable interfaces:

    I like to have my interfaces named LAN2, LAN3 etc.

    Click through all of the OPT interfaces, taking the following steps:
    
    1. click the OPT interface name ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs64.jpg))
    2. check 'enable interface'.  change description to new name (ie LAN2) ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs65.jpg))
    3. click 'save' , then apply changes ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs66.jpg))
    4. Interfaces > assignments and click the next OPT interface
    5. repeat the above steps for all OPT interfaces
    6. you should end up with all OPT interfaces renamed ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs69.jpg))

- ## interface management
    - for further details on configuring interfaces, including DHCP, firewall rules, and see the [interface setup guide]()
    - for notes on the services I manage with pfSense, see [this](/pfSenseMyUsage.md) document