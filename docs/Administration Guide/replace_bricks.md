## Replace faulty brick

**Replacing a brick in a *pure* distribute volume**

To replace a brick on a distribute only volume, add the new brick and then remove the brick you want to replace. This will trigger a rebalance operation which will move data from the removed brick.

> NOTE: Replacing a brick using the 'replace-brick' command in gluster is supported only for distributed-replicate or *pure* replicate volumes.

Steps to remove brick Server1:/home/gfs/r2_1 and add Server1:/home/gfs/r2_2:

1.  Here is the initial volume configuration:

        Volume Name: r2
        Type: Distribute
        Volume ID: 25b4e313-7b36-445d-b524-c3daebb91188
        Status: Started
        Number of Bricks: 2
        Transport-type: tcp
        Bricks:
        Brick1: Server1:/home/gfs/r2_0
        Brick2: Server1:/home/gfs/r2_1


2.  Here are the files that are present on the mount:


        # ls
        1  10  2  3  4  5  6  7  8  9

3.  Add the new brick - Server1:/home/gfs/r2_2 now:

        # gluster volume add-brick r2 Server1:/home/gfs/r2_2
        volume add-brick: success

4.  Start remove-brick using the following command:

        # gluster volume remove-brick r2 Server1:/home/gfs/r2_1 start
        volume remove-brick start: success
        ID: fba0a488-21a4-42b7-8a41-b27ebaa8e5f4

5.  Wait until remove-brick status indicates that it is complete.


        # gluster volume remove-brick r2 Server1:/home/gfs/r2_1 status
                                        Node Rebalanced-files          size       scanned      failures       skipped               status   run time in secs
                                   ---------      -----------   -----------   -----------   -----------   -----------         ------------     --------------
                                   localhost                5       20Bytes            15             0             0            completed               0.00


6.  Now we can safely remove the old brick, so commit the changes:

        # gluster volume remove-brick r2 Server1:/home/gfs/r2_1 commit
        Removing brick(s) can result in data loss. Do you want to Continue? (y/n) y
        volume remove-brick commit: success

7.  Here is the new volume configuration.

        Volume Name: r2
        Type: Distribute
        Volume ID: 25b4e313-7b36-445d-b524-c3daebb91188
        Status: Started
        Number of Bricks: 2
        Transport-type: tcp
        Bricks:
        Brick1: Server1:/home/gfs/r2_0
        Brick2: Server1:/home/gfs/r2_2

8.  Check the contents of the mount:

        # ls
        1  10  2  3  4  5  6  7  8  9

**Replacing bricks in Replicate/Distributed Replicate volumes**

This section of the document describes how brick: `Server1:/home/gfs/r2_0` is replaced with brick: `Server1:/home/gfs/r2_5` in volume `r2` with replica count `2`.

        Volume Name: r2
        Type: Distributed-Replicate
        Volume ID: 24a0437a-daa0-4044-8acf-7aa82efd76fd
        Status: Started
        Number of Bricks: 2 x 2 = 4
        Transport-type: tcp
        Bricks:
        Brick1: Server1:/home/gfs/r2_0
        Brick2: Server2:/home/gfs/r2_1
        Brick3: Server1:/home/gfs/r2_2
        Brick4: Server2:/home/gfs/r2_3


Steps:

1.  Make sure there is no data in the new brick Server1:/home/gfs/r2_5
2.  Check that all the bricks are running. It is okay if the brick that is going to be replaced is down.
3.  Bring the brick that is going to be replaced down if not already.

    -  Get the pid of the brick by executing 'gluster volume <volname> status'

            # gluster volume status
            Status of volume: r2
            Gluster process                        Port    Online    Pid
            ------------------------------------------------------------------------------
            Brick Server1:/home/gfs/r2_0            49152    Y    5342
            Brick Server2:/home/gfs/r2_1            49153    Y    5354
            Brick Server1:/home/gfs/r2_2            49154    Y    5365
            Brick Server2:/home/gfs/r2_3            49155    Y    5376

    -  Login to the machine where the brick is running and kill the brick.

            # kill -15 5342

    -  Confirm that the brick is not running anymore and the other bricks are running fine.

            # gluster volume status
            Status of volume: r2
            Gluster process                        Port    Online    Pid
            ------------------------------------------------------------------------------
            Brick Server1:/home/gfs/r2_0            N/A      N    5342 <<---- brick is not running, others are running fine.
            Brick Server2:/home/gfs/r2_1            49153    Y    5354
            Brick Server1:/home/gfs/r2_2            49154    Y    5365
            Brick Server2:/home/gfs/r2_3            49155    Y    5376

4.  Using the gluster volume fuse mount (In this example: `/mnt/r2`) set up metadata so that data will be synced to new brick (In this case it is from `Server1:/home/gfs/r2_1` to `Server1:/home/gfs/r2_5`)

    -  Create a directory on the mount point that doesn't already exist. Then delete that directory, do the same for metadata changelog by doing setfattr. This operation marks the pending changelog which will tell self-heal damon/mounts to perform self-heal from `/home/gfs/r2_1` to `/home/gfs/r2_5`.

            mkdir /mnt/r2/<name-of-nonexistent-dir>
            rmdir /mnt/r2/<name-of-nonexistent-dir>
            setfattr -n trusted.non-existent-key -v abc /mnt/r2
            setfattr -x trusted.non-existent-key  /mnt/r2

    -  Check that there are pending xattrs on the replica of the brick that is being replaced:

            getfattr -d -m. -e hex /home/gfs/r2_1
            # file: home/gfs/r2_1
            security.selinux=0x756e636f6e66696e65645f753a6f626a6563745f723a66696c655f743a733000
            trusted.afr.r2-client-0=0x000000000000000300000002 <<---- xattrs are marked from source brick Server2:/home/gfs/r2_1
            trusted.afr.r2-client-1=0x000000000000000000000000
            trusted.gfid=0x00000000000000000000000000000001
            trusted.glusterfs.dht=0x0000000100000000000000007ffffffe
            trusted.glusterfs.volume-id=0xde822e25ebd049ea83bfaa3c4be2b440

5.  Volume heal info will show that '/' needs healing.(There could be more entries based on the work load. But '/' must exist)

            # gluster volume heal r2 info
            Brick Server1:/home/gfs/r2_0
            Status: Transport endpoint is not connected

            Brick Server2:/home/gfs/r2_1
            /
            Number of entries: 1

            Brick Server1:/home/gfs/r2_2
            Number of entries: 0

            Brick Server2:/home/gfs/r2_3
            Number of entries: 0

6.  Replace the brick with 'commit force' option. Please note that other variants of replace-brick command are not supported.

    -  Execute replace-brick command

            # gluster volume replace-brick r2 Server1:/home/gfs/r2_0 Server1:/home/gfs/r2_5 commit force
            volume replace-brick: success: replace-brick commit successful

    -  Check that the new brick is now online

            # gluster volume status
            Status of volume: r2
            Gluster process                        Port    Online    Pid
            ------------------------------------------------------------------------------
            Brick Server1:/home/gfs/r2_5            49156    Y    5731 <<<---- new brick is online
            Brick Server2:/home/gfs/r2_1            49153    Y    5354
            Brick Server1:/home/gfs/r2_2            49154    Y    5365
            Brick Server2:/home/gfs/r2_3            49155    Y    5376

    -  Users can track the progress of self-heal using: `gluster volume heal [volname] info`.
      Once self-heal completes the changelogs will be removed.

            # getfattr -d -m. -e hex /home/gfs/r2_1
            getfattr: Removing leading '/' from absolute path names
            # file: home/gfs/r2_1
            security.selinux=0x756e636f6e66696e65645f753a6f626a6563745f723a66696c655f743a733000
            trusted.afr.r2-client-0=0x000000000000000000000000 <<---- Pending changelogs are cleared.
            trusted.afr.r2-client-1=0x000000000000000000000000
            trusted.gfid=0x00000000000000000000000000000001
            trusted.glusterfs.dht=0x0000000100000000000000007ffffffe
            trusted.glusterfs.volume-id=0xde822e25ebd049ea83bfaa3c4be2b440

    -  `# gluster volume heal <VOLNAME> info` will show that no heal is required.

            # gluster volume heal r2 info
            Brick Server1:/home/gfs/r2_5
            Number of entries: 0

            Brick Server2:/home/gfs/r2_1
            Number of entries: 0

            Brick Server1:/home/gfs/r2_2
            Number of entries: 0

            Brick Server2:/home/gfs/r2_3
            Number of entries: 0
