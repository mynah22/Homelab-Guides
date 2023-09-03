1. follow process in [ddwrt WAP guide](/ddwrtWAP.md) for initial device configuration
2. create vap
    - create VAP
    - set wireless security
3. create vlan (3 or above for safety), trunk to physical port
    - setup > switch config
    - enable vlans, apply
    - reload, enable, apply
    - remove 2 from everything (internal vlan used by firmware for WAN)
    - add vlan row
    - set trunk port, tagged with trunked vlans (add 1 if you want web management)
    - leave pc plugged into a port on vlan 1 (default)
    - administration > reboot router
    - reconnect
    - setup > switch config, add new VLAN to CPUPORT 
      - *CPUPORT needed in order to add to the vlan to the bridge.*
4. create bridge
    - STP off
    - add two assignments: target wireless interface and vlan
    - apply settings, ensuring bridging table looks correct
5. add vlan and vap interface to new bridge
6. setup>networking>interface setup : wan port disable
7. interface macs