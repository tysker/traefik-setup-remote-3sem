## Errors

### Error respond from docker swarm
- If you get an error like this:  **"this node is not a swarm manager. Use "docker swarm init" or "docker swarm join" to connect this node to swarm and try again"** then you need to run the following command: docker swarm init

### Certificate not showing in letsencrypt folder

This is typically a problem with how the file is mounted into the Traefik container. Start Traefik and then go into
the Traefik container docker exec -it sh

Then navigate to where the acme file should be mounted. If you don’t see then somehow your local path is not allowing
the file to get mounted. So try moving acme to the same directory as the compose file and try again.

### scale is deprecated

Since version 3.2.0, the scale option is deprecated.
Instead of using scale use replicas in the docker-compose file. See the following link for more information: https://docs.docker.com/compose/compose-file/compose-file-v2/#scale

```YAML
version: "3.9"
services:
  redis:
    image: redis:latest
    deploy:
      replicas: 1
    configs:
      - my_config
      - my_other_config
```