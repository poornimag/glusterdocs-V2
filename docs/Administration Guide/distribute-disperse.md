# Creating Distributed Dispersed Volumes

Distributed dispersed volumes are the equivalent to distributed replicated volumes, but using dispersed subvolumes instead of replicated ones.

**To create a distributed dispersed volume**

1. Create a trusted storage pool.
2. Create the distributed dispersed volume:

```
# gluster volume create disperse <count> [redundancy <count>] [transport tcp | rdma | tcp,rdma]
```

To create a distributed dispersed volume, the disperse keyword and <count> is mandatory, and the number of bricks specified in the command line must must be a multiple of the disperse count.

redundancy is exactly the same as in the dispersed volume.

If the transport type is not specified, tcp is used as the default. You can also set additional options if required, like in the other volume types.

>Note:
>
>Make sure you start your volumes before you try to mount them or else client operations after the mount will hang.
>
>GlusterFS will fail to create a distributed dispersed volume if more than one brick of a disperse set is present on the same peer.
>
>```
># gluster volume create <volname> disperse 3 server1:/brick{1..6}
>  volume create: <volname>: failed: Multiple bricks of a replicate volume are present on the same server. This setup is not optimal.
>Do you still want to continue creating the volume? (y/n)
>```
>
> Use the force option at the end of command if you want to create the volume in this case.

**Starting Volumes**

You must start your volumes before you try to mount them.

* To start a volume

    Start a volume:

```
    # gluster volume start
```

For example, to start test-volume:

```
# gluster volume start test-volume
Starting test-volume has been successful
```