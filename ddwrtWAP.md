- follow [official docs](https://wiki.dd-wrt.com/wiki/index.php/Wireless_Access_Point) (normal version) to build WAP:
    1. prepare an interface network for use with WAP
        - requires DHCP server
        - requires static ip reserved for this ddwrt device
        - requires appropriate firewall rules
    2. install ddwrt if it's not already [firmware](https://ftp.dd-wrt.com/dd-wrtv2/downloads/betas/)
    3. hard reset router
    4. connect pc to lan port. Connect to ddwrt via broswer @ 192.168.1.1
    5. set password / username ([screenshot]())
    6. admin > management 
        - set web protocol to https only
    7. setup > basic setup
       - WAN Connection Type: Disabled
       - Local IP Address: e.g. 10.242.242.2 (same subnet as pfsense gateway but outside the DHCP range)
       - DHCP Server: Disable
       - uncheck DNSmasq, dhcp authoritative
       - Gateway: IP address of pfsense on this interface
       - Local DNS: pfsense IP on this interface
       - save
    8. plug ddwrt LAN into network w/dhcp (pfsense)
    9.  setup > Advanced routing , set operating mode to router
    10. wireless > basic settings, set SSID(s)
    11. wireless > wireless security
        - wpa2-psk
        - tkip+ccmp
        - set psk
        - apply settings
    12. services > services
        - disable dnsmasq
        - disable telnet
        - disable ttraf
        - save
    13. security > firewall
        - disable spi firewall, save
        - check 'filter
    14. administration > management
        - check 'info site password protection', save
    15. reboot