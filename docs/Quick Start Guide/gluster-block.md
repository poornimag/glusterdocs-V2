# Quick start: glusterfs block storage

Glusterfs provides block storage using the gluster-block project (https://github.com/gluster/gluster-block). As mentioned there, the basic idea is to:

* Create a file in the gluster volume.
* Expose that file as a tcmu backstore using tcmu-runner, exporting the target file as iSCSI LUN.
* From the initiator, login to the exported LUN, format, mount and start consuming the block dev.

# How to set up
On the target machine:
-------------------------
Let us assume a 1 node setup with glusterfs and gluster-block binaries installed, with glusterd and gluster-blockd running.
```
systemctl status glusterd
systemctl status gluster-block
```

```
gluster volume create block-store 192.168.122.137:/bricks/brick1
gluster volume start block-store
gluster-block create block-test/sample-block 192.168.122.137 1GiB
gluster-block list block-test
```

On the initiator machine:
----------------------
```
lsblk
iscsiadm -m discovery -t st -p 192.168.122.137 -l
lsblk
mkfs.xfs /dev/new-device
mount /dev/new-device /mnt
```
We can now mount and use the device as needed.


# How to tear down
On the initiator machine:
-------------------------
```
umount /mnt
iscsiadm -m node -u
```

On the target machine:
----------------------
```
gluster-block delete block-test/sample-block
gluster volume stop block-test
gluster volume delete block-test
```
