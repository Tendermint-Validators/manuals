# Add Data Disk Ubuntu 20.04.3 LTS

## Requirements ##
You must attach a new storage device to your Virtual Machine before starting with this manual.
Ensure that the storage device is either of type `SATA` or type `SCSI`.

### Rescan the SCSI adapters to detect new storage devices ###

You can skip this step if you have powered off your Virtual Machine before adding the new storage device.

```
# Use this oneliner to detect new storage devices
for H in $(ls /sys/class/scsi_host); do echo "- - -" > "/sys/class/scsi_host/$H/scan"; done
```

If all goes well your new storage device should now be present.
The last lines of output from the command `dmesg` should look similar to the example below.
```
[12177.369254] sd 32:0:1:0: Attached scsi generic sg2 type 0
[12177.369637] sd 32:0:1:0: [sdb] 41943040 512-byte logical blocks: (21.5 GB/20.0 GiB)
[12177.369681] sd 32:0:1:0: [sdb] Write Protect is off
[12177.369683] sd 32:0:1:0: [sdb] Mode Sense: 31 00 00 00
[12177.369725] sd 32:0:1:0: [sdb] Cache data unavailable
[12177.369727] sd 32:0:1:0: [sdb] Assuming drive cache: write through
[12177.408439] sd 32:0:1:0: [sdb] Attached SCSI disk
```

### Create Physical Volume ###
We **do not** create a partition on the new storage device. Doing so will set bounds to the physical volume
that we are about to create. It will work now but it will bite you in the behind when you have to extend the
storage device.

```
pvcreate /dev/sdb
```

### Create Volume Group ###
A volume group is a pool of storage devices wherein we can create logical volumes.
Create a new volume group named `vg_data`.

```
vgcreate vg_data /dev/sdb
```

You can verify that the Volume Group was created with the `vgs` command.

### Create Logical Volume
A logical volume is used as block storage and needs a filesystem to be usable.
Create a logical volume and format it as EXT4.

```
lvcreate -n opt -l +100%FREE vg_data
mkfs.ext4 /dev/vg_data/opt
```

### Add Mountpoint in /etc/fstab ###
`/etc/fstab` Is used by the kernel for mounting devices. You must add the new volume to fstab to ensure that it
will be mounted on startup.

```
echo -e "/dev/vg_data/opt\t/opt\text4\tdefaults\t0 0" >> /etc/fstab
```

### Mount the volume ###
You can now proceed to mount the volume.

```
mount /opt
```

You can validate that the new volume is mounted with df

```
df -h /opt
```
