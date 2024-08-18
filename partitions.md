## PARTED 
### to open the parted shell - to write commands to manage hard drives partitions
    sudo parted 

### driver disk are named sequentaly 
    /dev/sda /dev/sdb

### help
    help

### print devices
    print devices

### out 
    quit

### to select a device to work to it
    sudo parted select /dev/sdb

### print partitions - to see info about the disk partitions.
    print partitions

### print devices
    print devices

### to delete a partition - delete the indicated partition of the disk 
    rm [number as name]

### to delete all the partitions - create a new partition table on the disk - all the data on that disk will be lost
    mklabel 
    gpt 
    confirm

### make a partition - on gpt all the partitions are primary types
    mkpart partition-type filesystem start end
    mkpart primary ext4 0 50000
    mkpart primary ext4 2048s 50000
    mkpart primary ext4 0% 70%

### give a partition with a name 
    name partition-number name
    name 1 backup 

### using parted from bash
    sudo parted /dev/sdb print partitions


### types of file system
    ext4 - the most common actually 

### TO partionate a disk
    select /dev/sdb
    print partitions
    mklabel - gpt - confirm
    mkpart primary ext4 2048s 50000
    name 1 backup 

### TO MOUNT A NEW PARTITION 
#### 1 GET THE PARTITION NAME
    lsblk -f 

#### 2 CREATE OR CHECK WHERE THE PARTITION WILL BE MOUNTED
    mkdir /mnt/backups

#### 3 MOUNT THE PARTITION INTO THE SPECIF FOLDER
    mount /dev/sdb1 /mnt/backups
    mount -t ext4 /dev/sdb1 /mnt/backups
    mount -o ro -t ext4 /dev/sdb1 /mnt/backups -- this is for read only permision

### FORCE THE LOG ROTATION - THIS CASE FOR THE SPECIFIC POSTGRESQL-COMMON
logrotate -fv /etc/logrotate.d/postgresql-common


### to automatically mount a filysystem during boot
UUID=cc319470-6220-4a8a-9a7f-e444be78df3d /mnt/backups ext4 defaults 0 2

### mount the filesystems once the fstab is edited with the new entry for the new filesystem
mount -a

## PARTED 
### to open the parted shell - to write commands to manage hard drives partitions
    sudo parted 

### driver disk are named sequentaly 
    /dev/sda /dev/sdb

### help
    help

### print devices
    print devices

### out 
    quit

### to select a device to work to it
    sudo parted select /dev/sdb

### print partitions - to see info about the disk partitions.
    print partitions

### print devices
    print devices

### to delete a partition - delete the indicated partition of the disk 
    rm [number as name]

### to delete all the partitions - create a new partition table on the disk - all the data on that disk will be lost
    mklabel 
    gpt 
    confirm

### make a partition - on gpt all the partitions are primary types
    mkpart partition-type filesystem start end
    mkpart primary ext4 0 50000
    mkpart primary ext4 2048s 50000
    mkpart primary ext4 0% 70%

### give a partition with a name 
    name partition-number name
    name 1 backup 

### using parted from bash
    sudo parted /dev/sdb print partitions


### types of file system
    ext4 - the most common actually 

### TO partionate a disk
    select /dev/sdb
    print partitions
    mklabel - gpt - confirm
    mkpart primary ext4 2048s 50000
    name 1 backup 

### to check the free space of a partition - in parted command line:
    print free 

### create the filesystem into the partition - crates the actual filesystem into the partition
    sudo mkfs.ext4 /dev/sdb1

### TO MOUNT A NEW PARTITION 
#### 1 GET THE PARTITION NAME
    lsblk -f 

#### 2 CREATE OR CHECK WHERE THE PARTITION WILL BE MOUNTED
    mkdir /mnt/backups

#### 3 MOUNT THE PARTITION INTO THE SPECIF FOLDER
    mount /dev/sdb1 /mnt/backups
    mount -t ext4 /dev/sdb1 /mnt/backups
    mount -o ro -t ext4 /dev/sdb1 /mnt/backups -- readonly permition

#### AUTOMATING THE MOUNT PROCESS WITH FSTAB FILE
    nano /etc/fstab 
#### The fstab consist of 1 - uuid (device uuid or the device path) 2 - mount point -3 format -4 options -5 dump option - 6 check priority
    /dev/sdb1 /mnt/backups ext4 default 0 2
    devicepath mountpoint format options dump check
    UUID=20cad212-2017-45a0-9e55-134ae254a662 /mnt/backups ext4 defaults 0 2
    UUID=20cad212-2017-45a0-9e55-134ae254a662 /mnt/backups ext4 defaults,noexec,nosuid  0 2
    /dev/vgroup/data /mnt/data ext4 defaults 0 2
### ONCE WE HAVE EDITED THE FSTAB - MOUNT THE FILESYSTEM LISTED IN THE FSTAB
    mount -a
    mount -av

#### You can find the UUID of the partition using this command:

    sudo blkid /dev/sdX1

### CHECK IF IS MOUNTED - df -h

#### HOW TO EXPAND FILE SYSTEM AND PARTITION - Resizing a filesystem (growing)
### 1 - Resize the partition to a large size - for this case the partition use from the 10GB to 15GB and in this case we want to expand it to 25GB, and the partition with end with a size aprox of 15GB - from a size of 5 to 15GB
    sudo parted
    select /dev/sdb2
    resizepart - this command will propt 1 - partition number 2 - new end of the partition
    - 2 
    - 25GB - select partition in this case the 2 and end in the new end 25GB
    print

### 2 - Resize the filesystem 
    df -h -> check for this case the partition has a size of 5GB 
    sudo resize2fs /dev/sdb2 - with this the filesystem will resize his size to the actual size of the partition - 15GB



### FORCE THE LOG ROTATION - THIS CASE FOR THE SPECIFIC POSTGRESQL-COMMON
logrotate -fv /etc/logrotate.d/postgresql-common
