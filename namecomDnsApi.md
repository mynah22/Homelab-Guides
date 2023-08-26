# Create Name.com DNS API keys
- ## Article type: how-to
  This guide is a how-to guide for creating DNS API keys for name.com
  
  This is entirely restricted to name.com. Other guides will show how to use these keys (in traefik, pfsense, proxmox etc) to produce ACME SSL certs

# Requirements
1. Domain owned by you on name.com

# Instructions 
1. Login your name.com account
2. Scroll all the way to the bottom, and click "name.com API for resellers" [link](https://www.name.com/resellersolutions) ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/nameDns0.jpg))
3. click 'start with an API token' ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/nameDns1.jpg))
4. enter a token name, then click 'generate new token' ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/nameDns2.jpg))
5. your first token might take a long time to populate!!! Your token is not valid until you see the username and token name filled in on this page
6. Once your username and token name are visible, your API token is ready for use