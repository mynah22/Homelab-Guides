Boot wap, connect ethernet to pc
browse to tplinkap.net
set admin password
set net settings
update gateway


# Create Name.com TPLink WAP build
- ## Article type: how-to
  This guide is a how-to guide for standing up a tl-wa801n in WAP mode
  
# Instructions 
1. Boot WAP. Connect PC to ethernet port
2. browse to TPLinkap.net
3. set admin password ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/tplinkWap0.jpg))
4. configure network ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/tplinkWap1.jpg))
5. configure network continued ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/tplinkWap2.jpg))
6. Multi SSID notes:
    - do NOT use vlan 1 : it behaves as untagged (like plugged into LAN port)
    - otherwise, vlans 2 and above can be used. Only appropriately tagged frames will be written to wireless LAN
    - create an interface in pfSense for each vlan
    - I also create an interface for the trunk (ie untagged packets) - this so I can access the web gui using specific firewall rules
7. wap will reboot
8. set manual ip to match wap
9. under Network > LAN settings, change the gateway to your upstream ip (pfsense interface ip)
10. dhcp>dhcp settings
  - disable again, save (it should stay disabled, but seems like it will re-enable dhcp if it sees unanswered requests)