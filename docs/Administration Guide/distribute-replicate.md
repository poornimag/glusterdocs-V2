# Creating Distributed Replicated Volumes

Distributes files across replicated bricks in the volume. You can use distributed replicated volumes in environments where the requirement is to scale storage and high-reliability is critical. Distributed replicated volumes also offer improved read performance in most environments.

> Note: The number of bricks should be a multiple of the replica count for a distributed replicated volume. Also, the order in which bricks are specified has a great effect on data protection. Each replica_count consecutive bricks in the list you give will form a replica set, with all replica sets combined into a volume-wide distribute set. To make sure that replica-set members are not placed on the same node, list the first brick on every server, then the second brick on every server in the same order, and so on.

![](https://i.imgur.com/Va6dwHS.png)


To create a distributed replicated volume

1. Create a trusted storage pool.
2. Create the distributed replicated volume:

        # gluster volume create  [replica ] [transport tcp | rdma | tcp,rdma]
    
    For example, a four node distributed (replicated) volume with a two-way mirror:

        # gluster volume create test-volume replica 2 transport tcp server1:/exp1 server2:/exp2 server3:/exp3 server4:/exp4
        Creation of test-volume has been successful
        Please start the volume to access data.

    For example, to create a six node distributed (replicated) volume with a two-way mirror:

        # gluster volume create test-volume replica 2 transport tcp server1:/exp1 server2:/exp2 server3:/exp3 server4:/exp4 server5:/exp5 server6:/exp6
        Creation of test-volume has been successful
        Please start the volume to access data.

    If the transport type is not specified, tcp is used as the default. You can also set additional options if required, such as auth.allow or auth.reject.

> Note: - Make sure you start your volumes before you try to mount them or else client operations after the mount will hang.
            
GlusterFS will fail to create a distribute replicate volume if more than one brick of a replica set is present on the same peer. For eg. for a four node distribute (replicated) volume where more than one brick of a replica set is present on the same peer.

            # gluster volume create <volname> replica 2 server1:/brick1 server1:/brick2 server2:/brick3 server4:/brick4
            volume create: <volname>: failed: Multiple bricks of a replicate volume are present on the same server. This setup is not optimal. Use 'force' at the end of the command if you want to override this behavior.

Use the force option at the end of command if you want to create the volume in this case.

