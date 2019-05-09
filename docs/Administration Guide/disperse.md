# Creating Dispersed Volumes

Dispersed volumes are based on erasure codes. It stripes the encoded data of files, with some redundancy added, across multiple bricks in the volume. You can use dispersed volumes to have a configurable level of reliability with minimum space waste.

**Redundancy**

Each dispersed volume has a redundancy value defined when the volume is created. This value determines how many bricks can be lost without interrupting the operation of the volume. It also determines the amount of usable space of the volume using this formula:

    <Usable size> = <Brick size> * (#Bricks - Redundancy)

All bricks of a disperse set should have the same capacity otherwise, when the smallest brick becomes full, no additional data will be allowed in the disperse set.

It's important to note that a configuration with 3 bricks and redundancy 1 will have less usable space (66.7% of the total physical space) than a configuration with 10 bricks and redundancy 1 (90%). However the first one will be safer than the second one (roughly the probability of failure of the second configuration if more than 4.5 times bigger than the first one).

For example, a dispersed volume composed of 6 bricks of 4TB and a redundancy of 2 will be completely operational even with two bricks inaccessible. However a third inaccessible brick will bring the volume down because it won't be possible to read or write to it. The usable space of the volume will be equal to 16TB.

The implementation of erasure codes in GlusterFS limits the redundancy to a value smaller than #Bricks / 2 (or equivalently, redundancy * 2 < #Bricks). Having a redundancy equal to half of the number of bricks would be almost equivalent to a replica-2 volume, and probably a replicated volume will perform better in this case.

**Optimal volumes**

One of the worst things erasure codes have in terms of performance is the RMW (Read-Modify-Write) cycle. Erasure codes operate in blocks of a certain size and it cannot work with smaller ones. This means that if a user issues a write of a portion of a file that doesn't fill a full block, it needs to read the remaining portion from the current contents of the file, merge them, compute the updated encoded block and, finally, writing the resulting data.

This adds latency, reducing performance when this happens. Some GlusterFS performance xlators can help to reduce or even eliminate this problem for some workloads, but it should be taken into account when using dispersed volumes for a specific use case.

Current implementation of dispersed volumes use blocks of a size that depends on the number of bricks and redundancy: 512 * (#Bricks - redundancy) bytes. This value is also known as the stripe size.

Using combinations of #Bricks/redundancy that give a power of two for the stripe size will make the disperse volume perform better in most workloads because it's more typical to write information in blocks that are multiple of two (for example databases, virtual machines and many applications).

These combinations are considered optimal.

For example, a configuration with 6 bricks and redundancy 2 will have a stripe size of 512 * (6 - 2) = 2048 bytes, so it's considered optimal. A configuration with 7 bricks and redundancy 2 would have a stripe size of 2560 bytes, needing a RMW cycle for many writes (of course this always depends on the use case).

**To create a dispersed volume**

1. Create a trusted storage pool.
2. Create the dispersed volume:

*gluster volume create [disperse [<count>]] [redundancy <count>][transport tcp | rdma | tcp,rdma]*

    # gluster volume create [disperse [<count]] [redundancy <count>] [transport tcp| rdma| tcp,rdma] 
    
    
A dispersed volume can be created by specifying the number of bricks in a disperse set, by specifying the number of redundancy bricks, or both.

If disperse is not specified, or the <count> is missing, the entire volume will be treated as a single disperse set composed by all bricks enumerated in the command line.

If redundancy is not specified, it is computed automatically to be the optimal value. If this value does not exist, it's assumed to be '1' and a warning message is shown:

```
# gluster volume create test-volume disperse 4 server{1..4}:/bricks/test-volume
There isn't an optimal redundancy value for this configuration. Do you want to create the volume with redundancy 1 ? (y/n)
```
    

In all cases where redundancy is automatically computed and it's not equal to '1', a warning message is displayed:

```
# gluster volume create test-volume disperse 6 server{1..6}:/bricks/test-volume
The optimal redundancy for this configuration is 2. Do you want to create the volume with this value ? (y/n)
```

redundancy must be greater than 0, and the total number of bricks must be greater than 2 * redundancy. This means that a dispersed volume must have a minimum of 3 bricks.

If the transport type is not specified, tcp is used as the default. You can also set additional options if required, like in the other volume types.

> Note:
>
>Make sure you start your volumes before you try to mount them or else client operations after the mount will hang.
>
>GlusterFS will fail to create a dispersed volume if more than one brick of a disperse set is present on the same peer.
>
>```
># gluster volume create <volname> disperse 3 server1:/brick{1..3}
>    volume create: <volname>: failed: Multiple bricks of a replicate volume >are present on the same server. This setup is not optimal.
>    Do you still want to continue creating the volume? (y/n)
>```
>
>Use the force option at the end of command if you want to create the volume >in this case.