Adding another part to jankenpool.

```shell
zfs create jankenpool/c
pvesm add zfspool jankenpool_c -pool jankenpool/c
```

Now in the Proxmox UI, add a new mount point `/var/lib/docker/fuse-overlayfs` that uses `jankenpool_c` for storage.
Just 32 GiB for now.
With the `noatime` option as usual.

Also, in the Proxmox UI, change the former `/var/lib/docker` mount point to be `/var/lib/docker/volumes`.

Next, move the data around for those mount points to make sense.

```shell
# Move images to new subvol.
mv /jankenpool/e/subvol-111-disk-1/fuse-overlayfs/* /jankenpool/c/subvol-111-disk-0/
# Move Docker data to main OS subvol.
mv /jankenpool/e/subvol-111-disk-1/* /jankenpool/e/subvol-111-disk-0/
# Move the volumes back.
mv /jankenpool/e/subvol-111-disk-0/volumes/* /jankenpool/e/subvol-111-disk-1/
```

Now configure snapshots and backups.
- Add `jankenpool/c` section in `/etc/sanoid/sanoid.conf`.
- Add `jankenpool/c` backup in `/etc/cron.daily/syncoid.conf`

We should verify that snapshots and backups are working.
```shell
# When will snapshot run next?
systemctl list-timers sanoid.timer

# This should show a snapshot after sanoid runs.
zfs list -t snapshot jankenpool/c/subvol-111-disk-0

# This should show snapshots tomorrow.
zfs list -t snapshot vampool/backup/jankenpool_c/subvol-111-disk-0
```