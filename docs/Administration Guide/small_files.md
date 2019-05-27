# Tunables for Small Files

### vm.vfs_cache_pressure

This option controls the tendency of the kernel to reclaim the memory which is used for caching of directory and inode objects.

At the default value of vfs_cache_pressure=100 the kernel will attempt to reclaim dentries and inodes at a "fair" rate with respect to pagecache and swapcache reclaim. Decreasing vfs_cache_pressure causes the kernel to prefer to retain dentry and inode caches. When vfs_cache_pressure=0, the kernel will never reclaim dentries and inodes due to memory pressure and this can easily lead to out-of-memory conditions. Increasing vfs_cache_pressure beyond 100 causes the kernel to prefer to reclaim dentries and inodes.

With GlusterFS, many users with a lot of storage and many small files easily end up using a lot of RAM on the server side due to 'inode/dentry' caching, leading to decreased performance when the kernel keeps crawling through data-structures on a 40GB RAM system. Changing this value higher than 100 has helped many users to achieve fair caching and more responsiveness from the kernel.