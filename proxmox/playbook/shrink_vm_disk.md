
```shell
#> Repartition / resize in VM.
#^ I like using GParted.

zfs set volsize=${desired_GiB}G ${zvol}
#^ Use ZFS directly to set volume size in GiB.

qm rescan
#^ Update Proxmox's view of reality.
```

### EXAMPLE
```shell
#> Reparation in the VM first! I like using the GParted livecd.

zfs set volsize=400G jankenpool/e/vm-106-disk-0

qm rescan
```
