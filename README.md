# Mesos docker image

Base docker image with mesos.

## Build

Because of the size during the compilation is better to unlock the use of the swap space and remove all the unuseful stages. You can use the following commands:

```bash
# Remove all unusefull images
docker rmi $(docker images -f dangling=true -q)

# Build with no swap restrictions
docker build --memory-swap -1 -t mesos-image .
```
