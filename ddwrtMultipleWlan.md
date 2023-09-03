1. follow process in [ddwrt WAP guide](/ddwrtWAP.md) for initial device configuration
2. Create a virtual AP
    - wireless > basic settings, add virtual AP ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/ddwrtVlan0.jpg))
    - wireless > wireless security
      - set wireless security (wpa2+psk, tkip+ccmp, set password)
3. Enable VLANs
    - setup > switch config
    - enable vlans, apply ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/ddwrtVlan1.jpg))
    - refresh the page, enable again, apply ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/ddwrtVlan2.jpg))
    - Administration > reboot router
4. create vlan for use with SSID
    - setup > switch config 
    - add vlan row ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/ddwrtVlan4.jpg))
    - set vlan tag number for new vlan ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/ddwrtVlan5.jpg))

        *Note, this should be **ABOVE 2** 0, 1, and 2 are special vlans on this firmware.*
    - remove 2 from all columns ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/ddwrtVlan3.jpg))
    
        *dd-wrt internal vlan used by firmware for WAN*
    - trunked port:
      - tagged
      - check the vlans you want to trunk
      - add vlan 1 if you want web management of ddwrt
        - you will have to update pfsense interface /subnet for ddwrt to use vlan 1 (instead of untagged)

    - non-trunked port:
      - untagged
    - check CPUPORT column for your custom vlans
        
        *CPUPORT is needed in order to add to the vlan to the bridge later*
    - apply settings

        ![](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/ddwrtVlan6.jpg)

5. wireless > basic settings. Create VAPs as needed ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/ddwrtVlan7.jpg))
6. create bridge
    - setup > networking > add bridge ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/ddwrtVlan8.jpg))
    - name bridge, turn STP off. apply settings. ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/ddwrtVlan9.jpg))
    - add two assignments: target wireless interface and vlan. repeat for all ssid vlans ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/ddwrtVlan10.jpg))
    - apply settings
    - Wait a moment, and you should see interfaces move to 'current bridging table' ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/ddwrtVlan11.jpg))
7. pfSense interfaces can now be configured. Interfaces can be made per-vlan (ssid)