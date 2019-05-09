# Sharding [Experimental]

Sharding addresses the problem of fragmentation of space within a volume. This feature adds support for files that are larger than the size of an individual brick. Sharding works by chunking files to blobs of a configurabe size.

For more information, see [here](https://github.com/gluster/glusterfs-specs/blob/master/done/GlusterFS 3.7/Sharding xlator.md).

Sharding is an experimental feature for this release. It is expected to be fully supported in a 3.7.x minor release.