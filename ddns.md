Namecheap ddns

namecheap:
    - Account > settings > api tokens > generate new token
    - *username will take a moment to populate*
    - create A record 
pfsense:
    - services > dynamic DNS > add
    - service type: name.com
    - interface to monitor: WAN
    - hostname : A record created at name.com
    - domain: domain you own (yourdomain.abc)
    - username: username in token *takes a few minutes to generate*
    - password: api token 
    - save & force reload