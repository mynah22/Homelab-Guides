# Privileged subnets
1. ## Workstation subnet
    - Description:

        *Subnet for personal workstations*
    - Type devices to be joined:
        - personal PCs
    - WLAN:
        - no        
    - Egress subnet(s):
        - WAN
            - allow all
        - services subnet:
            - allow only specific service ip:ports
            - ip:ports allowing standard management level (day to day)
        - non-VPN subnets(except control tablet):
            - allow all
    - Ingress subnet(s):
        - user0 & management VPN
        - user0 phone

2. ## user0 phone subnet
    - Description:

        *Subnet for personal phone*
    - Type devices to be joined:
        - personal cell(s) (WLAN)
    - WLAN:
        - yes        
    - Egress subnet(s):
        - WAN
            - allow all
        - services subnet:
            - allow only specific service ip:ports
            - ip:ports allowing standard management level (day to day)
        - non-VPN subnets(except control tablet):
            - allow all
    - Ingress subnet(s):
        - user0 & management VPN
        - Workstation

2. ## user1 subnet
    - Description:

        *Subnet for all user1 devices*
    - Type devices to be joined:
        - personal cell(s) (WLAN)
    - WLAN:
        - yes
    - Egress subnet(s):
        - WAN
            - allow all
        - services subnet:
            - allow only specific service ip:ports
        - non-VPN subnets (except control tablet):
            - allow all
    - Ingress subnet(s):
        - user0 & management VPN
        - Workstation
       

# VPN subnets
1. ## Management VPN
    - Description:

        *Subnet for management. Not used normally, only for administration. All ports and ips.*
    - Type of devices to be joined:
        - all personal devices, whether remote or local, when admin network access is required
    - Egress subnet(s):
        - Allow all on all subnets (except control tablet)
    - Ingress subnet(s):
        - none

2. ## user0 vpn
    - Description:

        *Subnet for remote access for user0's devices. Service and std management ports.*
    - Type devices to be joined:
        - all of user0's mobile devices
    - Egress subnet(s):
        - WAN
            - allow all
        - services subnet:
            - allow only specific service ip:ports
            - ip:ports allowing standard management level (day to day)
        - non-VPN subnets (except control tablet):
            - allow all
    - Ingress subnet(s):
        - management VPN

3. ## user1 vpn
    - Description:

        *Subnet for remote access for user1's devices. Service ports only*
    - Type devices to be joined:
        - all of user1's mobile devices
    - Egress subnet(s):
        - WAN
            - allow all
        - services subnet:
            - allow only specific service ip:ports
    - Ingress subnet(s):
        - Management VPN
        

# Homelab subnets
1. ## hypervisor management subnet
    - Description:

        *Subnet to access hypervisor management*
    - Type devices to be joined:
        - hypervisors
    - WLAN:
        - no
    - Egress subnet(s):
        - WAN
            - allow all
        - Services subnet
            - allow all
    - Ingress subnet(s):
        - management VPN
2. ## Services subnet
    - Description:

        *Subnet where services will be located*
    - Type devices to be joined:
        - VMs
    - WLAN:
        - no
    - Egress subnet(s):
        - WAN
            - allow all
        - iot subnets
            - allow all
    - Ingress subnet(s):
        - Full access (standard and management ports):
            - Management VPN
        - End user access (just standard ports):
            - All other privileged subnets

3. ## homeauto front end subnet


# Restricted access subnets
1. ## Control tablet 
    - Description:

        *Subnet that only allows access to a homeauto frontend*
    - Type devices to be joined:
        - old EOL android tablets
    - WLAN:
        - yes, 2.4Ghz isolated
    - Egress subnet(s):
        - homeauto front end subnet 
            - only 80/443
            - only to specified server IP
    - Ingress subnet(s):
        - none

2. ## iot pi
    - Description:

        *Subnet for iot endpoints running a full os stack*
    - Type devices to be joined:
        - raspberry pi and other linux machines
    - WLAN:
        - yes, 2.4Ghz non-isolated
    - Egress subnet(s):
        - WAN
            - allow all
        - iot uc
            - allow all
    - Ingress subnet(s):
        - iot uc
        - privileged & vpn subnets
        - services subnet

3. ## iot uc
    - Description:

        *Subnet for iot endpoints running only microcontroller firmware*
    - Type devices to be joined:
        - microcontrollers 
    - WLAN:
        - yes, 2.4Ghz non-isolated
    - Egress subnet(s):
        - iot pi
            - allow all
    - Ingress subnet(s):
        - iot uc
        - privileged & vpn subnets
        - services subnet

4. ## IOT camera
    - Description:

        *Subnet for internet-based cameras*
    - Type devices to be joined:
        - iot cameras
    - WLAN:
        - yes, 2.4Ghz isolated
    - Egress subnet(s):
        - WAN
            - allow all
    - Ingress subnet(s):
        - none

5. ## Streaming devices 
    - Description:

        *Subnet for streaming devices*
    - Type devices to be joined:
        - chrome
    - WLAN:
        - yes, 2.4Ghz isolated
    - Egress subnet(s):
        - WAN
            - allow all
    - Ingress subnet(s):
        - none

# Standard access subnets
1. ## user0 quick connect

2. ## guest




notes:

wireless connection categories:

- blink camera
- circuitpython
- rpi
- chromecast
- work phone / computer
- personal phone / computer
- control tablets
