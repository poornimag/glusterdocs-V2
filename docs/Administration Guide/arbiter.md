# Arbiter configuration for replica volumes

Arbiter volumes are replica 3 volumes where the 3rd brick acts as the arbiter brick. This configuration has mechanisms that prevent occurrence of split-brains.

It can be created with the following command:

`# gluster volume create  <VOLNAME>  replica 3 arbiter 1 host1:brick1 host2:brick2 host3:brick3`

More information about this configuration can be found at Features : afr-arbiter-volumes

Note that the arbiter configuration for replica 3 can be used to create distributed-replicate volumes as well.