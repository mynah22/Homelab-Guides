- [Privileged subnets](#privileged-subnets)
  - [Workstation subnet](#workstation-subnet)
  - [user0 phone subnet](#user0-phone-subnet)
  - [user1 subnet](#user1-subnet)
  - [non-personal subnet](#non-personal-subnet)
- [Homelab subnets](#homelab-subnets)
  - [hypervisor management subnet](#hypervisor-management-subnet)
  - [hypervisor backup subnet](#hypervisor-backup-subnet)
  - [Services subnet](#services-subnet)
  - [Database subnet](#database-subnet)
  - [homeauto front end subnet](#homeauto-front-end-subnet)
- [VPN subnets](#vpn-subnets)
  - [Management VPN](#management-vpn)
  - [user0 vpn](#user0-vpn)
  - [user1 vpn](#user1-vpn)
- [Restricted access subnets](#restricted-access-subnets)
  - [iot pi](#iot-pi)
  - [iot uc](#iot-uc)
  - [IOT camera](#iot-camera)
  - [Streaming devices](#streaming-devices)
  - [Control tablet](#control-tablet)


# Privileged subnets
## Workstation subnet
  - subnet: 
    - `10.11.1.0/24`
  - Description:

    *Subnet for personal workstations*
  - Devices to be joined:
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
      - user0 vpn 
      - management VPN
      - user0 phone

## user0 phone subnet
  - subnet: 
    - `10.12.1.0/24`
  - Description:

      *Subnet for personal phone*
  - Devices to be joined:
      - personal cell(s)
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

## user1 subnet
  - subnet: 
    - `10.13.1.0/24`
  - Description:

      *Subnet for all user1 devices*
  - Devices to be joined:
      - personal cell(s)
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
## non-personal subnet
  - subnet: 
    - `10.14.1.0/24`
  - Description:

      *Subnet for all corporate issued devices*
  - Devices to be joined:
      - work cells, laptops 
  - WLAN:
      - yes, 5Ghz 
  - Egress subnet(s):
      - WAN
          - allow all
      - homeauto front end:
          - allow service ports (80 & 443)
  - Ingress subnet(s):
      - Management VPN
       


# Homelab subnets
## hypervisor management subnet
  - subnet: 
    - `10.21.1.0/24`
  - Description:

      *Subnet to access hypervisor management*
  - Devices to be joined:
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
## hypervisor backup subnet
  - subnet: 
    - `10.22.1.0/24`
  - Description:

      *Subnet for proxmox backups. Only accessible to VE and backup instances*
  - Devices to be joined:
      - hypervisors, backup server
  - WLAN:
      - no
  - Egress subnet(s):
      - hypervisor management
        - allow all
  - Ingress subnet(s):
      - management VPN
      - hypervisor management
## Services subnet
  - subnet: 
    - `10.23.1.0/24`
  - Description:

      *Subnet where services will be located*
  - Devices to be joined:
      - Service VMs
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

## Database subnet
  - subnet: 
    - `10.24.1.0/24`
  - Description:

      *Subnet where service DBs will be located*
  - Devices to be joined:
      - DB VMs 
  - WLAN:
      - no
  - Egress subnet(s):
      - WAN
          - allow all
      - Hypervisor backup
          - allow all
  - Ingress subnet(s):
        - Management VPN
        - Services 
        - Hypervisor management 

## homeauto front end subnet
  - subnet: 
    - `10.25.1.0/24`
  - Description:

      *Subnet where services will be located*
  - Devices to be joined:
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


# VPN subnets
## Management VPN
  - subnet: 
    - `10.31.1.0/24`
  - Description:

      *Subnet for management. Not used normally, only for administration. All ports and ips.*
  - Type of devices to be joined:
      - all personal devices, whether remote or local, when admin network access is required
  - Egress subnet(s):
      - Allow all on all subnets except:
        - iot camera
        - control tablet
  - Ingress subnet(s):
      - none

## user0 vpn
  - subnet: 
    - `10.32.1.0/24`
  - Description:

      *Subnet for remote access for user0's devices. Service and std management ports.*
  - Devices to be joined:
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

## user1 vpn
  - subnet: 
    - `10.33.1.0/24`
  - Description:

      *Subnet for remote access for user1's devices. Service ports only*
  - Devices to be joined:
      - all of user1's mobile devices
  - Egress subnet(s):
      - WAN
          - allow all
      - services subnet:
          - allow only specific service ip:ports
  - Ingress subnet(s):
      - Management VPN
        

# Restricted access subnets


## iot pi
  - subnet: 
    - `10.91.1.0/24`
  - Description:

      *Subnet for iot endpoints running a full os stack*
  - Devices to be joined:
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

## iot uc
  - subnet: 
    - `10.92.1.0/24`
  - Description:

      *Subnet for iot endpoints running only microcontroller firmware*
  - Devices to be joined:
      - microcontrollers 
  - WLAN:
      - yes, 2.4Ghz non-isolated
  - Egress subnet(s):
      - iot pi
          - allow all
  - Ingress subnet(s):
      - iot pi
      - privileged & vpn subnets
      - services subnet

## IOT camera
  - subnet: 
    - `10.93.1.0/24`
  - Description:

      *Subnet for internet-based cameras*
  - Devices to be joined:
      - iot cameras
  - WLAN:
      - yes, 2.4Ghz isolated
  - Egress subnet(s):
      - WAN
          - allow all
  - Ingress subnet(s):
      - none

## Streaming devices
  - subnet: 
    - `10.94.1.0/24`
  - Description:

      *Subnet for streaming devices*
  - Devices to be joined:
      - chrome
  - WLAN:
      - yes, 2.4Ghz isolated
  - Egress subnet(s):
      - WAN
          - allow all
  - Ingress subnet(s):
      - none

## Control tablet
  - subnet: 
    - `10.99.99.0/24`
  - Description:

      *Subnet that only allows access to a homeauto frontend*
  - Devices to be joined:
      - old EOL android tablets
  - WLAN:
      - yes, 2.4Ghz isolated
  - Egress subnet(s):
      - homeauto front end subnet 
          - only 80/443
          - only to specified server IP
  - Ingress subnet(s):
      - none