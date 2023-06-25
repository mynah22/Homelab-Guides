bios settings:
    - Advanced > system options, enable:
        1. Turbo boot
        2. hyperthreading
        3. vtx
        4. vtd

Proxmox:
    - open shell:
        1. `nano /etc/systemd/logind.conf`
        2. uncomment the `HandleLidSwitch` line
        2. change it to `HandleLidSwitch=ignore`
        1. ctrl-s, ctrl-x to save and exit
        1. `systemctl restart systemd-logind` to restart that service
