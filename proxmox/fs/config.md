### Chosen Parameters

```shell
zpool set autotrim=on jankenpool
#^ Tell the SMR disk about free space so it doesn't shuffle data all willy nilly.
```

### Resetting Parameters

Nothing special for recordsize or sync.
I tried 1M and disabled, but defaults of 128K and standard work just as well.

zpool inherit -S recordsize jankenpool
zpool inherit -S sync jankenpool

### Swap

https://forum.proxmox.com/threads/new-installation-system-raid1-how-to-create-swap.103157/post-470250

```shell
zfs set atime=off swapool
zpool set autotrim=on swapool
zfs create -V 24G -b $(getconf PAGESIZE) \
  -o logbias=throughput -o sync=always -o primarycache=metadata \
  -o com.sun:auto-snapshot=false swapool/swapfile
mkswap -f /dev/zvol/swapool/swapfile
swapon /dev/zvol/swapool/swapfile
echo '/dev/zvol/swapool/swap none swap discard 0 0' >> /etc/fstab
```