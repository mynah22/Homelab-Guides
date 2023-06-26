# Basic Network explaination

This documents attempts to explain the big picture of the network architecture I recommend for building your first homelab

# Requirements
1. Understand the basics of DHCP and IP subnets

# Index
1. [Your home network](#your-home-network)
2. []()
    - [](#disable-pfblockerng)
    - [](#add-temporary-and-permanent-exceptions)


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
        4. and, since the router remembers who started the conversation, packets will be routed to the IP that started the conversation
    - No packets are allowed to go from WAN to LAN unless they are in response to a conversation the LAN-side device started
1. your router is also a DHCP server, automatically providing DHCP leases/IP assignments, subnet info, and DNS server IPs to any clients that connect to your network.
1. Your router uses a RFC1918 subnet. These are IP addresses that are reserved to NEVER be used on the internet. Using a 'private' (RFC1918) subnet ensures that the NAT always has a different subnet on either side of the boundary

Let's step through a LAN > WAN communication:
1. cell phone (ip 192.168.1.4) communicates with google.com (142.250.72.78)
1. Since it sees that 142.250.72.78 is not in it's subnet, it sends the packet to the nearest router (the 'gateway' in dhcp, our home router) 
1. The router sees that 142.250.72.78 can be accessed through the WAN interface, so it changes the source IP of the packet to our home network's WAN IP, remembers which internal IP started the conversation, and listens for responses to that packet on WAN
1. Response packets are routed to our home router, which uses NAT to translate the destination IP:port to the IP of LAN device that started the conversation (192.168.1.3 cell phone)


Here's a run down of how WAN > LAN would operate:
1. bad guy attempts to communicate with our WAN IP
2. This communication does not match any of the active conversations the router is listening for a response to. Packet is silently ignored

# Adding a lab network

