# Basic Network explanation

This documents attempts to explain the big picture of the network architecture I recommend for building your first homelab

# Requirements
1. Understand the basics of DHCP and IP subnets

# Index
- [Basic Network explanation](#basic-network-explanation)
- [Requirements](#requirements)
- [Index](#index)
- [Your Home Network](#your-home-network)
  - [LAN to LAN](#lan-to-lan)
  - [LAN to WAN](#lan-to-wan)
  - [WAN to LAN](#wan-to-lan)
- [Adding a lab network](#adding-a-lab-network)
  - [Why double NAT?](#why-double-nat)
  - [Understanding PFSense lab net](#understanding-pfsense-lab-net)
  - [LANx to WAN (home network)](#lanx-to-wan-home-network)
  - [LANx to WAN (internet)](#lanx-to-wan-internet)
  - [LANx to LANy](#lanx-to-lany)
  - [WAN (home network) to LANx](#wan-home-network-to-lanx)
- [Further config](#further-config)



# Your Home Network
![screenshot](/screenshots/homelabNet0.jpg)

The above diagram depicts a theoretical home network, which is probably similar to your home network

*note: In this diagram, the cable modem, router, and WAP are separate physical devices. In your environment, 2 or more of those functions may be performed by a single device*

*IPs have been changed to protect the identity of the innocent. The IPs listed are just for demonstration purposes*

This vanilla home network works in the following way:
1. Your router is a DHCP client on the WAN side (ISP / Internet)
1. Your router obtains a DHCP lease from the DHCP server for your ISP, giving it a WAN ip, subnet and DNS servers 
1. Your router's main function is as a NAT router between the WAN and your home network
    - your entire home network occupies a single IP address on the WAN side
    - Multiple devices are able to connect to the WAN because of the NAT:
        1. When packets go out across the NAT boundary to the WAN side, the NAT router changes the sender's IP to the WAN IP
        2. It also remembers which IP on the LAN started this conversation 
        3. this way, response packets from the internet will be able to be returned to the router
        4. and, since the router remembers who started the conversation, packets will be routed to the LAN IP that started the conversation
    - No packets are allowed to go from WAN to LAN unless they are in response to a conversation the LAN-side device started
1. your router is also a DHCP server on the LAN interface, automatically providing DHCP leases/IP assignments, subnet info, and DNS server IPs to any clients that connect to your network.
1. Your router uses a RFC1918 subnet. These are IP addresses that are reserved to NEVER be used on the internet. Using a 'private' (RFC1918) subnet ensures that the NAT always has a different subnet on either side of the boundary

## LAN to LAN
Here's what it looks like for devices to communicate within our LAN:
1. desktop at 192.168.1.6 is going to send a print job to the printer at 192.168.1.7
2. It sees that 192.168.1.7 is on the same subnet, so it writes a packet onto the LAN with 192.168.1.7 as the destination IP
3. printer is listening for communications addressed to it, and responds as needed 

## LAN to WAN
Let's step through a LAN > WAN communication:
1. cell phone (ip 192.168.1.4) communicates with google.com (142.250.72.78)
1. Since it sees that 142.250.72.78 is not in it's subnet, it sends the packet to the nearest router (the 'gateway' in dhcp, our home router) 
1. The router sees that 142.250.72.78 can be accessed through the WAN interface, so it changes the source IP of the packet to our home network's WAN IP, remembers which internal IP started the conversation, listens for responses to that packet on WAN, and sends the packet on to the next closest router (your WAN side gateway)
1. Response packets from google.com are routed to our home router. Since our router was expecting return traffic, it uses NAT to translate the destination IP:port of the incoming traffic to the IP:port of LAN device that started the conversation (192.168.1.3 cell phone)

## WAN to LAN
Here's a run down of how WAN > LAN would operate:
1. bad guy attempts to communicate with our WAN IP
2. This communication does not match any of the active conversations the router is monitoring for response packets. Packet is silently ignored, nothing is sent into our LAN or back to bad guy 

# Adding a lab network
## Why double NAT?
There are a lot of ways to build out your first home lab, and I want to take a moment to describe why I recommend this architecture for a first lab
1. Set up time
    - Just plug in to your home net and start configuring. Stop working any time you want. No big stressful push to get your network operational before the fam wakes up. 
1. Ain't Broke
    - Your home network likely works just fine for your everyday tasks. You can do quite a bit to optimize / reduce traffic when you are in complete control of your network, but why mess with things that are not broken when you are still learning?
1. Impact
    - You will make mistakes. A single firewall rule or interface config mishap can bring your ENTIRE network down. Much nicer to just have the lab network go down than your entire home network!
1. Learning
    - You can build any network / server you like, whether you place a lab net on the edge of your network, or double NATed like below, so there is no penalty to what you can learn / build. 



## Understanding PFSense lab net
![screenshot](/screenshots/homelabNet2.jpg)

The diagram above demonstrates a fairly simple lab network (managed by PFSense) that is connected to the home network through another NAT boundary.

*note: the way that this lab network is implemented physically and virtually don't matter here - principles discussed below apply regardless of whether you are using physical ports vs vlan trunks, or if PFsense is a VM vs on bare metal.*
Since PFSense is capable of creating such a wide variety of network architectures, please be aware that the items described here are ***only true for this example network***, and are not intended as hard rules of how PFSense operates.
1. WAN 
    - ***The WAN side of PFSense is the home network***
    - PFSense is a DHCP client on the WAN side - it will receive a DHCP lease / ip from the home router
    - PFSense is performing NAT routing between it's WAN (home network) and the PFSense LAN interfaces
1. LANs
    - Each LAN networks (LAN1, LAN2, LAN3) have different subnets - 10.0.1.0/24, 10.0.2.0/24, 10.0.3.0/24
    - All of the LAN networks have a different subnet than the WAN/home network (192.168.1.0/24)
    - PFsense has a a different IP on each LAN network, matching it's subnet
    - PFSense is a DHCP server on all the LAN interfaces
1. NAT 
    - connections from a LAN network to another LAN network do not pass a NAT boundary
    - connections from a LAN network to the home network pass through the homenet/pfsense NAT boundary
    - connections to the internet will pass through 2 NAT boundaries: 
        1. the homenet/pfsense boundary
        1. the isp/homenet boundary
1. Firewall
    - PFSense is firewalling traffic between all interfaces (WAN and the LANs)
    - By default, all traffic is blocked from traveling between interfaces 
        1. example: LAN2 to LAN1 
        2. example: LAN3 to WAN
    - ***Let's assume that we have set firewall rules allowing each of the LANs to communicate with WAN, but not each other***
    - Firewalls define what traffic / IPs are allowed to OPEN communication between networks - firewalls always allow *return* traffic. If a firewall allows a communication to be opened, then the return packets are always allowed

With an understanding of the above items, let's run through some communication examples

## LANx to WAN (home network)
1. A device on LAN1 wants to send a print job to the printer on the home network at 192.168.1.7
2. It sees that 192.168.1.7 is not the same subnet, so it passes it to the closest router (gateway in DHCP - PFSense IP 10.0.1.1)
3. PFSense sees that the packet must be routed to another interface (WAN) to get to destination.
    - Firewall rules are checked to see if it will be allowed to be routed
    - We set a firewall rule to allow traffic from the LANs to WAN, so it is allowed
    - Since it's crossing a NAT boundary, the source IP on the packet is changed to the WAN IP (192.168.1.8)
    - PFSense sends the traffic to WAN (home network), and pfsense starts listening for return traffic
3. printer is listening for communications addressed to it, and responds to 192.168.1.8
4. PFSense was expecting this traffic. It NATs the destination IP of the incoming traffic to the device that opened this communication (something on LAN1 / 10.0.1.0/24)

## LANx to WAN (internet) 

1. A device on LAN2 wants to communicate with google.com (142.250.72.78)
1. It sees that that IP is not on it's subnet, so it passes it to the closest router (gateway in DHCP - PFSense IP 10.0.2.1)
1. PFSense sees that the packet must be routed to another interface (WAN) to get to destination. 
    - Firewall rules are checked to see if it will be allowed to be routed
    - We set a firewall rule to allow traffic from the LANs to WAN, so it is allowed
    - Since it's crossing a NAT boundary, the source IP on the packet is changed to the WAN IP (192.168.1.8)
    - PFSense sends the traffic to WAN (home network), and pfsense starts listening for return traffic
1. On the home network, the home router likewise sees that this packet must be routed to it's WAN gateway 
    - this passes another NAT boundary, and the source IP is once more changed to the WAN ip (121.35.118.48)
    - Home router starts listening for return traffic. 
1. Google sends response traffic to the source IP on the packets we it received (121.35.118.48). Traffic travels the internet makes it to our home router
1. Home router was waiting for this traffic. It NATs the destination IP of the incoming traffic to the device that opened this communication (PFSense at 192.168.1.8) and sends it on into the home network (192.168.1.0/24)
1. PFSense was waiting for this traffic. It NATs the destination IP of the incoming traffic to the device that opened this communication (something on LAN2 / 10.0.2.0/24)

## LANx to LANy

1. A device on LAN3 wants to communicate with LAN1
1. It sees that that IP is not on it's subnet, so it passes it to the closest router (gateway in DHCP - PFSense IP 10.0.3.1)
1. PFSense sees that the packet must be routed to another interface (LAN1) to get to destination. 
    - Firewall rules are checked to see if it will be allowed to be routed
    - we only created rules allowing LANs to WAN. There is no matching rule, packet is ignored

## WAN (home network) to LANx
1. The desktop on the home net (192.168.1.6) want to communicate with a server on LAN2 using it's IP
2. It has no route to 10.0.2.0/24, transmit fails 



# Further config
You should be able to understand the way the various networks interact above. Once you have that in your head, the world is your oyster!

Here are a couple of obvious things you can do extend the architecture above, without losing it's key benefits

1. Move some home net devices to a lab network
    This is one of the first things you will do, and is usually required to configure devices on the lab net

    in the [LANx to WAN (home net)](#lanx-to-wan-home-network) section above, you can see that devices on your home net cannot connect to devices on the lab net. 
    
    But what if you want to connect to a resource on the lab net using your desktop on the home net?
    - The easiest way to do this is to move the desktop onto one of the lab nets
    - Then set appropriate firewall rules so that the interface it's plugged into can communicate with the other lab subnets & WAN
    - If you break your lab net, plug the desktop back into your home net and everything works again (so you can research what you broke :)