- Added `fuse` feature for the CT in Proxmox.

```shell
echo 'https://dl-cdn.alpinelinux.org/alpine/edge/testing' >> /etc/apk/repositories
apk update
apk upgrade
apk add fuse-overlayfs docker minikube

#docker-cli-compose
sed -i -e 's:.*DOCKER_OPTS=.*:DOCKER_OPTS="--storage-driver=fuse-overlayfs":' /etc/conf.d/docker
rc-update add docker boot

adduser -D -s /bin/false -u 12345 user-for-minikube
adduser user-for-minikube docker

reboot

echo 'permit nopass nolog root' > /etc/doas.d/root.conf
doas -u user-for-minikube minikube start --driver=docker
```