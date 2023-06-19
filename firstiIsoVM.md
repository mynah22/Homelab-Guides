# Windows server VM on isolated network 
When it comes to managing the core of your network, PFSense is an excellent choice. It's simple to configure, easy to understand, and does everything you'll need.

However, in a laboratory environment, you might want to experiment with different network stacks and technologies. This guide focuses on deploying a Windows Server in a way that allows it to provide network services (such as DHCP and DNS), while still being managed by the firewall within your PFSense network.

We are also building a IIS webserver on Windows Server to help demonstrate the firewall rules

*It's important to note that isolating Windows Server it in this manner is **Not Required** (you could just place it on LAN). The example provided here specifically addresses scenarios where the server is responsible for services that are typically handled by PFSense (such as DHCP and DNS).*

We will be doing the following:
1. Configure PFSense interface
1. Build a Windows VM
2. Install Windows Server
3. Enable Windows Services (DHCP, DNS, & IIS/webserver)
5. Configure PFsense firewall rules
6. Test network


## Requirements
1. Completed the [Server & Hypervisor Setup Instructions](firstConfig.md) 
2. Completed the [PFSense Setup instructions](firstPFSense.md)
3. Windows server ISO on your PC

## **Configure PFSense interface**
- ### Interface settings
    LAN3 should be unused so far, but we are going to quickly make sure everything is ready on the PFSense side before we build our Windows Server

    1. browse to the IP address / hostname of your PFSense VM
    2. on the top menu bar, Interfaces > LAN3 ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso48.jpg)
    1. Options  ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso46.jpg))
        - Enable interface
        - Static IPv4
            - *Devices on this subnet attempting to send packets outside of the subnet will need the IP of the gateway (router). Since PFSense will be the router, we are setting it's IP here.*
        - Set static IP and subnet (I went with 10.0.3.0/24)
        - Click 'save'
    1. Click 'apply' to save interface configuration ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso47.jpg))
- ### Firewall rules
    1. Firewall > rules ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso49.jpg))
    1. Click 'LAN3' ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso50.jpg))
    1. You should see no rules under LAN3. If you created any, delete them now ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso51.jpg))
    1. we will be creating firewall rules once the windows server is up and running
- ### DHCP config
    1. Services > DHCP Server ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso52.jpg))
    1. Click 'LAN3' ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso53.jpg))
    1. Make sure 'Enable DHCP server on LAN3 interface' is UNCHECKED. Scroll down and click 'Save' ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso54.jpg))


## **Build Windows server VM**
- ### **upload Windows Server ISO to ESXI** 

    1. Browse to the ESXI server IP, and click on 'storage' > datastore browser ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso1.jpg))

    2. Browse to the folder where you are storing your ISOs, and click upload. Select your copy of Windows Server ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso2.jpg))

    3. You will see the upload progress in the datastore browser ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso3.jpg))

    4. and you will see the file when it has uploaded successfully ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso4.jpg))

- ### **Create Virtual Machine**
    1. Virtual machines > create / register vm ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso0.jpg))
    2. create a new virtual machine ? next ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso5.jpg))
    3. Name- your choice. Guest OS family- Windows. Guest OS Version- Windows server 2016. Click 'Next' ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso6.jpg))
    4. Select the datastore for the VM's virtual hard drive, click 'Next' ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso7.jpg))
    5. customize settings screen:
        - CPU- 16
        - memory- 16MB (16384 MB)
        - hard disk 1- 100 GB
        - network adapter 1- LAN3
        - CD/DVD Drive 1- Datastore ISO file ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso10))
        - click 'Next ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso8.jpg))
    6. Review details, click 'Finish' ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso9.jpg))
    7. after a short delay you will see the new VM under Virtual Machines. Click the VM name to open it's configuration page ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso11.jpg))
    8. click the console preview window to start the VM ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso12.jpg))

## **Install & Configure Windows Server**
- ### Install windows server to VM
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
- ### Install services
    1. You need to press Ctrl + Alt + Delete on the guest (windows server) in order to log in. To do this, Right Click the top bar of the VM console window, then Guest os > send keys > Ctrl-Atl-Delete ([screenshot](https://github.com/mynah22/Homelab-Guides/ raw/main/screenshots/firstConfig/iso26.jpg))
    9. Type the administrator password you set above, ENTER to log in ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso27.jpg))
    9. It will take a moment for Windows Server to load. Server Manager will be automatically launched, and you will be presented with a pop-up informing you about Windows Admin Center. Check 'Don't show this message again' and click X to close the pop up  (screenshots [1](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso28.jpg) [2](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso29.jpg) [3](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso30.jpg))
    9. click 'add roles and features' (it may take a minute for this to be available) ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso31.jpg))
    10. click 'next' on the 'Before you begin' screen ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso32.jpg))
    10. click 'next' to proceed with role-based installation ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso33.jpg))
    10. click 'next' to install to this windows server instance ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso34.jpg))
    10. click the checkbox next to the DHCP server role, then click 'add features' (screenshots [1](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso35.jpg) [2](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso36.jpg))
    10. click the checkbox next to the DNS server role, then click 'add features' (screenshots [1](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso55.jpg) [2](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso56.jpg))
    10. click the checkbox next to the Web Server (IIS) role, then click 'add features' (screenshots [1](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso57.jpg) [2](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso58.jpg)
    11. click 'Next' [screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso37.jpg)
    12. click 'Next' without adding any features ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso38.jpg))
    13. click 'Next' on the DHCP server screen ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso39.jpg))
    13. click 'Next' on the DNS server screen ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso59.jpg))
    13. click 'Next' on the Web Server (IIS) screen ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso60.jpg))
    14. click 'Next' on the Role Services screen ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso61.jpg))
    13. click 'Install' on the confirmation screen. click 'Close' after installation completes (screenshots [1](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso40.jpg) [2](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso41.jpg) [3](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso42.jpg))
- ### Configure Networking
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
- ### Configure DHCP server

    1. In the Server Manager window, click the flag icon, then 'complete DHCP configuration' ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso43.jpg))
    2. Commit, close (screenshots [1](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso44.jpg) [2](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso45.jpg))
    3. under 'Roles and Server Groups', click 'DHCP' ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso70.jpg))
    4. Right click your server name, then DHCP Manager ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso71.jpg))
    4. Clicking your server will reveal IPv4 - right click, add new scope([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso73.jpg))
    4. Click 'Next' to begin wizard ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso72.jpg))
    4. Type a name, click 'Next' to continue ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso74.jpg))
    5. Enter the IP range you would like the DHCP to assign to,  and subnet settings ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso75.jpg))
    - *Note: PFSense (the gateway) is on 10.0.3.1, and this server is on 10.0.3.2, so mny range starts at 10.0.3.3, and I've given it 100 IPs*
    5. We are not configuring any exclusions or a delay. Click 'Next' ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso76.jpg))
    5. Leave lease duration at default. Click 'Next' ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso77.jpg))
    5. Leave 'Yes I want to configure these options now' selected. Click 'Next' ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso78.jpg))
    5. Leave 'Yes I want to configure these options now' selected. Click 'Next' ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso78.jpg))
    2. Type the IP of PFSense on LAN3, click Add. Click Next (screenshots [1](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso79.jpg) [2](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso80.jpg))
    5. type a domain name in (not really required, useful for demonstration later). Click Next([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso81.jpg))
    5. Leave WINS servers empty, click next' ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso82.jpg))
    5. click 'next' to activate scope ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso83.jpg))
    5. click 'finish' ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso84.jpg))
    5. You will see your new scope under IPv4 in DHCP manager ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso82.jpg))

- ### Configure DNS server





    






