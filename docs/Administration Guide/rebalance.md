## Rebalancing Volumes

After expanding a volume using the add-brick command, you may need to rebalance the data
among the servers. New directories created after expanding or shrinking
of the volume will be evenly distributed automatically. For all the
existing directories, the distribution can be fixed by rebalancing the
layout and/or data.

This section describes how to rebalance GlusterFS volumes in your
storage environment, using the following common scenarios:

-   **Fix Layout** - Fixes the layout to use the new volume topology so that files can
    be distributed to newly added nodes.

-   **Fix Layout and Migrate Data** - Rebalances volume by fixing the layout
    to use the new volume topology and migrating the existing data.

### Rebalancing Volume to Fix Layout Changes

Fixing the layout is necessary because the layout structure is static
for a given directory. Even after new bricks are added to the volume, newly created
files in existing directories will still be distributed only among the original bricks.
The command `gluster volume rebalance <volname> fix-layout start` will fix the
layout information so that the files can be created on the newly added bricks.
When this command is issued, all the file stat information which is
already cached will get revalidated.

As of GlusterFS 3.6, the assignment of files to bricks will take into account
the sizes of the bricks.  For example, a 20TB brick will be assigned twice as
many files as a 10TB brick.  In versions before 3.6, the two bricks were
treated as equal regardless of size, and would have been assigned an equal
share of files.

A fix-layout rebalance will only fix the layout changes and does not
migrate data. If you want to migrate the existing data,
use `gluster volume rebalance <volume> start` command to rebalance data among
the servers.

**To rebalance a volume to fix layout**

-   Start the rebalance operation on any Gluster server using the
    following command:

    `# gluster volume rebalance <VOLNAME> fix-layout start`

    For example:

        # gluster volume rebalance test-volume fix-layout start
        Starting rebalance on volume test-volume has been successful

### Rebalancing Volume to Fix Layout and Migrate Data

After expanding a volume using the add-brick respectively, you need to rebalance the data
among the servers. A remove-brick command will automatically trigger a rebalance.

**To rebalance a volume to fix layout and migrate the existing data**

-   Start the rebalance operation on any one of the server using the
    following command:

    `# gluster volume rebalance <VOLNAME> start`

    For example:

        # gluster volume rebalance test-volume start
        Starting rebalancing on volume test-volume has been successful

-   Start the migration operation forcefully on any one of the servers
    using the following command:

    `# gluster volume rebalance <VOLNAME> start force`

    For example:

        # gluster volume rebalance test-volume start force
        Starting rebalancing on volume test-volume has been successful

A rebalance operation will attempt to balance the diskusage across nodes, therefore it will skip
files where the move will result in a less balanced volume. This leads to link files that are still
left behind in the system and hence may cause performance issues. The behaviour can be overridden
with the `force` argument.

### Displaying the Status of Rebalance Operation

You can display the status information about rebalance volume operation,
as needed.

-   Check the status of the rebalance operation, using the following
    command:

    `# gluster volume rebalance <VOLNAME> status`

    For example:

        # gluster volume rebalance test-volume status
                                        Node  Rebalanced-files  size  scanned       status
                                   ---------  ----------------  ----  -------  -----------
        617c923e-6450-4065-8e33-865e28d9428f               416  1463      312  in progress

    The time to complete the rebalance operation depends on the number
    of files on the volume along with the corresponding file sizes.
    Continue checking the rebalance status, verifying that the number of
    files rebalanced or total files scanned keeps increasing.

    For example, running the status command again might display a result
    similar to the following:

        # gluster volume rebalance test-volume status
                                        Node  Rebalanced-files  size  scanned       status
                                   ---------  ----------------  ----  -------  -----------
        617c923e-6450-4065-8e33-865e28d9428f               498  1783      378  in progress

    The rebalance status displays the following when the rebalance is
    complete:

        # gluster volume rebalance test-volume status
                                        Node  Rebalanced-files  size  scanned       status
                                   ---------  ----------------  ----  -------  -----------
        617c923e-6450-4065-8e33-865e28d9428f               502  1873      334   completed

### Stopping an Ongoing Rebalance Operation

You can stop the rebalance operation, if needed.

-   Stop the rebalance operation using the following command:

    `# gluster volume rebalance <VOLNAME> stop`

    For example:

        # gluster volume rebalance test-volume stop
                                        Node  Rebalanced-files  size  scanned       status
                                   ---------  ----------------  ----  -------  -----------
        617c923e-6450-4065-8e33-865e28d9428f               59   590      244       stopped
        Stopped rebalance process on volume test-volume

