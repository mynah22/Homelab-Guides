# Windows server VM on isolated network
This demonstration uses ESXI and PFSense to configure a windows server VM on an isolated network 

## Requirements
1. Completed the [Server & Hypervisor Setup Instructions](firstConfig.md) 
2. Completed the [PFSense Setup instructions](firstPFSense.md)
3. Windows server ISO on your PC

## **Build Windows server VM**
- ### **upload Windows Server ISO to ESXI** 

    1. Browse to the ESXI server IP, and click on 'storage' > datastore browser ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso1.jpg))

    2. Browse to the folder where you are storing your ISOs, and click upload. Select your copy of Windows Server ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso2.jpg))

    3. You will see the upload progress in the datastore browser ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso3.jpg))

    4. and you will see the file when it has uploaded successfully ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso4.jpg))

- ### **Create Virtual Machine**
    1. Virtual machines > create / register vm ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso0.jpg))
    2. create a new virtual machine ? next ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso5.jpg))
    3. Name- your choice. Guest OS family- Windows. Guest OS Version- Windows server 2016. Click 'Next' ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso6.jpg))
    4. Select the datastore for the VM's virtual hard drive, click 'Next' ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso7.jpg))
    5. CPU- 16, memory- 16MB (16384 MB), 100 GB hard drive, click 'Next ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso8.jpg))
    6. Review details, click 'Finish' ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/firstConfig/iso9.jpg))
