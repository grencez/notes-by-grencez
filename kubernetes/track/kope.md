# Kube

```shell
# https://gist.github.com/ruanbekker/fcc906bdcb2fed5937f7ce73a97e1001
apk add curl

echo "cgroup /sys/fs/cgroup cgroup defaults 0 0" >> /etc/fstab

cat >> /etc/cgconfig.conf <<EOF
mount {
cpuacct = /cgroup/cpuacct;
memory = /cgroup/memory;
devices = /cgroup/devices;
freezer = /cgroup/freezer;
net_cls = /cgroup/net_cls;
blkio = /cgroup/blkio;
cpuset = /cgroup/cpuset;
cpu = /cgroup/cpu;
}
EOF

reboot
# https://docs.k3s.io/quick-start
curl -sfL https://get.k3s.io | sh -
#curl -sfL https://get.k3s.io | K3S_URL=https://myserver:6443 K3S_TOKEN=mynodetoken sh -
```



- [alpine](https://ncrmro.com/posts/alpine-k3s)
- [rpi](https://docs.technotim.live/posts/multi-arch-k3s-rpi/)

- install on proxmox
- put stuff behind traefik
- install on desktop
- install on rpi