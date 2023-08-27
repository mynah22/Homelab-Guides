# Traefik Basics

- ## Article type: reference
  this is a reference for my own usage of Traefik. It is by no means comprehensive, and I have not studied the fundamentals of Traefik, so my explanations of how it functions may be flawed. 

  

# example Docker compose

```
Version: "3.5"

services:

  traefik:
    image: traefik:latest
    container_name: traefik
    command:
      - "--providers.docker=true"
      - "--providers.docker.exposedbydefault=false"
      - "--entrypoints.web.address=:80"
      - "--entrypoints.websecure.address=:443"
      - "--certificatesresolvers.acmeCA.acme.dnschallenge=true"
      - "--certificatesresolvers.acmeCA.acme.dnschallenge.provider=namedotcom"
      - "--certificatesresolvers.acmeCA.acme.email=your@email.com"
      - "--certificatesresolvers.acmeCA.acme.storage=/acme/acme.json"

    environment:
      - "NAMECOM_USERNAME=yourTOKENusername"
      - "NAMECOM_API_TOKEN=164961616deadbeef4694611ae6161d616d61e"

    restart: always 
    ports:
      - 80:80
      - 443:443

    labels:
      - traefik.enable=true
      # HTTPS Redirect
      - "traefik.http.routers.http-catchall.rule=Host(`bitwarden.yourdomain.tld`)"
      - "traefik.http.routers.http-catchall.entrypoints=web"
      - "traefik.http.routers.http-catchall.middlewares=redirect-to-https@docker"
      - "traefik.http.middlewares.redirect-to-https.redirectscheme.scheme=https"
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock:ro
      - ./acme:/acme
    networks:
      - bitwarden-external

networks:
  bitwarden-external:
    name: bitwarden-external
```

# DNS Acme Validation
- Note: Traefik uses the Lego certificate generation library. The Lego docs can be particularly helpful for troubleshooting DNS validation issues.  

```
command:
      - "--certificatesresolvers.acmeCA.acme.dnschallenge=true"
      - "--certificatesresolvers.acmeCA.acme.dnschallenge.provider=namedotcom"
      - "--certificatesresolvers.acmeCA.acme.email=your@email.com"
      - "--certificatesresolvers.acmeCA.acme.storage=/acme/acme.json"
```
- DNS challenge for acme certificateresolver set to true
- DNS api provider set to name.com
- email is your email (notification of expiring certs)
- storage location for acme certs specified

```
    environment:
      - "NAMECOM_USERNAME=yourTOKENusername"
      - "NAMECOM_API_TOKEN=164961616deadbeef4694611ae6161d616d61e"
```
- For name.com API:
- `NAMECOM_USERNAME` : NOT your email. This is the token username, check the api token page on name.com
- Check Lego docs for environment vars needed for other providers. 

# HTTPS redirect
