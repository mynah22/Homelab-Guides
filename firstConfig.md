# HP DL380 G7 - configuring ESXI 

This guide runs through the steps needed to install ESXI on an HP DL380 G7 rack server

Please note the following caveats:
- *Version 6.0.0 of ESXI is the newest that will work on this server- this is an old version, and is unsupported. 6.5.0 will install, but crashes as soon as it tries to write to the filesystem*
- *The license for 6.0.0 is free, but I cannot find a way to obtain license keys or installation ISOs any longer. If you do not already have them, then you will need to either use a different hypervisor like proxmox, or a different server compatible with ESXI 7+*

# Requirements
Initial set up requires the following:

- Existing home network
    - You will need one open ethernet port in in your existing home network in order to give your laboratory network a path to the internet

- Hardware
    - HP DL380 g7 rack server
    - VGA Capable monitor
    - at least one 2.5" SATA drive
    - 2.5" SATA caddy for every drive SATA drive
    - VGA cable
    - 2-5 ethernet cables (on top of what you are currently using in your home net)
    - USB keyboard
    - power cables
    - PC with ethernet port (for configuring ESXI and PFSense)

- Installation tools
    - ESXI 6.0.0 HP custom image installed on a bootable USB
    -  A copy of the other OSes you plan to install downloaded to your PC. PFsense 2.6.0 is required

- Connections
    - Server and VGA monitor plugged into power
    - VGA Cable plugged into monitor and server
    - Bootable USB inserted into USB slot on server 
    - Keyboard plugged into server
    - At least one 2.5" SATA disk installed into caddy / slot
        1. Remove existing partitions from the drive
        1. Mount your SATA disk into a drive caddy
        1. Press the red button on the caddy to release the locking lever
        2. Insert the caddy fully into the drive bay
        3. Push the locking lever back into the caddy to lock the caddy to the drive bay.
        1. you should see status LEDs visible on the right side of the caddy 
        3. It's best to follow the above steps for all disks you plan to use - that way you do not need to get into the RAID menu to add them later

# Instructions Index
Here are the steps taken below:
1. [Initialize disks in RAID menu](#initialize-disks-in-raid-menu)
    - [RAID modes](#raid-modes-reminder )
    - [How to get to RAID menu](#how-to-get-to-the-raid-menu)
    - [RAID Main Menu](#raid-main-menu)
    - [RAID - Deleting Logical Drives](#deleting-logical-drives-in-the-raid-menu)
    - [RAID - Creating Logical Drives](#creating-logical-drives-in-the-raid-menu)
    - [Examine Logical Drives](#take-a-second-to-look-at-your-logical-drive-numbers)
    - [Setting a boot disk](#setting-a-boot-disk)
1. [Install hypervisor (ESXI 6.0.0)](#install-esxi-to-boot-disk)
    - [Install ESXI](#install-esxi-to-boot-disk)
    - [Connect to ESXI management interface](#connect-to-esxi-management-interface)
1. [ESXI Web Configuration](#esxi-web-configuration)
    - [License ESXI](#license-esxi)
    - [ESXI Network Config](#set-up-networking)
1. [Understanding port assignments / names](#understanding-port-assignments)
    - [Physical port and NIC names](#physical-ports--nic-names)
    - [Port group / vSwitch names](#port-group-names)
    - [Check your understanding](#check-your-understanding)
    - [Labelling and documentation](#labelling-and-documentation)
1. [Next steps](#next-steps)


# Initialize disk(s) in RAID menu
With everything in the Server Setup section above ready, we are ready to get started initializing our disk(s) with the RAID controller. This is required for all disks (even those not in an array with other disks)
- To get everything successfully working, you ***MUST*** enter the RAID controller menu before installing / trying to boot a hypervisor.

- ## RAID modes reminder
    RAID (Redundant Array of Inexpensive Disks) is a set of storage technologies that allow multiple disks to be used in tandem. As a reminder, here are a few common RAID modes:
    * 0 : 'Striped' - treats disks in the array as one big drive. No redundancy. This RAID controller calls everything RAID 0, even standalone disks
    * 1 : 'Mirrored' - data is written to disks identically - if one goes down the other still has everything. No increase in storage capacity
    * 10 (1+0) - A striped array is mirrored with another array. Usually described as requiring 4 disks -  Because this controller calls everything RAID 0, it describes a 2-disk RAID 1 config as RAID 1+0 
    * 5 / 50 - cool modes utilizing the XOR function to create parity drives able to recover any other drive - this server's RAID controller requires a license to set those up


- ## How to get to the RAID menu

    The RAID menu is not hard to get to if you know how, but  ***there is only a narrow window when you can enter the menu***
    1. Hold power button for about a second to turn the power on, and after a while you will be presented with a boot screen 
    2. Once you see the F9 and F11 options, **start pressing F8** - this is the ***only*** chance you have to enter the RAID menu.

        You can stop pressing F8 once the screen goes black

        ![press f8 here](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/proLiantInitialBoot.jpg)

    3. The ILO menu will load. Press DOWN, ENTER and ENTER to exit that menu 
        - (screenshots 
        [1](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/proliantILO1.jpg)
        [2](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/proliantILO2.jpg)
        [3](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/proliantILO3.jpg) )
    4. *Immediately start pressing F8* -  you must press F8 while the RAID controller loads:

        ![raid controller loading](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/proliantRaidController2.jpg) 

- ## RAID main menu

    ![](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/proliantRaidDelete1.jpg)

    The RAID menu you just entered is used to configure RAID controller on how it will handle attached physical disks
    
    We will define what physical drives make up the 'logical drives' (RAID arrays that the hypervisor will see as a single disk). 


    *Be prepared to lose all data on a drive if you change it's RAID configuration*
    
- ## Deleting logical drives in the RAID menu

    The first order of business is to delete ALL logical drives. do the following until you no longer see drives in the deletion menu:
    1. Use UP/DOWN to navigate to 'Delete Logical Drive', then ENTER to enter the submenu 
    2. F8 to delete a drive, then F3 to confirm
    (screenshots [1](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/proliantRaidDelete2.jpg)
    [2](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/proliantRaidDelete3.jpg) 
    [3](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/proliantRaidDelete5.jpg) 
    [4](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/proliantRaidDelete4.jpg) )

    3. Keep going until you [run out](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/proliantRaidDelete6.jpg)

    Now we must create logical drives so the controller properly maps physical disks to RAID arrays:

- ## Creating logical drives in the RAID menu

    To get started, enter the 'Create Logical Drive' submenu ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/proliantRaidCreate1.jpg))

    You will configure one array at at time:

    1. Use TAB, ENTER, SPACE, and UP/DOWN to configure the logical disks as needed. Here are some examples
        - Single disk ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/proliantRaidCreate3.jpg))
        - RAID 1 array (two disks mirrored - this controller calls that RAID 1 + 0 ) ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/proliantRaidCreate5.jpg))
    2. Press ENTER when you are done with the array and you will see a confirmation screen. Press F8 to save (Screenshots:
    RAID [1](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/proliantRaidCreate4.jpg)
    RAID [1+0](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/proliantRaidCreate6.jpg))
    3. Repeat until you have assigned all disks to a logical drive

 
- ## Examining logical drives

    It's a good idea to double check what logical drive number each array was assigned
    1. in [main RAID menu](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/proliantRaidDelete1.jpg), highlight 'View logical drives', hit ENTER
    2. you will be presented with a [view](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/proliantRaidCreate12.jpg) of your logical drives - write this information down
    3. if you hit ENTER while highlighting an array you will see [a screen](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/proliantRaidCreate13.jpg) displaying the physical slots each disk is inserted into. Write this info down or label it on the disk caddies
    4. ESC to return to the main RAID menu


- ## Setting a boot disk

    Last thing in the RAID menu - setting a boot drive
    
    1. in [main RAID menu]( https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/proliantRaidCreate7.jpg), highlight 'Select Boot Volume', hit ENTER
    2. on the [next screen](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/proliantRaidCreate8.jpg), highlight 'Direct Attached Storage', hit ENTER
    3. then, on [this screen](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/proliantRaidCreate9.jpg), highlight the array you would like to install your hypervisor onto, hit ENTER. Then F8 on the [confirmation screen](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/proliantRaidCreate10.jpg).
        - *note- as you can see from the screenshot, you are only given the logical drive number, not any details about it's size or physical slot. This is why it is a good idea to write that info down from the 'View Logical Drives' submenu*
    4. once you are on this [final boot select screen](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/proliantRaidCreate11.jpg), you are ready to install a hypervisor. take a second to pause before you hit F8 to reboot and make sure you have the following things ready:
        - No ethernet cables plugged into server
        - Bootable ESXI installation USB inserted into server
        
    5. Press F8 if you are ready to exit the RAID menu - otherwise press ENTER to go back to the main RAID menu (good idea to double check / write your config down). You can press ESCAPE at the [main RAID menu](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/proliantRaidDelete1.jpg) when you are ready to install the hypervisor
    6. *you do not need to come back to the RAID menu again unless you are adding / changing disk configuration* 

# Installing hypervisor (ESXI)
- When you exit the RAID menu, the server will start to attempt to boot from the various bootable hardware (CD, USB, network/PXE over ethernet and fiber, and finally the RAID controller), in the order configured in BIOS 
- I use Ventoy, so I was presented with an OS select screen (screenshots
[1](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/proliantESXIVentoy.jpg)
[2](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/proliantESXIVentoy2.jpg)) - I selected the ESXI 6.0.0 image and it began to boot. If you are using Rufus or a similar tool you should see ESXI load directly
- When ESXI boots you will be briefly shown a boot menu. It will proceed to boot the ESXI image after a short delay [screenshot]((https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/esxi600_2.jpg))
- ESXI will take a few minutes to load (screenshots 
[1](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/esxi600_3.jpg)
[2](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/esxi600_4.jpg)
[3](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/esxi600_5.jpg))

- ## Install ESXI to boot disk
    Proceed with steps in the [ESXI Installation Guide]() - it's pretty simple:
    1. ENTER to begin install([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/esxi600_6.jpg))
    2. F11 to accept EULA (screenshots 
    [1](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/esxi600_7.jpg)
    [2](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/esxi600_8.jpg))
    3. Select the disk you would like to install to using arrow keys, then press ENTER - *this has to be the logical disk you set as the boot disk in the RAID menu.* ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/esxi600_Disks.jpg))
    4. Select your keyboard layout, ENTER ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/esxi600_10.jpg))
    5. Set a password, ENTER (screenshots
    [1](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/esxi600_11.jpg)
    [2](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/esxi600_12.jpg))
    6. F11 to confirm install to selected disk ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/esxi600_13.jpg))
    7. ESXI will take a few minutes to install ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/esxi600_14.jpg))
    8. Once installation is complete you will be presented with this [success screen](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/esxi600_15.jpg). 
    9. Remove the USB boot drive from the server, the press ENTER to reboot - ESXI will take a moment to shut down ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/esxi600_16.jpg)), then the server will reboot

- ## Connect to ESXI management interface
    With the USB drive removed and network cables disconnected from the server, it will attempt to boot from the logical drive assigned as the boot disk by the RAID array

    If all goes well, you should see ESXI begin to boot  (screenshots 
    [1](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/esxiBoot2.jpg)
    [2](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/esxiBoot3.jpg)
    [3](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/esxiBoot4.jpg))

    * *Note: if installation succeeded but you still have difficulty booting to ESXI (particularly the ['No system disk'](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/proliantRaidCreate18.jpg) message on boot), you probably need to reconfigure your RAID Setup. Your best bet is to follow the ['Initialize disk(s) in RAID menu'](firstConfig.md#initialize-disks-in-raid-menu) section above once more. If that does not work go ahead and reinsert the USB stick where you have the ESXI 6.0.0 installer and reinstall ESXI.*


    Once ESXI has booted, you will be presented with a simple window showing the network status of the web configuration server

    ![](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/esxiBoot6_2.jpg)

    All further configuration of ESXI and Virtual Machines is done via the ESXI webgui. This is a webserver running on the hypervisor that provides a graphical method for managing everything about the hypervisor & Virtual Machines.

    In order to connect to the webgui, we will have to place a pc with a web browser on the same network as the server, and ensure both are configured so that they can communicate


    There are two easy ways of doing this: DHCP and Link-Local (APIPA)

    1. DHCP
        - the Dynamic Host Configuration Protocol is an effective way for clients to obtain network configuration and unused IP addresses when they connect to a network
        - if you are following the 'Intro Network' architecture, all you have to do the ESXI webgui to obtain a DHCP address is:
            1. Use an ethernet cable to connect the first logical port of the server (labeled '1') to an open port on your home network
            1. ESXI will obtain a DHCP lease from your home router
            2. after obtaining a DHCP lease, ESXI will display a screen like [this one](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/esxiBoot5.jpg) - note that the IP address will depend on the range of the DHCP server it obtained it's lease from
    2. Link-local (APIPA)
        - if a client does not get any responses from a DHCP server, it will grab a random IP in the Automatic Private IP Addressing (APIPA) range - 169.254.0.0/16 (255.255.0.0)
        - configuring a Link-Local network is as simple as placing devices on a network without a DHCP server:
            1. Disconnect your PC from all networks, including wireless
            2. Use an ethernet cable to connect the first logical port of the server (labeled '1') to an ethernet port on your PC
            3. After a short while, the DHCP clients on your PC and the server will time out, and obtain random APIPA addresses
            4. Your server should display a screen like [this one](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/esxiBoot7.jpg) once it's attempts to communicate with a DCHP server have timed out. Note that the IP will depend on what random APIPA address your NIC has decided to use

    *Again, the goal here is to have your computer on the same network as the ESXI webserver so that you can communicate with it. Checking your PC's subnet and pinging the IP on the server are obvious ways to confirm that you are on the same network*


# **ESXI Web Configuration**
Once your PC and ESXI management webserver are on the same network, open a web browser and browse to the IP address shown on the ESXI status page (
[DHCP](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/esxiBoot5.jpg) / 
[APIPA](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/esxiBoot7.jpg))



 - Click through the [Certificate Error](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/certError.jpg)
    - *Note: when you browse to the ESXI webgui, you will be presented with a certificate error. This is because the server is using a self-signed certificate - it is still safely encrypting your communication with the server, but it's identity is not vouched for by any authorities trusted by your machine.*

* On the [Welcome Screen](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/connectionChoicesESXI.jpg) click 'Open the VMWare Host Client' to open the web interface
* congrats, you are now able to fully configure your server and VMs!
* you can safely unplug the display and keyboard from the server - you will only need to connect them if you add disks later, otherwise all configuration will be done via the ESXI webgui

- ## License ESXI
    1. If it's not already expanded, click 'navigator' on the left side to expand the main menu
    ![](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/ESXIExpandNavigator.jpg) 
    2. Click Manage (under Host) > licensing > assign license, and enter your ESXI 6 license key (screenshots 
    [1](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/esxiLicenseServer.jpg)
    [2](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/esxilicenseConfirm.jpg)
    [3](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/esxilicenseConfirm2.jpg))

- ## Set up networking:

    We will have to configure virtual networks in order to expose physical ports to our VMs

    The idea with this configuration is to use a dedicated network appliance (PFSense) to manage all traffic. To do so, we will be creating isolated virtual  networks on each physical port, and assigning them to the pfsense vm. 

    - *note on vSwitches and port groups:*

        *We are building a virtual network architecture where each physical port is isolated, with it's own virtual switch and port group. This very straightforward architecture lets us use PFSense (as a firewall / router) to manage traffic between ports.*

        *ESXI vSwitches and port groups can create very complex networks, and can be leveraged for many of the functions we are using PFSense for.  I want to stress that using ESXI for network functions **should be avoided**. ESXI is **not designed to be a primary network appliance**. PFSense IS built to be a primary network appliance - It has bulletproof security, can provide any network service you need, and is super easy to configure to boot. Use PFSense for all traffic management, including between VMs.*

    - **vSwitches**

        [Navigate](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/esxiPFSenseVSwitchNavigate.jpg) to Networking > Virtual switches > Add standard virtual switch. Enter appropriate details then click 'Add'. Repeat for all of these nics:
        1. vswitch Name = WAN , Uplink 1 = vmnic1 ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/esxiPFSenseVSwitch.jpg))
        2. vswitch Name = LAN , Uplink 1 = vmnic2 ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/esxiPFSenseVSwitch2.jpg))
        3. vswitch Name = LAN2 , Uplink 1 = vmnic3 ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/esxiPFSenseVSwitch3.jpg))
        4. vswitch Name = LAN3 , Uplink 1 = vmnic4
        5. vswitch Name = LAN4 , Uplink 1 = vmnic5
        6. vswitch Name = LAN4 , Uplink 1 = vmnic6
        7. vswitch Name = LAN4 , Uplink 1 = vmnic7

    - **Port Groups**

        [Navigate](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/esxiPFSensePortGroupNavigate.jpg) to Networking > port groups > add port group. Enter following details (keep vlan at 0!), then click 'Add'. Repeat for all of the following port groups
        1. name = WAN, Virtual Switch = WAN ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/esxiPFSensePortGroup1.jpg))
        2. name = LAN, Virtual Switch = LAN ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/esxiPFSensePortGroup2.jpg)) 
        3. name = LAN2, Virtual Switch = LAN2 ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/esxiPFSensePortGroup3.jpg)) 
        4. name = LAN3, Virtual Switch = LAN3 
        5. name = LAN4, Virtual Switch = LAN4
        6. name = LAN5, Virtual Switch = LAN5
        7. name = LAN6, Virtual Switch = LAN6

        [Here](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/esxiPFSensePortGroup4.jpg) is what your port groups should look like once you have set up all of your virtual switches and port groups. MAKE SURE vSwitch0 is not assigned to any of the port groups you created


# Understanding port assignments
Port names can be confusing:

The port number physically listed on the server, the hardware NIC name, port group/vswitch in ESXI, and interface name in PFSense are all different. 

We are going to go through all of these mappings so that you are able to document / label your ports with confidence
- ## Physical ports & NIC names
    There are 9 total ethernet ports on this server. Four are unlabeled, and the other five are labeled 1-4 and 'ILO'. 
    - the ILO port is for hardware management, and will not be available for our use
    - the four labeled and four unlabeled NICs are named vmnic0-vmnic7:
    - vmnic0 is the NIC on the port labelled '1'
    - vmnic1 is the NIC on the port labelled '2'
    - vmnic2 is the NIC on the port labelled '3'
    - vmnic3 is the NIC on the port labelled '4'
    - vmnic4-7 need to be mapped to physical ports

    How to identify physical ports

    go to Networking > Physical NICs ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs24.jpg))


    ![](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs23.jpg)
    
    We are going to ID unknown ports by plugging an ethernet cable into a known port and an unknown port:
    
    1. plug an ethernet cable into a known port (I chose the one labeled '4') on one side, and one of the unknown ports on the other 
    2. Refresh the Physical NICs page ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs22.jpg))
    3. You should see three NICs with a link: 
        - vmnic0 (esxi hypervisor - the network where you are connected to the webgui)
        - the known port you plugged into (port '4' in my example)
        - one of the unknown ports

        in [this example]((https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs25.jpg)), we know that the unknown port we plugged into corresponds to vmnic7 

    4. repeat steps for all unknown ports, you now know the NIC names of all physical ports!



- ## Port Group Names
    you now should be able to look at a physical port and say it's NIC name (vmnic0-7) 

    We will now determine which NICs are attached to the port groups in ESXI

    1. Double check the vSwitch / port group assignments

        - We have set things up in ESXI so that each NIC is assigned to a single, isolated vSwitch and port group

        - vSwitch0 should ONLY be used for the default 'VM Network' and 'Management Network' port groups, this is for security reasons. vmnic0 is attached to this vSwitch - this allows us to connect to the esxi web management, but should not be attached to any other virtual networks. We WILL NOT be managing port 1 in pfsense. [Here](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs27.jpg) is what vSwitch0 should look like (vm netowork, management network, and vmnic0 are the only things attached)

        - There should be 7 vSwitches, named WAN, LAN, and LAN2-6. These should each be the sole members of 7 identically named port groups. [Here's](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/pfs26.jpg) what is should look like in Networking>Port groups
    2. Check which NIC is on each port group

        If you [click](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/esxiPortMap2.jpg) on a port group you can see what NIC is attached:

        ![screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/esxiPortMap3.jpg)

        Repeat for all port groups - you now know which NIC is on each port group

- ## Check your understanding
    as an exercise, pick one of the unlabelled physical ports. At this point, you should be able to determine the name of the NIC (vmnic_), and the port group name (WAN, LAN, or LAN2-6). Doing this a couple of times will help you keep all of these port names straight in your head - and make sure you get your labelling right!

- ## Labelling and documentation
    port 1 is our esxi management port and can be labelled as such

    You should record the following information for each of the other ports:
    - physical label (good time to add one to the blank ports - I went with 'a', 'b', 'c', 'd')
    - NIC name (vmnic0-vmnic7)
    - ESXI port group / vswitch name

    keep this info handy, you will be adding another name once we build PFSense - at that point you will have all names and physical ports mapped! 

# Next steps
There are a lot of things you can build with this hardware and a good hypervisor, but a robust firewall will probably be a key component regardless of what you create. 

PFSense is a robust, secure, and easy to configure firewall / network appliance. Let's build a PFSense VM to in ESXI to manage the networks we configured above

continue this build with the [ESXI PFSense guide](firstPFSense.md) 
