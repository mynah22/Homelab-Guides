# Wireguard on pfsense
[og source](https://www.wundertech.net/how-to-set-up-wireguard-on-pfsense/)

set primary dns server to internal (for pfblockerng)
# Wireguard VPN via pfSense
- ## Article type: how-to
  This guide is a how-to guide for setting up a wireguard VPN in pfSense

# Requirements
1. ideally, a dynamic DNS record pointing to your WAN IP, or static a IP
   - there are free dynamic DNS services, and domain names are not prohibitively expensive - all the big providers include DNS resolution with any domain. 
2. if pfSense is inside of another private network, you will need to configure appropriate port forwarding rules on the outside network

# Instructions
1. system > package manager > available packages, search 'wireguard', and click the install button on the wireguard package, then confirm. You will see a success page (screenshots [1](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/wireguard0.jpg) [2](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/wireguard1.jpg) [3](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/wireguard2.jpg))
2. vpn > wireguard > settings > check 'enable wireguard'. Save. Apply changes. (screenshots [1](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/wireguard6.jpg) [2](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/wireguard7.jpg))
3. vpn > wireguard > tunnels, click Add Tunnel ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/wireguard3.jpg))
4. enter a description, then generate public / private keypair ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/wireguard4.jpg))
5. copy public key and put it somewhere easy to get to. We will need this for the client configurations later. 
6. Scroll to the bottom of the page, click 'save tunnel'. Apply changes.  (screenshots [1](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/wireguard5.jpg) [2](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/wireguard7.jpg))
7. Interfaces > interface assignments, select the tunnel you just made, click Add ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/wireguard8.jpg))
8. click the name of the new interface ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/wireguard9.jpg))
9. interface edit screen:
    - enable interface checkbox
    - set description / interface name as desired
    - set ip address / subnet scope for this tunnel (must be unused)
    - save
        [screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/wireguard10.jpg)