# Advantech FWA1320

This is a little 6 port network appliance, with an msata drive, a couple of DIMM slots, and some usb ports for installation media. No video output, so the only way to connect to the device is by using a console cable (RJ45 to USB)

I will describe the basic of connecting to this device, and installing PFSense 

# Requirements
1. RJ45 to USB console cable
2. USB installation media with PFSense **SERIAL** installation .iso
1. PuTTY or similar tool installed on your PC

# Index
1. [Connect to device]()
    - []()
2. [Install PFsense]()
    - []()

# Connect to device
I am pretty much just following the recommendations in the [device manual](https://advdownload.advantech.com/productfile/Downloadfile4/1-1TBDP2H/FWA-1320_User_Manual_Ed.2-D1.pdf)  

We will be using PuTTY on windows to connect to the device using a USB to RJ45 serial console cable

Once you have PuTTY installed on your PC and a console cable connected to the network appliance, here are the settings you will need to configure in PuTTY
1. 
    - Make sure the Serial option is selected under Session. Under 'Serial Line', type the number of the COM port of your USB console cable (COM40 in my case)  
    ![](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/fwa0.jpg)
2. Under Connection > Serial, set the following parameters:
    - speed: 115200
    - data bits: 8
    - stop bits:1
    - Parity & flow control : none
    ![](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/fwa1.jpg)
3. Under Terminal > keyboard, ensure that the function key mode is VT100+
    ![](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/fwa2.jpg)
4. Save your config in PuTTY for later use. Click 'Open' to start listening on COM40, and turn the network appliance on

# Install PFsense
You should see this output:
![](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/fwa3.jpg)

This confirms that you are connected to the device over the console cable. Power the network appliance down

1. prepare USB media
    - you will have to build a PFSense USB bootable drive
    - you will need the SERIAL version of the PFSense memstick (memstick-serial) ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/fwa4.jpg))
    - Use Rufus to create the bootable USB
    - Insert prepared USB into one of the network appliance's USB ports
2. Enter PFSense installer
    - when you get to [this screen](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/fwa3.jpg), hit DELETE or ESCAPE to enter the BIOS menu
    - use RIGHT and LEFT to move to the Save & Exit tab
    - use UP and DOWN to select your USB drive
    - ENTER to boot from usb
