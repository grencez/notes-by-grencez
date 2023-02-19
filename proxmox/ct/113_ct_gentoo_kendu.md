# kendu
Container based on Gentoo template for running Kubernetes (k3s).

## Everything Done

### Generic
```shell
# We have readonly repositories in /var/db/repos/, so no need to `emerge --sync`.
# Just need to tell the system that my personal repo exists there.
mkdir -p /etc/portage/repos.conf
printf '[grencez]\nlocation = /var/db/repos/grencez\n' >/etc/portage/repos.conf/grencez.conf

# Initial update.
emerge -u1 sys-apps/portage
emerge -uDN world
# Update configs (requires human).
dispatch-conf
# Clean up.
eselect news read all
emerge --depclean

```

### Install
```shell
# For k3s and podman.
echo '*/* rootless' >> /etc/portage/package.use/00_world
echo 'sys-cluster/k3s' >> /etc/portage/package.accept_keywords/00_world
echo 'app-misc/yq' >> /etc/portage/package.accept_keywords/01_k3s_dep
emerge sys-cluster/k3s
emerge sys-cluster/podman
emerge app-containers/containerd
emerge sys-fs/fuse-overlayfs
```

### User
```shell
useradd --user-group --create-home --shell /bin/false --uid 12345 user-for-k3s
echo 'command_args="server --rootless --snapshotter=fuse-overlayfs"' >>/etc/conf.d/k3s
echo 'command_user="user-for-k3s:user-for-k3s"' >>/etc/conf.d/k3s
 #--feature-gates=KubeletInUserNamespace=true
 
mkdir -p /etc/sysctl.d
echo kernel.unprivileged_userns_clone=1 >>/etc/sysctl.d/99-rootless-k3s.conf
echo net.ipv4.ip_forward=1 >>/etc/sysctl.d/99-rootless-k3s.conf
#echo net.ipv4.ping_group_range = 0 2147483647 >>/etc/sysctl.d/99-rootless-k3s.conf

cat /etc/subuid
cat /etc/subgid
# Both should look like:
#  user-for-k3s:100000:65536
#echo user-for-k3s:100000:65536 >>/etc/subuid
#echo user-for-k3s:100000:65536 >>/etc/subgid
# `cat /proc/self/uid_map` should give more than 65536 as the last number.

# I had to make proxmox host /etc/subuid and /etc/subgid look like:
#   root:100000:200000
chown 100000:100000 /dev/net/tun

rc-config add k3s default
# reboot

ln -s /home/user-for-k3s/.rancher/k3s/agent /home/user-for-k3s/.rancher/k3s/server/agent

```

tun https://pve.proxmox.com/wiki/OpenVPN_in_LXC
https://kubernetes.io/docs/tasks/administer-cluster/kubelet-in-userns/

https://techviewleo.com/install-kubernetes-on-alpine-linux-with-k3s/
https://docs.k3s.io/installation/configuration#configuration-file

/etc/local.d/k3s.start
```
#!/bin/sh -e

# Kubeadm 1.15 needs /dev/kmsg to be there, but it's not in lxc, but we can just use /dev/console instead
# see: https://github.com/kubernetes-sigs/kind/issues/662
if [ ! -e /dev/kmsg ]; then
    ln -s /dev/console /dev/kmsg
fi

# https://medium.com/@kvaps/run-kubernetes-in-lxc-container-f04aa94b6c9c
mount --make-rshared /
```
chmod +x /etc/local.d/k3s.start

https://gist.github.com/triangletodd/02f595cd4c0dc9aac5f7763ca2264185?permalink_comment_id=3687487
https://wiki.gentoo.org/wiki/OpenRC/CGroups#CGroups_version_2
edit /etc/rc.conf


### want

k3s server --rootless --snapshotter=fuse-overlayfs --debug

WARN[0000] cannot set cgroup2 evacuation, make sure to run k3s as a systemd unit 
FATA[0000] failed to evacuate root cgroup: mkdir /sys/fs/cgroup/init: permission denied 
FATA[0004] failed to setup UID/GID map: newuidmap 1261 [0 12345 1 1 100000 65536] failed: newuidmap: open of uid_map failed: Permission denied
