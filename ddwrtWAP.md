- follow [official docs](https://wiki.dd-wrt.com/wiki/index.php/Wireless_Access_Point) (normal version) to build WAP:
    1. prepare an interface network for use with WAP
        - requires DHCP server
        - requires static ip reserved for this ddwrt device
        - requires appropriate firewall rules
    2. install ddwrt if it's not already [firmware](https://ftp.dd-wrt.com/dd-wrtv2/downloads/betas/)
    3. hard reset router
    4. connect pc to lan port. Connect to ddwrt via broswer @ 192.168.1.1
    5. set password / username ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/netgearWap11.jpg))
    6. admin > management 
        - set web protocol to https only ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/netgearWap10.jpg))
        - reconnect at https://192.168.1.1
    7. setup > basic setup
       - WAN Connection Type: Disabled
       - set host / domain names
       - Local IP Address: same subnet as pfsense gateway on the interface your configured, but outside the DHCP range
       - Gateway & Local DNS: IP address of pfsense on interface you will be using for management
       - DHCP Server: Disable
       - uncheck DNSmasq, dhcp authoritative

       - save

            ![](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/netgearWap9.jpg)

            ![](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/netgearWap8.jpg)

    8. plug ddwrt LAN into network w/dhcp (pfsense)
    9. connect pc to IP you set for this ddwrt device ('local ip address' above). Must use `https://`
    10. setup > Advanced routing , set operating mode to router ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/netgearWap7.jpg))
    11. wireless > basic settings, set SSID(s) ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/netgearWap6.jpg))
    12. wireless > wireless security
        - wpa2-psk
        - tkip+ccmp
        - set psk
        - apply settings

            ![](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/netgearWap5.jpg)

    13. services > services
        - disable dnsmasq
        - disable telnet
        - disable ttraf
        - save

            ![](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/netgearWap4.jpg)

            ![](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/netgearWap3.jpg)

    14. security > firewall
        - disable spi firewall, save ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/netgearWap2.jpg))
    15. administration > management
        - check 'info site password protection', save ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/netgearWap1.jpg))
    16. reboot router
    17. see [ddwrt multiple wlan guide](/ddwrtMultipleWlan.md) for setting up wlans on separate vlans