# Extending the btrfs System (root) Partition on a microSD card or eMMC
This applies to https://github.com/hexdump0815/imagebuilder/releases/tag/230321-02 and other images with btrfs on the root partition.

01. List the available space on all partitions:
`# df -h`
Example output where we can see our root partition is 70% full:
```
Filesystem      Size  Used Avail Use% Mounted on
udev            1.7G     0  1.7G   0% /dev
tmpfs           389M  3.1M  386M   1% /run
/dev/mmcblk1p4  3.0G  2.0G  888M  70% /
tmpfs           1.9G     0  1.9G   0% /dev/shm
tmpfs           5.0M   12K  5.0M   1% /run/lock
/dev/mmcblk1p3  511M   35M  477M   7% /boot
tmpfs           389M   52K  389M   1% /run/user/1001
```

02. Run `fdisk` (where X stands for the number of your partition - in this case an eMMC partition):
`# fdisk /dev/mmcblkX`

Example output:

```
Welcome to fdisk (util-linux 2.38.1).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

GPT PMBR size mismatch (7340031 != 62333951) will be corrected by write.
The backup GPT table is not on the end of the device. This problem will be corrected by write.
This disk is currently in use - repartitioning is probably a bad idea.
It's recommended to umount all file systems, and swapoff all swap
partitions on this disk.


Command (m for help): d
Partition number (1-4, default 4): 4

Partition 4 has been deleted.

Command (m for help): n
Partition number (4-128, default 4): 4
First sector (1064960-62333918, default 1064960): 
Last sector, +/-sectors or +/-size{K,M,G,T,P} (1064960-62333918, default 62332927): 

Created a new partition 4 of type 'Linux filesystem' and of size 29.2 GiB.
Partition #4 contains a btrfs signature.

Do you want to remove the signature? [Y]es/[N]o: N

Command (m for help): w

The partition table has been altered.
Syncing disks.
```
Here we adjusted partition No. 4.

03. Inform the kernel about changes with `# partx /dev/mmcblkX`.

04. Resize the `/dev/mmcblkXp4` (again, change the X) partition by running the `resize2fs` command to fully utilize the unused block:

```
# btrfs filesystem resize max /
Resize device id 1 (/dev/mmcblk1p4) from 2.99GiB to max
```

05. Now we can check and see that the partition has grown to its maximum size:
`# df -h`

```
$ df -h
Filesystem      Size  Used Avail Use% Mounted on
udev            1.7G     0  1.7G   0% /dev
tmpfs           389M  3.1M  386M   1% /run
/dev/mmcblk1p4   30G  2.0G   28G   7% /
tmpfs           1.9G     0  1.9G   0% /dev/shm
tmpfs           5.0M   12K  5.0M   1% /run/lock
/dev/mmcblk1p3  511M   35M  477M   7% /boot
tmpfs           389M   52K  389M   1% /run/user/1001
```
