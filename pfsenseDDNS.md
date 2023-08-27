# Configure Dynamic DNS in pfSense
- ## Article type: how-to
  This guide is a how-to guide for configuring DDNS in pfsense, using Name.com as an example provider

# Requirements
1. Domain name owned by you, with a DNS provider that supports dynamic DNS records
2. Token / credentials configured for API access (See [name.com] api token set up example if you use that provider)
3. typically, an A record for the target hostname / domain created on your DNS provider

# Instructions
1. services > dynamic DNS > Dynamic DNS clients, click add ([screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/pfsenseDdns1.jpg))
2. client config page
   - *Note, the example below is **ONLY for name.com DNS service**. Although the details of where to place your logon credentials varies widely between providers, all edge cases should be covered by the text under the 'hostname', 'username' and 'password' sections, just read the page on pfsense closely.*
   - select your dns provider from "service type" (name.com for this example)
   - enter hostname, username and password as approriate for your provider. In the example of name.com:
     - hostname: host in one field, domain in another (ie hostname | domainName.com)
     - username: your TOKEN's username (see api token info on name.com)
     - password: api key
   ![screenshot](https://github.com/mynah22/Homelab-Guides/raw/main/screenshots/pfsenseDdns2.jpg)
   - scroll to bottom, save
 3. you should see a green cached IP in the Dynamic DNS client screen. You should also see the A record update in your DNS provider