# What I use pfSense for

## Article type: reference
this is a reference for my own usage of pfSense. There are links to some how-to documents for most of the services I utilize

## Contents
- [Packages and services](#packages-and-services)

# Built-in Functions
1. Router
    - pfSense performs NAT routing between the WAN and LAN(s)
    - It also routes traffic between LANs
2. Firewall
    - determine if / what packets are allowed to route between interfaces
    - used to create robust, secure, and segmented networks
3. DNS server
    - pfSense is proving name resolution services on all of my interfaces
    - I am blocking ads at the dns level (see [installed packages](#packages-installed))
    - I am using the DNS Resolver (not Forwarder)
    - I check the following boxes under `Services > DNS Resolver > General Settings`:
      -  'Register DHCP leases in the DNS Resolver
      -  'Register DHCP static mappings in the DNS Resolver'
      -  *This allows devices to be resolved by hostname if they provide one when requesting a DHCP lease, or if they have a static DHCP mapping*
   -  I set host overrides in the DNS resolver, notably for docker container aliases and proxmox hosts.
4. DHCP server
   - I have a DHCP server on all of my interfaces except those with fully static configs (like my vpn interface)
   - I have a couple of static mappings for personal devices for convenience 


# Packages installed
1. `WireGguard`
   - VPN. See [how-to](/pfsenseWireguard.md)
2. `acme`
   - SSL certs for pfSense webConfigurator. See DNS validation [how-to](/pfsenseDnsAcme.md)
3. `pfBlockerNG-devel`
   - DNS and IP based adblocker. See [how-to](/pfsenseDNSBlackhole.md)

# Other services
1. Dynamic DNS 
    - keeps the DNS records for my domain up to date as my WAN IP changes
    - see an [example](/pfsenseDDNS.md) using name.com's DNS service