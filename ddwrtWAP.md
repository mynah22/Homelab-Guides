- follow [official docs](https://wiki.dd-wrt.com/wiki/index.php/Wireless_Access_Point) (normal version) to build WAP:
    1. prepare an interface network for use with WAP
        - requires DHCP server
        - requires static ip reserved for this ddwrt device
        - requires appropriate firewall rules
    2. install ddwrt if it's not already - [link](https://ftp.dd-wrt.com/dd-wrtv2/downloads/betas/) to firmware
    3. hard reset router
    4. connect pc to any lan port on r6400. Connect to ddwrt via broswer @ 192.168.1.1 
    5. set password / username ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/netgearWap11.jpg))
    6. after setting password, you may stall at the apply screen. browse to 192.168.1.1 again
    7. admin > management 
        - set web protocol to https only ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/netgearWap10.jpg))
        - Apply
        - reconnect at `https://192.168.1.1`, `http://` will fail.
        - enable remote access, apply
            *Note, the port does not matter, you will still be able to connect over 80/443 remotely. Leave it at 8080.*
    8. setup > basic setup
       - WAN Connection Type: Disabled
       - set host / domain names
       - Local IP Address: same subnet as pfsense gateway on the interface your configured, but outside the DHCP range
       - Gateway & Local DNS: IP address of pfsense on interface you will be using for management
       - DHCP Server: Disable
       - uncheck DNSmasq, dhcp authoritative
       - set timezone
       - save

            ![](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/netgearWap9.jpg)

            ![](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/netgearWap8.jpg)

    9. Remove cable from pc, and plug into interface you previously configured (pfsense)
    10. connect pc to normal network, and browse to the IP you set for this ddwrt device ('local ip address' above). Must use `https://`
        - troubleshooting notes:
          1. check firewall rules - can you access the interface you created from your PC?
          2. http / https
          3. was remote access enabled? Did you restrict access by IP?
    11. setup > Advanced routing: set operating mode to router, apply ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/netgearWap7.jpg))
    12. wireless > basic settings, set SSID(s) ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/netgearWap6.jpg))
    13. wireless > wireless security
        - wpa2-psk
        - tkip+ccmp
        - set psk
        - apply settings

            ![](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/netgearWap5.jpg)

    14. services > services
        - disable dnsmasq
        - disable telnet
        - disable ttraf
        - save

            ![](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/netgearWap4.jpg)

            ![](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/netgearWap3.jpg)

    15. security > firewall
        - disable spi firewall, save ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/netgearWap2.jpg))
    16. administration > management
        - check 'info site password protection' (or disable), save ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/netgearWap1.jpg))
    17. reboot router
    18. see [ddwrt multiple wlan guide](/ddwrtMultipleWlan.md) for setting up wlans on separate vlans