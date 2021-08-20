there are two major benefits 
- first is you can have a logical division of your data as well as the container 
- second thing is second benefit is you can preserve the data that once you remove the container your volume data will be present so after destroying the containers you can easily attach the volume to any existing containers as well as new containers, so your data will not be lost if you attach the volume to the containers , your data will be still be safe with the volumes even if you destroy the containers 

```shell
Commands:
  create      Create a volume
  inspect     Display detailed information on one or more volumes
  ls          List volumes
  prune       Remove all unused local volumes
  rm          Remove one or more volumes

```





```shell
docker volume ls
docker volume create <volumeName>
docker volume rm <volumeName>
docker volume prune
docker container create <containerName> --mount source=volumeName,target=/folderName
docker volume inspect
docker container inspect <containerName>

```






























