
Snapshot policies are in /etc/sanoid/sanoid.conf. 
Sanoid runs snapshots periodically /usr/lib/systemd/system/sanoid.{timer,service}; I changed it to run hourly instead of quarter-hourly.
Syncoid actually backs things up in /etc/cron.daily/syncoid

### Syncoid commands look like this:
```shell
syncoid -r --skip-parent jankenpool/e vampool/backup/sanoid_e
syncoid -r --skip-parent rpool/data vampool/backup/sanoid_rpool_data
syncoid -r --skip-parent rpool/ROOT vampool/backup/sanoid_rpool_ROOT
```