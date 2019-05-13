## Shrinking Volumes

You can shrink volumes, as needed, while the cluster is online and 
available. For example, you might need to remove a brick that has become
inaccessible in a distributed volume due to hardware or network failure.

> **Note**
>
> Data residing on the brick that you are removing will no longer be
> accessible at the Gluster mount point. Note however that only the 
> configuration information is removed - you can continue to access the 
> data directly from the brick, as necessary.

When shrinking distributed replicated and distributed dispersed volumes,
you need to remove a number of bricks that is a multiple of the replica
or stripe count. For example, to shrink a distributed replicate volume
with a replica count of 2, you need to remove bricks in multiples of 2
(such as 4, 6, 8, etc.). In addition, the bricks you are trying to
remove must be from the same sub-volume (the same replica or disperse
set).

Running remove-brick with the _start_ option will automatically trigger a rebalance
operation to migrate data from the removed-bricks to the rest of the volume.

**To shrink a volume**

1.  Remove the brick using the following command:

    `# gluster volume remove-brick <VOLNAME> <BRICKNAME> start`

    For example, to remove server2:/exp2:

        # gluster volume remove-brick test-volume server2:/exp2 start
        volume remove-brick start: success

2.  View the status of the remove brick operation using the 
    following command:

    `# gluster volume remove-brick <VOLNAME> <BRICKNAME> status`

    For example, to view the status of remove brick operation on
    server2:/exp2 brick:

        # gluster volume remove-brick test-volume server2:/exp2 status
                                        Node  Rebalanced-files  size  scanned       status
                                   ---------  ----------------  ----  -------  -----------
        617c923e-6450-4065-8e33-865e28d9428f               34   340      162   in progress

3.  Once the status displays "completed", commit the remove-brick operation

        # gluster volume remove-brick <VOLNAME> <BRICKNAME> commit

    In this example:

        # gluster volume remove-brick test-volume server2:/exp2 commit
        Removing brick(s) can result in data loss. Do you want to Continue? (y/n) y
        volume remove-brick commit: success
        Check the removed bricks to ensure all files are migrated.
        If files with data are found on the brick path, copy them via a gluster mount point before re-purposing the removed brick.

4.  Check the volume information using the following command:

    `# gluster volume info `

    The command displays information similar to the following:

        # gluster volume info
        Volume Name: test-volume
        Type: Distribute
        Status: Started
        Number of Bricks: 3
        Bricks:
        Brick1: server1:/exp1
        Brick3: server3:/exp3
        Brick4: server4:/exp4
