# Docker

## Rancher Desktop
- Use Rancher Desktop because Docker Desktop requires a paid license for large companies.
  - https://docs.rancherdesktop.io/

## Commands
- Get Docker images
  - ```docker images```
- Remove Docker image
  - ```docker rmi <image_ID>```
  - ```docker rmi <repository>:<tag>```
- Build a docker image
  - ```docker build -t <image_tag>```
- Run a docker image in a container
  - ```docker run -t -dp:3000:3000 <image_tag>```
- Go inside the Docker container (for debugging)
  - ```docker exec -it 815f54f640b8 sh```
