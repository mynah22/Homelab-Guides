# Wireguard on pfsense
[og source](https://www.wundertech.net/how-to-set-up-wireguard-on-pfsense/)

# Wireguard VPN via pfSense
- ## Article type: how-to
  This guide is a how-to guide for setting up a wireguard VPN in pfSense

# Requirements
1. A dynamic DNS record pointing to your WAN IP, or static a IP
   - there are free dynamic DNS services
   - Domain names are not prohibitively expensive: all the big domain name providers include DNS resolution with any domain. 
2. if pfSense is inside of another private network, you will need to configure appropriate port forwarding rules on the outside network

# Instructions
1. system > package manager > available packages, search 'wireguard', and click the install button on the wireguard package, then confirm. You will see a success page (screenshots [1](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/wireguard0.jpg) [2](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/wireguard1.jpg) [3](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/wireguard2.jpg))
2. vpn > wireguard > settings > check 'enable wireguard'. [screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/wireguard6.jpg)
3.  Save. Apply changes. ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/wireguard7.jpg))
4.  vpn > wireguard > tunnels > click 'add tunnel' ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/wireguard3.jpg))
5. enter a description, then generate public / private keypair ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/wireguard4.jpg)
6. copy public key and put it somewhere easy to get to. We will need this for the client configurations later. 
7. Scroll to the bottom of the page, click 'save tunnel'. Apply changes.  (screenshots [1](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/wireguard5.jpg) [2](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/wireguard7.jpg))
8. Interfaces > interface assignments, select the tunnel you just made, click Add ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/wireguard8.jpg))
9. click the name of the new interface ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/wireguard9.jpg))
10. interface edit screen:
    - enable interface checkbox
    - set description / interface name as desired
    - ipv4: static
    - **MTU must equal 1420**!!
    - set ip address / subnet scope for this tunnel (must be unused)
    - save
    - apply changes
        ![](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/wireguard10_1.jpg)

        ![](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/wireguard11.jpg)
11. you should see the assigned interface on your tunnel at vpn > wireguard > tunnels ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/wireguard12.jpg))
12. Firewall rules
    - please note, these are simply a bare minimum example for function testing. **your production firewall rules should be more thought out**
    - At a bare minimum, you will need 2 firewall rules for the vpn to function:
        1. A firewall rule allowing traffic out of the wireguard interface
        2. a firewall rule allowing WAN traffic (ie traffic from outside your network) to enter on the wireguard port
            - *the pfsense wireguard package is listening on this port, so if a correctly configured request comes thorugh it will respond, otherwise it is stealthed - it does not send any packet in response*
    - Wireguard interface to anywhere rule:
        - firewall > rules > select wireguard interface. Click Add ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/wireguard13.jpg))
        - change protocol to 'any'
        - add a description
        - save
              ![](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/wireguard14.jpg)
    - WAN allow WG port rule:
        - firewall > rules > WAN. Click Add ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/wireguard15.jpg))
        - change protocol to udp
        - set destination to 'WAN address'
        - set destination port to 51820
            - *this is the default port the wireguard service is listening on. this can be edited in vpn > wireguard > tunnels.*
        - add a description
        - save
              ![](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/wireguard16.jpg)
    - apply firewall changes
        ![](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/wireguard17.jpg)
13. Peer configuration
    - The precise details of the configuration varies with each peer UI, but the idea is the same across all operating systems:
        1. peer creates a public / private keypair
        2. peer adds server public key to it's config
        3. server adds peer public key to it's allowed peer list
        4. server sets peer network configuration
        5. peer is configured to point to server, with dns settings as desired by the client
    - here's an example with an Android client (using the wireguard app from the google play store)
        1. in the client, tap + to create a new tunnel config ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/wireguard21.jpg))
        2. from scratch ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/wireguard20.jpg))
        3. create tunnel and generate key
            - name the tunnel. Under addresses **enter the IP address this client will have**, along with the bitwise subnet mask for the wireguard interface you configured in pfsense
            - Enter the DNS servers you would like to use for this interface 
            - click the arrows icon to generate a public / private keypair
            - save
            
                ![](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/wireguard19.jpg)

                ![](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/wireguard18.jpg)
        4. In pfsense, add a peer
            - vpn > wireguard > peers > add peer ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/wireguard22.jpg))
            - peer edit screen
                - tunnel: select the wireguard interface / tunnel you created
                - description: of client (peer)
                - public key: obtained from client (peer), should match 'public key' field on client. 
                - allowed IPs: the IPs that the **client will have when on the vpn** - this should match the addresses field on the client, but with a mask of 32 (ie, only one ip address allowed)
                - save and apply changes
  
                    ![](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/wireguard23.jpg)
        5. Configure server details on client
            - obtain your wireguard tunnel public key. This can be found at vpn > wireguard > tunnels > edit pencil ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/wireguard24.jpg))
            - in the client config, open the tunnel and tap 'add peer' ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/wireguard25.jpg))
            - public key: the public key of the wireguard tunnel you obtained above
            - endpoint: the public IP and port of your wireguard server (default port is 51820)
            - allowed IPs:
                - Here is where you can configure a split tunnel. For more, see [split tunnel explanation](splitTunnel.md)
                - for a full tunnel, leave it blank ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/wireguard26.jpg))
                - for a split tunnel, enter the subnet(s) you would like to pass through the vpn ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/wireguard27.jpg))
            - save
        6. client should now be able to connect to server
        7. to check, go to vpn > wireguard > status and expand the tunnel. if the client handshake icon is green everything has succeeded. ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/wireguard28.jpg))
