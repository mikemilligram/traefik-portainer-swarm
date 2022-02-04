# traefik-portainer-swarm

## Quick Start
Before deploying the stack, make sure the required volumes and networks exist by executing these commands on the manager node:

    docker network create -d overlay traefik-swarm
    docker network create -d overlay agent-network
    docker volume create portainer-data
    docker volume create traefik-certificates

Next be sure to replace the variables in the `traefik-portainer.yml` file with your real values, or enter them into a .env file in the same directory as the file.

The following command then deploys the stack and makes it available for all your services to use:
    
    docker stack deploy -c <(docker-compose -f traefik-portainer.yml config) traefik-portainer
    
Once you log into your portainer instance at portainer.your-domain.com, navigate to the environments section and add an environment with the environment type `Agent` and enter  `tasks.agent:9001` for the environment URL.

Now you are good to go!

## Using other services with traefik
To make any service use traefik, add it to the `traefik-swarm` network and add the following to your service's configuration under the `labels` tag:

    - traefik.enable=true
    - traefik.docker.network=traefik-swarm
    - traefik.http.routers.my-service-https.entrypoints=http
    - traefik.http.routers.my-service-http.rule=Host(`service.your-domain.com`)
    - traefik.http.services.my-service.loadbalancer.server.port=your-port-here
      
Change `service.your-domain.com` to the FQDN you want your service to be accessible under.

`your-port-here` has to match the port your service is available at.

Additionally, if you want to use https, add:

    - traefik.http.routers.my-service-https.entrypoints=https
    - traefik.http.routers.my-service-https.rule=Host(`service.your-domain.com`)
    - traefik.http.routers.portainer-https.tls=true
    - traefik.http.routers.my-service-https.tls.certresolver=le
    - traefik.http.routers.my-service-http.entrypoints=https-redirect

If you need http authentication, add:

    - traefik.http.routers.my-service-http.middlewares=auth 

<u>OR</u>, if you are using https, add:

    - traefik.http.routers.my-service-https.middlewares=auth
      
