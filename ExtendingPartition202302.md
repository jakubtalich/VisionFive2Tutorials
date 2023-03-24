# Extending the ext4 System (root) Partition on a microSD card or eMMC
To fully use all the free space under StarFive's Debian version 202302 on a microSD or eMMC one must extend the partition to its full available size:

(Based on: https://doc-en.rvspace.org/VisionFive2/Quick_Start_Guide/VisionFive2_QSG/extend_partition.html)

01. List the available space on all partitions:
`# df -h`
Example output where we can see our root partition is 96% full:
```
Filesystem      Size  Used Avail Use% Mounted on
udev            3.7G     0  3.7G   0% /dev
tmpfs           793M  3.1M  790M   1% /run
/dev/mmcblk1p4  2.0G  1.9G   88M  96% /
tmpfs           3.9G     0  3.9G   0% /dev/shm
tmpfs           5.0M   12K  5.0M   1% /run/lock
tmpfs           793M   32K  793M   1% /run/user/107
tmpfs           793M   24K  793M   1% /run/user/0
```

02. Run `fdisk` (where X stands for the number of your partition - in this case an eMMC partition):
`# fdisk /dev/mmcblkX`

Example output:

```
root@starfive:~# fdisk /dev/mmcblk1

Welcome to fdisk (util-linux 2.38.1).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

GPT PMBR size mismatch (4505599 != 62929919) will be corrected by write.
This disk is currently in use - repartitioning is probably a bad idea.
It's recommended to umount all file systems, and swapoff all swap
partitions on this disk. 
Command (m for help): d
Partition number (1-4, default 4): 4  

Partition 4 has been deleted.

Command (m for help): n
Partition number (4-128, default 4): 4
First sector (34-62929886, default 221184):
Last sector, +/-sectors or +/-size{K,M,G,T,P} (221184-62929886, default 62928895):

Created a new partition 4 of type 'Linux filesystem' and of size 29.9 GiB.
Partition #4 contains a ext4 signature.

Do you want to remove the signature? [Y]es/[N]o: N

Command (m for help): w

The partition table has been altered.
Syncing disks.
```
Here we adjusted partition No. 4.

03. Inform the kernel about changes with `# partx /dev/mmcblkX`.

04. Resize the `/dev/mmcblkXp4` (again, change the X) partition by running the `resize2fs` command to fully utilize the unused block:

```
root@starfive:~# resize2fs /dev/mmcblk1p4
resize2fs 1.46.6-rc1 (12-Sep-2022)
Filesystem at /d[  295.372617] EXT4-fs (mmcblk1p4): resizing filesystem from 535291 to 7838464 blocks
ev/mmcblk1p4 is mounted on /; on-line resizing required
old_desc_blocks = 1, new_desc_blocks = 4
[  295.993163] EXT4-fs (mmcblk1p4): resized filesystem to 7838464
The filesystem on /dev/mmcblk1p4 is now 7838464 (4k) blocks long.
```

05. Now we can check and see that the partition has grown to its maximum size:
`# df -h`

```
root@starfive:~# df -h
Filesystem      Size  Used Avail Use% Mounted on
udev            3.7G     0  3.7G   0% /dev
tmpfs           793M  3.1M  790M   1% /run
/dev/mmcblk1p4   30G  1.9G   28G   7% /
tmpfs           3.9G     0  3.9G   0% /dev/shm
tmpfs           5.0M   12K  5.0M   1% /run/lock
tmpfs           793M   32K  793M   1% /run/user/107
tmpfs           793M   24K  793M   1% /run/user/0
```
