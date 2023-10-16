# Kali via Windows Subsystem for Linux

- Article type: how to

    This guide describes how to setup Kali in Windows Subsystem for Linux

    It covers guest and host configuration, as well as Kex (guest > host UI) usage, and network bridging

## Contents

## Prerequisites

- Windows 11
- virtualization enabled in BIOS

## Installing WSL

1. Enable Windows Features (control panel applet)

    - containers
    - Hyper-v (expand and select all!!)
    - WSL

2. Reboot

## Installing / configuring Kali

- In windows terminal:

  - `wsl --install -d kali-linux`
  - Set kali username / password
  - you will be presented with a kali prompt (zsh shell)
  - enter:

    1. `sudo apt update -y; sudo apt upgrade -y`
    2. `sudo apt install kali-win-kex -y; sudo apt install kali-linux-large -y`
    3. You will be prompted with some package options:
        - randomize MAC: your choice.

            I left mine unrandomized (so I can easily ID the device when not testing)

            I only initiate a random mac as needed.

        - Kismet: non -sudo mode, enter your username
        - dumpcap: non sudo mode
    4. it will take a while for all packages to download / install. When done you will be at the ZSH prompt

## Configure network bridge

1. open Hyper-V Manager (As ADMIN)
2. select server on the left (your computer's hostname)
3. under 'Actions' on the right, select 'Virtual Switch Manager'
4. Select 'WSL' under 'Virtual Switches' on the right
5. Under 'Connection Type', click the 'External switch' radio button, and select the NIC you want kali on
6. Leave 'allow management operating system to share this network adapter' checked
7. Apply

## Kex usage

1. install wsl vm
2. kex
3. add packages
