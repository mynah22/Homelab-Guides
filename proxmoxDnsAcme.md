# PFsense WebConfigurator ACME SSL certs via DNS validation 
- ## Article type: how-to
  This guide is a how-to guide for producing SSL certs for the proxmox web portal using the ACME protocol via DNS validation

# Requirements
1. Domain owned by you, with a DNS provider that supports DNS ACME validation 
2. API tokens / credentials configured (see [name.com](namecomDnsApi.md) instructions)
3. Able to resolve target proxmox host by hostname

# Instructions 
1. Login to proxmox
2. click 'datacenter' (not a node)>acme. Under accounts click 'add'
   
   ![](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/proxAcme0.jpg)
3. enter a nickname for this acct, your email address, and which ACME service to use. Check 'accept TOS' and click register ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/proxAcme1.jpg))
4. close the window after acct creation succeeds ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/proxAcme2.jpg))
5. under challenge plugins, click 'add'. Give it an ID (name), select the appropriate api, and fill the api data as required (name.com api / data in example). (screenshots [1](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/proxAcme3.jpg) [2](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/proxAcme4.jpg))
6. click a node you would like to produce SSL certs for. Then system >certificates, and click 'add' under ACME ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/proxAcme5.jpg))
7. Select DNS challenge type, the plugin you configured above, and the FQDN for your proxmox node, click create ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/proxAcme6.jpg))
8. click 'order certificates now' ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/proxAcme8.jpg))
8. your order should process fairly quickly. After it succeeds, the webgui will reload with the new cert ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/proxAcme7.jpg))
   - *you must connect to your proxmox host **BY FQDN** for the certificate to be trusted. This is because Let's Encrypt has only signed a certificate valid only for that FQDN, nothing else.*