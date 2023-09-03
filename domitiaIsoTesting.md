# testing wlan isolation features on the netgate r6400

## test conditions
- inter SSID AP comms
- cross SSID comms
- comms with device on port w/same vlan as SSID
- 


## desired end results
- SSID that allows comms with all members within the SSID (iot devices, user0)
  - Including wired devices on the same vlan 
- SSID that allows no comms between members within the SSID / LAN ports

## potential trunk solution
1. follow WAP instructions
2. create vap
3. create vlan (3 or above for safety), trunk to physical port
4. create bridge
5. add cpuport, vlan and vap interface to new bridge


## how to test iso
1. can't ping ip on same ssid
  - in iso : can't ping other SSID devices. Router, wap visible
  - in iso, firewall allow: still can't ping other SSID devices
  - non-iso: can ping connected devices
  - both: CAN ping wired devices on same vlan
2. can't see wlan packets of other nodes on same ssid
  - in iso: can't see any packets originating from SSID, including broadcast
  - non-iso: can only see direct comms and broadcast packets
3. can't ping ip on vlan bridged with ssid
  - in iso: CAN ping 
  - non-iso: can ping
4. can't see wlan packets of other nodes on vlan bridged with ssid
  - in iso: CAN see broadcast / direct comms. Can't see other packets
  - non-iso: CAN see broadcast / direct comms. Can't see other packets
5. can't ping ip on same frequency different bridge
  - non-iso, firewall allow: CAN ping
  - non-iso, firewall block: can't ping
  - dest iso, firewall allow: CAN ping
  - dest iso, firewall block: can't ping
  - source iso, firewall allow: CAN ping

## confirm non-iso
1. can access service on same ssid
2. can't ping ip on same frequency
3. cant ping ip with different frequency