# docker-systemd
The docker containers with systemd support

## Introduction

The systemd is not supported by default in official docker images.

For example, users will get the following error when start `ssh` service using systemctl in docker container
```
root@f3ff816528f4:/# systemctl restart ssh 
System has not been booted with systemd as init system (PID 1). Can't operate.
Failed to connect to bus: Host is down
```

To fix the issue, some changes are needed when build the docker images.
- For rockylinux, almalinux and oraclelinux, refer to https://hub.docker.com/r/rockylinux/rockylinux
- For ubuntu24 and debian12, refer to https://blog.cloudabc.eu/linux/container/2024/10/27/ubuntu-docker-container-systemd/

## Build the docker images

If you use a non-root user, please add `sudo` before the commands.
```
docker build . --file Dockerfile.ubuntu24 --tag weizhouapache/docker-systemd:latest-ubuntu24
docker build . --file Dockerfile.debian12 --tag weizhouapache/docker-systemd:latest-debian12
docker build . --file Dockerfile.alma9 --tag weizhouapache/docker-systemd:latest-alma9
docker build . --file Dockerfile.oracle9 --tag weizhouapache/docker-systemd:latest-oracle9
docker build . --file Dockerfile.rocky9 --tag weizhouapache/docker-systemd:latest-rocky9
```

You can find the docker images ready for use on:
https://hub.docker.com/repository/docker/weizhouapache/docker-systemd/

## Create docker containers

Please note, the container name, hostname, network name (`net`) and ip address (`ip`) are optional.

### Rocky9/Alma9/Oracle9 containers

    docker run -it -d \
        --privileged \
        --volume=/sys/fs/cgroup:/sys/fs/cgroup:rw \
        --cgroupns=host \
        --name alma9 \
        --hostname=alma9 \
        --net=<network name> \
        --ip=<ip address> \
        weizhouapache/docker-systemd:latest-alma9

### Ubuntu24/Debian12 containers

    docker run -it -d \
        --privileged \
        --name ubuntu24 \
        --hostname=ubuntu24 \
        --net=<network name> \
        --ip=<ip address> \
        weizhouapache/docker-systemd:latest-ubuntu24

## Access the docker containers

Users can access the docker containers from the host by
```
docker exec -it ubuntu24 bash
docker exec -it alma9 bash
```

The `openssh-server` package has been installed in all containers, users can enable ssh by
```
echo "root:password" |chpasswd
echo "PermitRootLogin yes" >>/etc/ssh/sshd_config
systemctl enable ssh || systemctl enable sshd
systemctl restart ssh || systemctl restart sshd
```
