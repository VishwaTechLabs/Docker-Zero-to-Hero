# Docker CLI Cheat Sheet

```bash
docker version
docker info
docker pull IMAGE
docker images
docker build -t NAME:TAG .
docker run -d --name NAME -p HOST:CONTAINER IMAGE
docker ps
docker ps -a
docker logs -f CONTAINER
docker exec -it CONTAINER sh
docker inspect CONTAINER
docker stop CONTAINER
docker start CONTAINER
docker rm CONTAINER
docker rmi IMAGE
docker network ls
docker network create NETWORK
docker volume ls
docker volume create VOLUME
docker compose up -d
docker compose down
docker compose ps
docker compose logs -f
docker stats
docker system prune
```
