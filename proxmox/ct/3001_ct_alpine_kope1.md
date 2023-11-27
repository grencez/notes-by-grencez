
```shell
# https://www.cyberciti.biz/faq/how-to-upgrade-alpine-linux-3-4-to-3-5-xx/
apk update
apk upgrade
sed -i -E -e 's:/alpine/v[^/]*/:/alpine/v3.18/:' /etc/apk/repositories
apk update
apk upgrade --available && sync
reboot

# https://gist.github.com/danmack/53eaea4353d6b3dcfd20238741557290
apk add podman util-linux
apk add fuse-overlayfs
apk add containerd
apk add k3s


busybox adduser -D -s /bin/false -u 12345 -g user-for-k3s user-for-k3s
echo 'permit nopass nolog root' >>/etc/doas.d/doas.conf
echo user-for-k3s:200000:65536 >>/etc/subuid
echo user-for-k3s:200000:65536 >>/etc/subgid
echo 'K3S_OPTS="--rootless --snapshotter=fuse-overlayfs ${K3S_OPTS}"' >>/etc/conf.d/k3s
echo 'command_user="user-for-k3s:user-for-k3s"' >>/etc/conf.d/k3s
sed -i -E -e 's/root:root/${command_user}/' /etc/init.d/k3s
# https://kubernetes.io/docs/tasks/administer-cluster/kubelet-in-userns/

#mkdir -p /etc/sysctl.d
echo net.ipv4.ip_forward=1 >>/etc/sysctl.d/99-rootless-k3s.conf
rc-update add sysctl boot

rc-update add cgroups default
rc-update add podman default
rc-update add k3s default
# rc-service k3s start


cat >/etc/local.d/k3s.start <<EOF
#!/bin/sh -e

# Kubeadm 1.15 needs /dev/kmsg to be there, but it's not in lxc, but we can just use /dev/console instead
# see: https://github.com/kubernetes-sigs/kind/issues/662
if [ ! -e /dev/kmsg ]; then
    ln -s /dev/console /dev/kmsg
fi

# https://medium.com/@kvaps/run-kubernetes-in-lxc-container-f04aa94b6c9c
#mount --make-rshared /
EOF
chmod +x /etc/local.d/k3s.start

```
