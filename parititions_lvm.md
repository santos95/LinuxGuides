## INSTALL LVM  - often is not already installed in ubuntu
    sudp apt install lvm2

### LVM ALLOWS TO WORK WITH MANY DRIVERS AS ONE DRIVE

### Steps to create logical volumes
    1 - create a physical volume 
    2 - create a volume group 
    3 - create a logical volume

### hows this work
    1 - PV - physical volumes - this are a actual disk partition reserved to lvm - to create it we have to create a partition and mark it as lvm partition 
    2 - Volume Group - the volume group is placed on top of the pv, allows to combine the space of multiple disk or patitions as single one space - Responsable to store the date in the physical partitions - Works as a container. 
    Works as device mapper, allowing the combination of multiple partitions from one or multiple disks.
    3 - Logical Volume - Partition created on top of the volume group. This partitions is that we actually use it to create of filesystem and mounted into our system. 
    With this we did not directly write in our disks, we use a layer in between that is responsible to storage the data in the physical part - volume group. 

#### create a logical volumes from scratch from two devices used 100% to that
#### 1 create pv 
    sudo parted 
    select /dev/sdb 
    mklabel - prompt - gpt
    mkpart primary 0% 100%
    set 1 lvm on 
    print - must show in flats - lvm
    sudo pvcreate /dev/sdb1 - in this case only one partition, that use 100% of the size 
    in case is a n partition of a disk
    sudo pvcrate /dev/sdbn - n the number of partition  

    sudo parted 
    select /dev/sdb 
    mklabel - prompt - gpt
    mkpart primary 0% 100%
    set 1 lvm on 
    print - must show in flats - lvm
    sudo pvcreate /dev/sdc1
##### to visualize info about pv
    sudo pvs - short info
    sudo pvdisplay - detailed info about pv
    sudo pvscan - to troubleshooting - scan or search for pv when we can't see it with pvs or pvdisplay

#### 2 create a volume group - once that exists pv or is initialized we can create a volume group:
    pvcreate name /dev/sdbn /dev/sdcn - require the name and the required partitions to use
    pvcreate vgroup /dev/sdb1 /dev/sdc1 
##### to visualize
    sudo vgs
    sudo vgdisplay
    sudo vgscan

#### 3 create logical volumes
    Once we have volume group, we can create logical volumes on top: 
    lvcreate -L 10G -n data vgroup - lvcreate - create logical volume, - n for name, -L the size specify in units and -l to specify the size based on the % of free space in our volume group.
    lvcreate -n name [volume group]
    sudo lvcreate -L 10G -n data vgroup 
    sudo lvcreate -l to specify the % of the volume group
    sudo lvcreate -l 100%FREE -n data vgroup 

    this last command create a logical volume using the 100% of free space of the volume group called vgroup and the partitions will be called data.
    
##### to visualize
    sudo lvs
    sudo lvdisplay
    sudo lvscan - to throubleshooting

#### 4 Once we have our logil volume we can use it as a common partition - we can craete a filesystem and mount it 
    mkfs.ext4 /dev/vgroup/data - the path for the partition we can find it with the sudo lvdisplay command.
    
    
