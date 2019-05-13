## Expanding Volumes

You can expand volumes, as needed, while the cluster is online and
available. For example, you might want to add a brick to a distributed
volume, thereby increasing the distribution and adding to the capacity
of the GlusterFS volume.

Similarly, you might want to add a group of bricks to a distributed
replicated volume, increasing the capacity of the GlusterFS volume.

> **Note**
>
> When expanding distributed replicated and distributed dispersed volumes,
> you need to add a number of bricks that is a multiple of the replica
> or disperse count. For example, to expand a distributed replicated
> volume with a replica count of 2, you need to add bricks in multiples
> of 2 (such as 4, 6, 8, etc.).

**To expand a volume**

1.  If they are not already part of the TSP, probe the servers which contain the bricks you
    want to add to the volume using the following command:

    `# gluster peer probe <SERVERNAME>`

    For example:

        # gluster peer probe server4
        Probe successful

2.  Add the brick using the following command:

    `# gluster volume add-brick <VOLNAME> <NEW-BRICK>`

    For example:

        # gluster volume add-brick test-volume server4:/exp4
        Add Brick successful

3.  Check the volume information using the following command:

    `# gluster volume info <VOLNAME>`

    The command displays information similar to the following:

        Volume Name: test-volume
        Type: Distribute
        Status: Started
        Number of Bricks: 4
        Bricks:
        Brick1: server1:/exp1
        Brick2: server2:/exp2
        Brick3: server3:/exp3
        Brick4: server4:/exp4

4.  Rebalance the volume to ensure that files are distributed to the
    new brick.

    You can use the rebalance command as described in [Rebalancing Volumes](#rebalancing-volumes)

<a name="shrinking-volumes"></a>
