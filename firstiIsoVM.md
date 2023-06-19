# Windows server VM on isolated network
This demonstration uses ESXI and PFSense to configure a windows server VM on an isolated network. A homelab is usually primarily built as a learning environnement, so you may want to play with networks that do not use PFSense

The idea here is to show how you can add network segments that are managed by non-pfsense systems. Access between the windows segment and the other interfaces can be managed using some simple firewall rules

We are going to use windows server as the DHCP server on LAN3

We are also going to deploy an IIS webserver, and create some firewall rules to control access from other interfaces
        We will set up a windows server instance on LAN3

        We will then set up DHCP and an IIS webserver on the Windows Server, and create firewall rules allowing only port 80 and 443 traffic from LAN2.

        We will then plug a device into LAN3. We will demonstrate the the windows DHCP server server, and demonstrate the connectivity to other subnets

        We will then connect to the IIS webserver 

## Requirements
1. Completed the [Server & Hypervisor Setup Instructions](firstConfig.md) 
2. Completed the [PFSense Setup instructions](firstPFSense.md)
3. Windows server ISO on your PC

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
        - CD/DVD Drive 1- Datastore ISO file ([screenshot]((https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso10))
        - click 'Next ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso8.jpg))
    6. Review details, click 'Finish' ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso9.jpg))
    7. after a short delay you will see the new VM under Virtual Machines. Click the VM name to open it's configuration page ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso11.jpg))
    8. click the console preview window to start the VM ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso12.jpg))

- ### **Install Windows Server**
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
- ### **Set up windows services**
    1. You need to press Ctrl + Alt + Delete on the guest (windows server) in order to log in. To do this, Right Click the top bar of the VM console window, then Guest os > send keys > Ctrl-Atl-Delete ([screenshot](https://github.com/mynah22/Homelab-Guides/ raw/main/screenshots/firstConfig/iso26.jpg))
    9. Type the administrator password you set above, ENTER to log in ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso27.jpg))
    9. It will take a moment for Windows Server to load. Server Manager will be automatically launched, and you will be presented with a pop-up informing you about Windows Admin Center. Check 'Don't show this message again' and click X to close the pop up  (screenshots [1](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso28.jpg) [2](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso29.jpg) [3](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso30.jpg))
    9. click 'add roles and features' ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso31.jpg))
    10. click 'next' on the 'Before you begin' screen ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso32.jpg))
    10. click 'next' to proceed with role-based installation ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso33.jpg))
    10. click 'next' to install to this windows server instance ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso34.jpg))
    10. click the checkbox next to the DHCP server role, then click 'add features' (screenshots [1](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso35.jpg) [2](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso36.jpg))
    11. click 'Next' [screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso37.jpg)
    12. click 'Next' without adding any features ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso38.jpg))
    13. click 'Next' on the DHCP server screen ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso39.jpg))
    13. click 'Install' on the confirmation screen ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso40.jpg))
    13. click 'Install' on the confirmation screen. click 'Close' after installation completes (screenshots [1](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso40.jpg) [2](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso41.jpg) [3](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso42.jpg))
    14. Click the flag icon, then 'complete DHCP configuration' ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso43.jpg))
    15. Commit, close (screenshots [1](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso44.jpg)) [2](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso45.jpg)))
