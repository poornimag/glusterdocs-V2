# Distributed
Distributed volumes distribute files across the bricks in the volume. You can use distributed volumes where the requirement is to scale storage and the redundancy is either not important or is provided by other hardware/software layers.


In a distributed volume files are spread randomly across the bricks in the volume. Use distributed volumes where you need to scale storage and redundancy is either not important or is provided by other hardware/software layers.

> Note: Disk/server failure in distributed volumes can result in a serious loss of data because directory contents are spread randomly across the bricks in the volume.

![](https://i.imgur.com/aA2uR1U.png)

To create a distributed volume

1. Create a trusted storage pool.
2. Create the distributed volume:

```
# gluster volume create [transport tcp | rdma | tcp,rdma]
``` 
For example, to create a distributed volume with four storage servers using tcp:
```
# gluster volume create test-volume server1:/exp1 server2:/exp2 server3:/exp3 server4:/exp4
Creation of test-volume has been successful
Please start the volume to access data.
```
(Optional) You can display the volume information:
```
# gluster volume info
Volume Name: test-volume
Type: Distribute
Status: Created
Number of Bricks: 4
Transport-type: tcp
Bricks:
Brick1: server1:/exp1
Brick2: server2:/exp2
Brick3: server3:/exp3
Brick4: server4:/exp4
```
For example, to create a distributed volume with four storage servers over InfiniBand:
```
# gluster volume create test-volume transport rdma server1:/exp1 server2:/exp2 server3:/exp3 server4:/exp4
Creation of test-volume has been successful
Please start the volume to access data.

```
If the transport type is not specified, tcp is used as the default. You can also set additional options if required, such as auth.allow or auth.reject.

> Note: Make sure you start your volumes before you try to mount them or else client operations after the mount will hang.
