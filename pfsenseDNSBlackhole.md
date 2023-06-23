# creating a DNS Blackhole on PFSense 

This is the first thing I do after installing PFSense - it only takes a couple of clicks!

A DNS blackhole is used to block connections to ads, trackers, and malware for all devices on a network

I'll write an explaination of DNS blackholes later, and link it here

# Requirements
1. PFSense installed
2. Able to connect to PFSense webgui
1. PFSense has a WAN connection (can communicate with the internet)

# Index
1. [Install PFBlockerNG]()
2. [How to ID & whitelist blocks]()

# Install PFBlockerNG
1. in the pfsense webgui, click System > Package manager [screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/dnsb0.jpg)
2. Click 'available Packages' - PFsense will load them shortly. Type 'pfblockerng' in the search bar. Click 'Search'. 
3. Click install on the entry for ***pfBlockerNG-devel***, not the non-devel package 

    ![screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/dnsb1.jpg)
4. Click to confirm installation. You will see a success notice when installation is complete (screenshots [1](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/dnsb2.jpg) [2](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/dnsb3.jpg))
5. in the top menu of pfSense, click Firewall > pfBlockerNG [screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/dnsb5.jpg)