# PFsense WebConfigurator ACME SSL certs via DNS validation 
- ## Article type: how-to
  This guide is a how-to guide for producing SSL certs for the pfSense web portal using the ACME protocol via DNS validation

# Requirements
1. Domain owned by you, with a DNS provider that supports DNS ACME validation 
2. API tokens / credentials configured (see [name.com](namecomDnsApi.md) instructions)

# Instructions 
1. Login pfSense
2. system>Package Manager > Available Packages> search 'acme'> click install, then confirm (screenshots [1](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/pfsenseAcme0.jpg) [2](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/pfsenseAcme1.jpg) [3](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/pfsenseAcme2.jpg))
3. services>acme certificates>account keys>add ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/pfsenseAcme3.jpg))
4. fill in the name and description. Then select the Let's Encrypt prod server. Enter your Email address (for automatic cert expiration notice), then click 'create new account key' ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/pfsenseAcme4.jpg))
5. Then click 'register acme account key', then save ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/pfsenseAcme5.jpg))
6. services>acme certificates>Certificates > add ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/pfsenseAcme6.jpg))
7. cert add screen
   - name and description as you like
   - make sure you are using the account you just configured
   - domainname: set to the FQDN of pfsense (not visible in screenshot below)
   - Method: select the DNS validation method for your provider (name.com in my example)
   - name.com specific fields (this will be different if you have another provider)
     - name.com username: NOT your email. The username listed on your api token (see name.com account settings > api tokens)
     - name.com api token: token info from above
   - click save at bottom of page