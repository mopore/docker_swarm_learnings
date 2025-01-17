# Docker Swarm Learnings

This projects documents the learnings from the Docker Swarm tutorial by Mark Takacs.
https://takacsmark.com/docker-swarm-tutorial-for-beginners/
https://www.youtube.com/watch?v=3-7gZS4ePak&t=1950


## Initiate a Docker Swarm

A machine with a running docker daemon can easily be made to a docker swarm manager or member.

Making the current docker instance a docker swarm manager:  `docker swarm init`
It might be necessary to provide a network adapter to create a swarm.
`docker swarm init --advertise-addr eth1`


 
## Wording

Word | Description
--- | ---
Node | A 'manager' oder 'worker' of the swarm
Stack | The application stack you deploy with a docker-compose.yml file
Service | The scalable items defined as a 'service' in the docker-compose.yml file
Task | (1:1 reservation for a container)
Container | A container as known on from the docker base level

## Commands

### Deploy and Undeploy
Deploying a sample application (as a stack) in the swarm:
```
docker stack deploy -c docker-compose.yml example_app
```

To update simply redeploy.

To undeploy
```
docker stack rm example_app
```

### List entities

List all nodes (from manager node)
```
docker node ls
```

List deployed applications (as stacks) in the swarm:
```
docker stack ls
```

List the services in a particular stack
```
docker stack services example_app
```

List all services deployed with swarm
```
docker service ls
```

List the tasks (1:1 container reservations) of a stack
```
docker stack ps example_app
```

### Scale a service at Runtime

To scale a particular serivce (use the name provided by the command `docker service ls`).
```
docker service scale example_app_my-nginx=4
```


### Leaving the Swarm
```
docker swarm leave -f
```
This will remove everything deployed.




## Example docker docker-compose.yml to deploy a stack

```
version: '3'
services:
  my-nginx:
    image: nginx:latest
    ports:
      - "8080:80"
    networks:
      - mynet
    deploy:
      # replicas: 2
      resources:
        reservations:
          memory: 100m
        limits:
          memory: 300m
      restart_policy:
        condition: any
      # update_config:
      #   parallelism: 2
      #   delay: 10s

networks:
  mynet:
```


https://linuxhint.com/docker_compose_memory_limits/