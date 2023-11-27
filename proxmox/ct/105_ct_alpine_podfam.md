# podfam


## old minikube
```shell
#setup-alpine -q
apk update
apk upgrade
apk add fuse-overlayfs docker docker-cli-compose
# reboot

#echo 'https://dl-cdn.alpinelinux.org/alpine/edge/testing' >> /etc/apk/repositories
#apk add podman minikube
reboot
#apk add sudo
adduser -D -s /bin/false -u 12345 user-for-minikube
adduser user-for-minikube docker
#echo 'user-for-minikube ALL=(ALL) NOPASSWD: /usr/bin/podman' >/etc/sudoers.d/user-for-minikube
sudo -u user-for-minikube minikube start
```

## old k3s
```shell
apk add docker docker-cli-compose k3s
rc-update add docker boot
apk add k3s
sed -i -e 's/K3S_OPTS=.*/K3S_OPTS="--snapshotter=fuse-overlayfs --kubelet-arg=feature-gates=KubeletInUserNamespace=true"/' /etc/conf.d/k3s
#rc-config start k3s
#doesn't work
```

## old dockge
### External
- Mounted `/opt/compose` as its own drive.
- Configured static IPv4 address in router for the CT's MAC.
- Added `fuse` feature for the CT in Proxmox.

### Podman Setup
https://wiki.alpinelinux.org/wiki/Podman

```shell
## Need testing for podman-compose.
#echo 'https://dl-cdn.alpinelinux.org/alpine/edge/testing' >> /etc/apk/repositories

apk update
apk upgrade
# reboot maybe
apk add fuse-overlayfs
apk add podman-docker
#apk add podman-compose
# reboot maybe

#rc-update add cgroups
#podman system reset
```

```shell
mkdir -p /opt/dockge
cd /opt/dockge
wget https://raw.githubusercontent.com/louislam/dockge/master/compose.yaml
sed -i -e 's:/opt/stacks:/opt/compose:g' compose.yaml

#docker compose up -d
#podman-compose up -d
```