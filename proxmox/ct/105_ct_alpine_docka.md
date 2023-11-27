# docka

### External
- Add `fuse` feature for the CT in Proxmox. The `nested` feature should already be on.
- 
- Mounted `/opt` as its own drive.
- Mount `/var/lib/docker/volumes` and `/var/lib/docker/fuse-overlayfs` as their own drives.

### Package Setup
```shell
#setup-alpine -q
apk update
apk upgrade
apk add fuse-overlayfs docker docker-cli-compose
sed -i -e 's:.*DOCKER_OPTS=.*:DOCKER_OPTS="--storage-driver=fuse-overlayfs":' /etc/conf.d/docker
rc-update add docker boot
reboot
```

```shell
mkdir -p /opt/compose /opt/dockge
cd /opt/dockge
wget https://raw.githubusercontent.com/louislam/dockge/master/compose.yaml
sed -i -e 's:/opt/stacks:/opt/compose:g' compose.yaml
docker compose up -d
```