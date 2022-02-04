# traefik-portainer-swarm
To make any service use traefik, add it to the `traefik-swarm` network and add the following to your service's configuration:

    labels:
      # ...
      - traefik.enable=true
      - traefik.docker.network=traefik-swarm
      - traefik.http.routers.my-service-https.entrypoints=http
      - traefik.http.routers.my-service-http.rule=Host(`service.your-domain.com`)
      - traefik.http.services.my-service.loadbalancer.server.port=80
      
Change `service.your-domain.com` to the FQDN you want your service to be accessible under.

Edit the port according to your service's available port.

Additionally, if you want to use https, add:

    labels:
      # ...
      - traefik.http.routers.my-service-https.entrypoints=https
      - traefik.http.routers.my-service-https.rule=Host(`service.your-domain.com`)
      - traefik.http.routers.portainer-https.tls=true
      - traefik.http.routers.my-service-https.tls.certresolver=le
      - traefik.http.routers.my-service-http.entrypoints=https-redirect

If you need http authentication, add:

    labels:
      # ...
      - traefik.http.routers.my-service-http.middlewares=auth 

<u>OR</u>, if you are using https, add:

    labels:
      # ...
      - traefik.http.routers.my-service-https.middlewares=auth
      
