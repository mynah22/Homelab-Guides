1. follow [setup guide](/ddwrtWAP.md)
2. wireless > basic settings hide both default SSIDs ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/ddwrt0.jpg))
3. wireless > basic settings Add virtual 2.4/5 APs as needed
    - AP isolation: devices on this AP cannot communicate with others on this AP. Other comms can proceed as normal. non virtual APs cannot be isolated
    - web UI access: disable if non-user net
    - disable ssid broadcast if desired for VAP
4. wireless > wireless security
    - set security mode and psk for all ssids