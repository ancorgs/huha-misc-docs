# Expert Partitioner Brainstorming

## Existing GUI Partitioning Tools for Linux


None of the tools in this section offer anything beyond very basic
partition-based operations. In particular,

- no LVM

- no RAID

- no encryption (LUKS etc.)


### GParted

https://en.wikipedia.org/wiki/GParted

![screenshot](https://upload.wikimedia.org/wikipedia/commons/2/2c/GParted_0.18_GUID_partition_table.png)



### QtParted

https://en.wikipedia.org/wiki/QtParted

![screenshot](https://upload.wikimedia.org/wikipedia/commons/f/ff/Qtparted_screenshot.png)



### KDE partition manager
  https://en.wikipedia.org/wiki/KDE_Partition_Manager

  ![screenshot](https://upload.wikimedia.org/wikipedia/commons/a/af/KDE_Partition_Manager_screenshot.jpg)



### GNOME disks (gnome-disk-utility)

https://en.wikipedia.org/wiki/GNOME_Disks

![screenshot](https://upload.wikimedia.org/wikipedia/commons/c/cd/GNOME_Disks_3.12.1.png)



## Other Graphical Storage Related Tools for Linux/Unix


### PartedMagic

https://partedmagic.com/

This is little more than a Linux ISO that boots into a desktop with GParted.


### Logical Volume Management (Ubuntu / Red Hat)

https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Storage_Administration_Guide/s1-system-config-lvm.html


![screenshot](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Storage_Administration_Guide/images/lvm-main1.png)

![screenshot](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Storage_Administration_Guide/images/lvm-main2.png)

![screenshot](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Storage_Administration_Guide/images/lvm-main3.png)

- only LVM, nothing else (no partition management)

- tree based like our expert partitioner

- graphical display: cylinders for logical volumes and physical volumes

- tree branch "uninitialized entities"

- all operations largely based on dialogs with input fields, combo boxes and
  sometimes sliders


### Solaris Volume Manager

http://shrubbery.net/solaris9ab/SUNWaadm/LOGVOLMGRADMIN/p6.html

http://docs.oracle.com/cd/E19253-01/816-4520/

![screenshot](http://docs.oracle.com/cd/E18752_01/html/816-4520/figures/svm-gui.png)
http://docs.oracle.com/cd/E18752_01/html/816-4520/exlwh.html


This tool looks quite promising at first glance, but it's just one huge device
tree starting at "this computer" with a lot of branches that are mostly just
very loosely related to each other.



## Existing Storage-related GUI Tools for Windows and MacOS X

There are many partitioning tools for Windows (and some for MacOS X), but none
of them goes beyond basic partition operations (create, delete, resize, maybe
move). Available RAID tools typically handle RAID only, not partitions;
encryption tools handle only encryption.

There is no counterpart to LVM on Windows. MacOS X introduced that only
recently (called "CoreStorage"), but it is unclear if there are any GUI tools
available for this yet. They seem to try to hide the LVM as well as the
encryption layer from the user.

Bottom line: Both Windows and MacOS X have colorful tools with little
functionality. None of them comes even close to what our Expert partitioner offers.



## YaST Expert Partitioner

Compared with everything else on the market (including Windows and MacOS), the
YaST expert partitioner is the most powerful tool by far.

### Available Views

- <Hostname>
  - Hard Disks
  - RAID
  - Volume Management
  - Crypt Files
  - Device Mapper
  - NFS
  - Btrfs
  - tmpfs
  - Unused Devices
- Device Graph
- Mount Graph
- Installation Summary
- Settings


### New Features

Already requested:

- Create filesystem on disk directly without partition table

- Create LUKS on disk directly


Not requested yet, but forseeable:

- Btrfs as volume manager (Btrfs spanning multiple partitions, very much like
  LVM)



### Ideas

#### Subvolumes Directly Below a Btrfs Partition

Right now, subvolumes are very much hidden: You only see them in the
"installation summary" (proposal) view. Otherwise you'll have to click on a
Btrfs partition and then "Edit" and then on the "subvolumes" volume.


#### Hyperlinks in Many Views

Whenever a view is HTML-like (not a table with selectable items), add
hyperlinks at appropriate places.

Example:

    Partition: /dev/vda3

      Device:

        Device: /dev/vda3
        Size: 40 GiB
        Encrypted: No
        Device Path:
        Device ID 1:
        FS ID: 0x83 Linux native
        File System:

    File System:

        File System: Ext4
        Mount Point: /home
        Label: Home

- Size: Same as "Resize" button
- Encrypted: Toggle encryption
- FS ID: Change partition type / ID
- File System: Change filesystem type / format flag
- Mount point: Edit mount point
- Label: Edit partition label

etc.

In other cases (if a partition is used by LVM), jump to the appropriate view
(LVM in this case).

In the table-like views, context menus serve very much the same purpose
already.

In an ideal world, we'd present only the item a user clicked on - only the
partition label, only the encryption flag etc; for a first version, we'd open
the appropriate "edit partition" (etc.) dialog where the user can change other
things as well.

The problem is that some of those settings are interdependent of each other;
that would raise usability problems in some cases. For example, changing either
the partition type or the mount point of a swap partition would also imply to
change the other, and that is realistically only possible (and usable) if the
user sees them both at the same time.

_**To be discussed**_



#### Show Gaps (Free Space) in Partitions Display

Right now, the partitions display (the table of partitions) does not show any
gaps (unallocated space) between the partitions, so the user does not get any
cue about unallocated disk space between partitions. We should display those
gaps (unless they are tiny - in which case they are probably irrelevant).



#### Guided Workflows for Complex Tasks (RAID, LVM)

Setting up a RAID or LVM in the expert partitioner could be easier (at least
for some very common tasks). Right now, the user has to create and prepare
partitions, then switch to a different view, create volume groups and logical
volumes or add partitions or disks to a RAID.

We could design a number of simple workflows for that to collect the relevant
information in an organized manner.


#### Proposal Refinement Step

This has been requested as a different feature: Support moving partition
boundaries around during installation, before any changes are already commited
to disk. That would basically mean to resize partition A, move partition B to
the front to use the space just freed by partition A, and then resize partition
B to fill the space.

It would be very difficult to make this work in the general case, and it would
be even more difficult in the running system because partition B would have to
be moved on disk, i.e. all content would have to be copied to different
sectors.

But what many users really want to do is to make minor changes to the storage
proposal: For example, distribute the disk space differently between / and
/home. For that, we could introduce an intermediate step between the proposal
and the expert partitioner that would look very much like the windows partition
resize dialog, but instead of resizing a windows partition to make space for a
Linux partition, we'd resize the root partition to make space for the /home
partition.


#### Btrfs as Volume Manager

Btrfs can span multiple disks all by itself, very much like LVM. The question
is how to present this to the user.


#### Btrfs RAID

Btrfs can act as a RAID all by itself. Again, the question is how to present
this to the user.



