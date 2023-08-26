# creating a DNS Blackhole on PFSense 

This is the first thing I do after installing PFSense - it only takes a couple of clicks!

A DNS blackhole is used to block connections to ads, trackers, and malware for all devices on a network

I'll write an explanation of DNS blackholes later, and link it here

# Requirements
1. PFSense installed
2. Able to connect to PFSense webgui
1. PFSense has a WAN connection (can communicate with the internet)

# Index
1. [Install pfBlockerNG]()
2. [Managing pfBlockerNG]()
    - [Disable pfBlockerNG](#disable-pfblockerng)
    - [Create blocklist exceptions](#add-temporary-and-permanent-exceptions)


# Install PFBlockerNG
1. in the pfsense webgui, click System > Package manager ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/dnsb0.jpg))
2. Click 'available Packages' - PFsense will load them shortly. Type 'pfblockerng' in the search bar. Click 'Search'. 
3. Click install on the entry for ***pfBlockerNG-devel***, not the non-devel package ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/dnsb1.jpg))
4. Click to confirm installation. You will see a success notice when installation is complete (screenshots [1](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/dnsb2.jpg) [2](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/dnsb3.jpg))
5. in the top menu of pfSense, click Firewall > pfBlockerNG ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/dnsb5.jpg))
6. An easy configuration wizard will run (screenshots [1](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/dnsb6.jpg) [2](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/dnsb7.jpg) [3](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/dnsb8.jpg) [4](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/dnsb9.jpg) [5](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/dnsb10.jpg))
7. After you click 'Finish', pfblockerng will pull the blocklist definitions. You will see a blue success screen when everything is finalized (screenshots [1](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/dnsb11.jpg) [2](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/dnsb12.jpg))

# Managing pfBlockerNG
Occasionally, pfBlockerNG will cause issues communicating with a site / service 

If you are troubleshooting a network / connectivity issue, you may want to eliminate pfBlockerNG as the cause. 

1. ## Disable pfBlockerNG
    - The simplest technique to determine if pfBlockerNG is the source is to disable it. This can be helpful if you are in a hurry, or want to be *really* sure your issue is not due to pfBlockerNG. Obviously, this is not a permanent solution. 
    - Firewall > pfBlockerNG ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/dnsb13.jpg))
    - Uncheck 'enable', save ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/dnsb14.jpg))
2.  ## Add temporary and permanent exceptions

    Using the below method, you can identify which blocked domains / IPs are causing issues, and then add permanent exceptions to the dns and IP blocklists - allowing the site or application that was having issues to function normally. 
    - Firewall > pfBlockerNG ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/dnsb13.jpg))
    - Click 'Reports'. You will see IP blocks under 'Block', and DNS blocks under 'DNSBL Block'. the DNS blocks are almost certainly what you will be looking at
        ![](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/dnsb15.jpg)
    - If you click the padlock icon, you can temporarily prevent that domain from being blocked
    - If you click the '+' icon, you will ***permanently*** prevent that domain from being blocked
    - a standard workflow looks like this:
        1. Try and reduce blocklist noise - disconnect / refrain from using other devices on interfaces managed by pfblockerng
        2. Use the device / service that is having issues. Try several times to get more entries on the block report
        3. open up the dnsblocklist report in pfsense
        3. The first few entries should be related to your issue. Click on the padlocks to create temporary exceptions until the service starts working
        4. Once the connectivity issue has been resolved, remove each temporary exception one by one, and test the service again:
            - If the service still works, move to the next exception
            - if removing the temp exception breaks the service, then click '+' to create a permanent exception