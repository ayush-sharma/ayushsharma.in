---
layout: post
title:  "Working with Logical Volume Manager (LVM)"
number: 27
date:   2017-02-03 0:00
categories: automation
---
Logical Volume Manager allows for a layer of abstraction between the OS and the hardware. Normally, the OS would look for disks (`/dev/sda`, `/dev/sdb`, etc.) and partitions within those disks (`/dev/sda1`, `/dev/sdb1`, etc.). In LVM, a virtual layer is created between the OS and the disks. Beneath this layer, the LVM managers disk differently, creating volume groups and logical volumes, but shows them as disks and partitions, respectively, to the OS. So the OS is completely unaware that something different is happening, and continues to operate normally.

<img src="{{ site.images-path | prepend: site.baseurl | prepend: site.url }}difference-between-regular-partitions-and-lvm.jpg" width="700" height="281" alt="Difference between regular partitions and LVM.">

Because the LVM creates volume groups and logical volumes virtually, it makes it easy to resize or move them, or create new volumes, even while the system is running. Additionally, LVM provides features that are not present otherwise, like creating live snapshots of logical volumes, without un-mounting the disk first.

A volume group in a LVM is a named virtual container that groups together the underlying physical disks. It acts as a pool from which logical volumes of different sizes can be created. Logical volumes contain the actual file system, and can span multiple disks, and don't need to be physically contiguous.

## Features

1. Partition names normally have system designations like `/dev/sda1`. LVM volumes have normal human-understandable names, like `home` or `media`.
1. Total size of partitions is limited by the size of the underlying physical disk. In LVM, volumes can span multiple disks, and are only limited by the total size of all physical disks in the LVM.
1. Partitions can normally only be resized, moved or deleted when the disk is not in use and is un-mounted. LVM volumes can be manipulated while the system is running.
1. Partitions can only be expanded by allocating them free space adjacent to the partition. LVM volumes can take free space from anywhere.
1. Expanding a partition involves moving the data around to make free space, which is time consuming and could lead to data loss during a power outage. LVM volumes can take free space from anywhere in the volume group, even on another disk.
1. Because it's so easy to create volumes in a LVM, it encourages creating different volumes, like creating separate volumes to test features or to try different operation systems. With partitions, this process would be time consuming and error-prone.
1. Snapshots can only be created in a LVM. It allows you to create a point-in-time image of the current logical volume, even while the system is running. This is great for backups.

## Test Setup
We're going to test this with a system with the following drive configuration:

```
NAME    MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
xvda    202:0    0   8G  0 disk
`-xvda1 202:1    0   8G  0 part /
xvdb    202:16   0   1G  0 disk
xvdc    202:32   0   1G  0 disk
xvdd    202:48   0   2G  0 disk
xvde    202:64   0   5G  0 disk
xvdf    202:80   0   8G  0 disk
```

So let's begin.

### Step 1. Initialize disks to use with LVM
Run `pvcreate /dev/xvdb /dev/xvdc /dev/xvdd /dev/xvde /dev/xvdf`. The output should be:

```
Physical volume "/dev/xvdb" successfully created
Physical volume "/dev/xvdc" successfully created
Physical volume "/dev/xvdd" successfully created
Physical volume "/dev/xvde" successfully created
Physical volume "/dev/xvdf" successfully created
```

We can check the result using `pvs` or `pvdisplay`:

```
"/dev/xvde" is a new physical volume of "5.00 GiB"
--- NEW Physical volume ---
PV Name               /dev/xvde
VG Name
PV Size               5.00 GiB
Allocatable           NO
PE Size               0
Total PE              0
Free PE               0
Allocated PE          0
PV UUID               728JtI-ffZD-h2dZ-JKnV-8IOf-YKdS-8srJtn

"/dev/xvdb" is a new physical volume of "1.00 GiB"
--- NEW Physical volume ---
PV Name               /dev/xvdb
VG Name
PV Size               1.00 GiB
Allocatable           NO
PE Size               0
Total PE              0
Free PE               0
Allocated PE          0
PV UUID               zk1phS-7uXc-PjBP-5Pv9-dtAV-zKe6-8OCRkZ

"/dev/xvdd" is a new physical volume of "2.00 GiB"
--- NEW Physical volume ---
PV Name               /dev/xvdd
VG Name
PV Size               2.00 GiB
Allocatable           NO
PE Size               0
Total PE              0
Free PE               0
Allocated PE          0
PV UUID               R0I139-Ipca-KFra-2IZX-o9xJ-IW49-T22fPc

"/dev/xvdc" is a new physical volume of "1.00 GiB"
--- NEW Physical volume ---
PV Name               /dev/xvdc
VG Name
PV Size               1.00 GiB
Allocatable           NO
PE Size               0
Total PE              0
Free PE               0
Allocated PE          0
PV UUID               FDzcVS-sq22-2b13-cYRj-dXHf-QLjS-22Meae

"/dev/xvdf" is a new physical volume of "8.00 GiB"
--- NEW Physical volume ---
PV Name               /dev/xvdf
VG Name
PV Size               8.00 GiB
Allocatable           NO
PE Size               0
Total PE              0
Free PE               0
Allocated PE          0
PV UUID               TRVSH9-Bo5D-JHHb-g0NX-8IoS-GG6T-YV4d0p
```

### Step 2. Create the volume group
Run `vgcreate myvg /dev/xvdb /dev/xvdc /dev/xvdd /dev/xvde /dev/xvdf`. We can check the results using `vgs` or `vgdisplay`:

```
--- Volume group ---
VG Name               myvg
System ID
Format                lvm2
Metadata Areas        5
Metadata Sequence No  1
VG Access             read/write
VG Status             resizable
MAX LV                0
Cur LV                0
Open LV               0
Max PV                0
Cur PV                5
Act PV                5
VG Size               16.98 GiB
PE Size               4.00 MiB
Total PE              4347
Alloc PE / Size       0 / 0
Free  PE / Size       4347 / 16.98 GiB
VG UUID               ewrrWp-Tonj-LeFa-4Ogi-BIJJ-vztN-yrepkh
```

### Step 3: Create logical volumes
Run the following commands:

```
lvcreate myvg --name media --size 4G
lvcreate myvg --name home --size 4G
```

We can check the above using `lvs` or `lvdisplay`:

```
--- Logical volume ---
LV Path                /dev/myvg/media
LV Name                media
VG Name                myvg
LV UUID                LOBga3-pUNX-ZnxM-GliZ-mABH-xsdF-3VBXFT
LV Write Access        read/write
LV Creation host, time ip-10-0-5-236, 2017-02-03 05:29:15 +0000
LV Status              available
# open                 0
LV Size                4.00 GiB
Current LE             1024
Segments               1
Allocation             inherit
Read ahead sectors     auto
- currently set to     256
Block device           252:0

--- Logical volume ---
LV Path                /dev/myvg/home
LV Name                home
VG Name                myvg
LV UUID                Hc06sl-vtss-DuS0-jfqj-oNce-qKf6-e5qHhK
LV Write Access        read/write
LV Creation host, time ip-10-0-5-236, 2017-02-03 05:29:40 +0000
LV Status              available
# open                 0
LV Size                4.00 GiB
Current LE             1024
Segments               1
Allocation             inherit
Read ahead sectors     auto
- currently set to     256
Block device           252:1
```

### Step 4: Create the file system
Let's create the file system using...

```
mkfs.ext3 /dev/myvg/media
mkfs.ext3 /dev/myvg/home
```

... and mounting it using...

```
mount /dev/myvg/media /media
mount /dev/myvg/home /home
```

Let's check our full setup using `lsblk`:

```
NAME         MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
xvda         202:0    0   8G  0 disk
`-xvda1      202:1    0   8G  0 part /
xvdb         202:16   0   1G  0 disk
xvdc         202:32   0   1G  0 disk
xvdd         202:48   0   2G  0 disk
xvde         202:64   0   5G  0 disk
`-myvg-media 252:0    0   4G  0 lvm  /media
xvdf         202:80   0   8G  0 disk
`-myvg-home  252:1    0   4G  0 lvm  /home
```

### Step 5: Extending the LVM
Let's add a new disk at `/dev/xvdg`. To extend the `home` volume, run the following commands:

``` 
pvcreate /dev/xvdg
vgextend myvg /dev/xvdg
lvextend -l 100%FREE /dev/myvg/home
resize2fs /dev/myvg/home
```

Run `df -h` and you should see your new size reflected.

And that's it! Have fun :)