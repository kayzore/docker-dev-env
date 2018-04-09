# docker-dev-env

Simple Docker dev environment with Portainer and Traefik tools enabled.

## Requirements

Install :
- Docker
- Docker-compose

## Starting

- Clone this repo.
- Go to the cloned repository with cd /path/to/repo
- Execute ``` docker-compose up -d ``` 

  
```http://portainer.localhost/``` is now available, you can easily manage your containers/images/volumes/networks docker with this tool.

```http://traefik.localhost/``` is now available, you can easily view your frontend rules with this tool.

## Usage example with PHP, apache, mysql and phpMyAdmin
```yaml
version: "3"
services:
  apache:
    # Image, Build, container_name, etc... Then:
    labels:  
      - "traefik.enable=true" # Enabling Traefik for this rule
      - "traefik.backend=apache" # Traefik custom backend name
      - "traefik.frontend.rule=Host:myapp.localhost" # Traefik url
      - "traefik.port=80" # Traefik backend port, refer to service exposed port
      - "traefik.docker.network=traefik_webgateway" # Need to specify the docker network because this service has multiple networks
    networks:
      - webgateway # The Traefik network
      - backend # The app network

  php:
    # Image, Build, container_name, etc... Then:
    depends_on:  
      - apache  
      - mysql  
    networks:
      - backend

  mysql:
    # ...
    networks:
      - backend

  pma:
    # Image, Build, container_name, etc... Then:
    labels:
      - "traefik.enable=true"
      - "traefik.backend=pma"
      - "traefik.frontend.rule=Host:pma.localhost"
      - "traefik.port=80"
      - "traefik.docker.network=traefik_webgateway"
    depends_on:
      - mysql
    networks:
      - webgateway
      - backend

networks:
  backend: # The internal network of the app
    driver: bridge
  webgateway: # Traefik network is a external network, need to specify that
    external:
      name: traefik_webgateway
```

## Documentation links
- How to use Portainer ? See the [Portainer documentation](https://portainer.readthedocs.io/en/stable/)
- How to use Traefik ? See the [Traefik documentation](https://docs.traefik.io/)
