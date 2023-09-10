I have installed pfsense on an Advantech fwa-1320 network appliance

This requires a serial interface. I bought a usb console cable for under $10 (useful for other net appliances that lack video out). Install the drivers (most likely [ftdi](https://ftdichip.com/drivers/)). 

For this installation you need to download the ***USB Memstick Installer*** from the [PFSense Download page]https://www.pfsense.org/download/  
Achitecture: AMD64
Console: Serial

once you have the correct .iso, create the bootable media
I used rufus without issue

in addition to the cable, you will need a correctly configured serial terminal to view / interact with the appliance
I used PuTTY on windows  (freeware) with the following settings:
- set COM port to matching device in device manager
- speed: 115200
- connection type : serial 
- under Terminal > Keyboard: 'Function keys and keypad' =  VT100+
- under Window > Colors: 'indicate bolded text by changing' = The color

plug the console cable into your pc and the bootable usb into the net appliance
open a putty session with the settings above
turn on the network appliance, then press DELETE in the putty window until you see the BIOS menu (should be shortly after the fan starts spinning hard)
go to the 'save and exit' tab, and select your USB drive under the boot override menu (Not the UEFI option)

![](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/advantechBios.jpg)

ENTER to select the default terminal type (vt100) 

![](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/cmdPfsense0.jpg)


You should see PFSense boot as normal in the terminal console. The display will be slightly different ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/cmdPfsense1.jpg)), but otherwise set up matches the [Basic PFSense guide](pfsenseBasic.md) from this point forward. Looks like WAN defaulted to port MGT1 (igb0 in pfsense) and LAN to port MG2 (igb0 in pfsense). 