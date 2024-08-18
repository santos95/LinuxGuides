## INSTALL LVM  - often is not already installed in ubuntu
    sudp apt install lvm2

### LVM ALLOWS TO WORK WITH MANY DRIVERS AS ONE DRIVE - Service to manage logical volume - Logical Volume managment

### Steps to create logical volumes
    1 - create a physical volume 
    2 - create a volume group 
    3 - create a logical volume

### hows this work
    1 - PV - physical volumes - this are a actual disk partition reserved to lvm - to create it we have to create a partition and mark it as lvm partition - Then we crate the actual pv, with the pvcreate command
    2 - Volume Group - the volume group is placed on top of the pv, allows to combine the space of multiple disk or patitions as single one space - Responsable to store the data in the physical partitions - Works as a container - Map the logical partition with the actual physical storage, to store in the actual physical disk the data stored in the logical volumes. 
    Works as device mapper, allowing the combination of multiple partitions from one or multiple disks. Mapper, allow to combine multiple devices to support the data storage from one or multiple logical partitions.
    3 - Logical Volume - Partition created on top of the volume group. This partitions is that we actually use it to create of filesystem and mounted into our system. 
    With this we did not directly write in our disks, we use a layer in between that is responsible to storage the data in the physical part - volume group. The layer is refered to the volumgroup. 

#### create a logical volumes from scratch from two devices used 100% to that
#### 1 create pv - Fist, is a good practice to format the disk, create a partition table, create the partition and set it to be used as lvm. Then we create the actual pv. 
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
##### to visualize info about pv - The units specify a amount of data which represent a block into the pv.
    sudo pvs - short info
    sudo pvdisplay - detailed info about pv
    sudo pvscan - to troubleshooting - scan or search for pv when we can't see it with pvs or pvdisplay

#### 2 create a volume group - once that exists pv or is initialized we can create a volume group - To create a volume group we specify one or multitple pv to be used to support that volume group, so the disk that the vg will manage in order to handle the actual read/write process from the logical volumes to the physical volumes:
    pvcreate name /dev/sdbn /dev/sdcn - require the name and the required partitions to use
    pvcreate vgroup /dev/sdb1 /dev/sdc1 
##### to visualize
    sudo vgs
    sudo vgdisplay
    sudo vgscan

#### 3 create logical volumes. Once we have volume group, we can create logical volumes on top: 
    lvcreate -L 10G -n data vgroup - lvcreate - create logical volume, - n for name, -L the size specify in units and -l to specify the size based on the % of free space in our volume group.
    lvcreate -n name [volume group]
    sudo lvcreate -L 10G -n data vgroup 
    sudo lvcreate -l to specify the % of the volume group
    sudo lvcreate -l 100%FREE -n data vgroup 
    sudo lvcrate -l 100%FREE -n data vgroup -n to give a name to the logical volume, and, the main parameter is the volume group to use to create the logical volume
    sudo lvcrate -l 100%FREE -n backcups vgroup1 
    

    this last command create a logical volume using the 100% of free space of the volume group called vgroup and the partitions will be called data.
    
##### to visualize
    sudo lvs
    sudo lvdisplay
    sudo lvscan - to throubleshooting

#### 4 Once we have our logic volume we can use it as a common partition - we can craete a filesystem and mount it 
    mkfs.ext4 /dev/vgroup/data - the path for the partition we can find it with the sudo lvdisplay command.

#### GUIDE TO CREATE LOGICAL VOLUMES
    sudo parted 
    select /dev/sdb 
    mklabel - prompt - gpt
    mkpart primary 0% 100%
    set 1 lvm on 
    print - must show in flats - lvm
    sudo pvcreate /dev/sdb1 - in this case only one partition, that use 100% of the size 
    
    sudo parted 
    select /dev/sdc 
    mklabel - prompt - gpt
    mkpart primary 0% 100%
    set 1 lvm on 
    print - must show in flats - lvm
    sudo pvcreate /dev/sdc1 - in this case only one partition, that use 100% of the size 
    
    pvcreate vgroup /dev/sdb1 /dev/sdc1     

    sudo lvcreate -l 100%FREE -n data vgroup

#### MANAGE LVM
##### Modify Volume Group - Add a physical volume
##### Extend de volume group with aditional pv - using the command vgextend - require the volume group to modify and the pv to add
    sudo parted
    select /dev/sdd
    mklabel 
    mkpart primary 0% 50%
    set 1 lvm on

    sudo pvcreate /dev/sdd1
    sudo vgextend vgroup /dev/sdd1 
    sudo vgextend vgroup - volume group name - /dev/sdd1 - the pv to add

    sudo vgs 
    sudo pvs

#### Remove or replace a physical volume - with this above configuration - with one pv added we can move the data from one pv and move it to the new one and then remove the pv - this use case mean that for some reason is in bad state the disk that support the pv and we have to remove it and destroy the data to destroy it - There are the steps for this, 1 - move the data of the pv to the free space available (be sure to have enough free space in volume group). 2 - Remove the pv from the volume group with the vgreduce command. 3 - Remove the physical volume - Actually remove the pv 
    sudo pvmove -v physical-volume
    sudo pvmove -v /dev/sdc1
    
    vgreduce volume-group-name physical-volume
    vgreduce vgroup /dev/sdc1

    sudo pvremove /dev/sdc1

##### after this, the /dev/sdc1 is removed as a physical volume, and if we check it in parted, we can see it has his configuration normal as lvm partition, from this, we can overwrite and create a new partition table and add new data to destroy production data stored in a physical way. Or may be, we can crate or avoid to remove the pv and we can crate or add it to another volume group. 

#### Increase the size of a logical partition - to do this we have or allocate more space to our physical volume, and new physical volume or if we have some free space in our volume group, we can allocate that free space an increase the size of a logical volume. For this a disk /dev/sdd with two partitions, remove the second partition and resize the first partition - the 2 partition was removed from the vgroup and removed the pv. Create a single partition for the pv
##### 1 - resize the lvm partition     
    sudo parted
    select /dev/sdd
    rm 2 
    resizepart 1 100% - resize the partition 1 to the 100% of free space of the disk

##### 2 - resize the physical volume - resize the physical volume at the total available size of the partition
    sudo pvresize -v /dev/sdd1     
    sudo pvs -o+pv_used - check free space

##### 3 - extend the logical volume
    sudo lvexted -l +100%FREE /dev/vgroup/data

##### 4 - resize the filesystem - resize the filesystem at the available space in the logical volume or partition
    sudo resize2fs /dev/vgroup/data 

##### 5 - add the logical volume into the fstab 
    sudo nano /etc/fstab
    /dev/vgroup/data /mnt/data ext4 defaults 0 2
    sudo mount -a

###### to increase the size of the logical volume we need free space into the volume group, then we use lvexted to resize the logical volume, once the logical volume is extended we can extend the size of the filesystem to actually use the new size in our applications.
    lvextend -L 20GB /dev/vgroup/data extend the size of the lv to 20GB
    lvextend -L +1GB /dev/vgroup/data extend one 1gb to the size of the lv
    lvextend -l +100%FREE /dev/vgroup/data extend to the 100% of free space of lv

### REMOVE LVM
#### 1 - Chech the fstab config file and remove every entry that refers or mount a logical volume.
nano /etc/fstab 
#### 2 - Once that fstab entries for a logical volume, we can remove a logical volume:
lvremove /dev/vgroup/data
lvremove /dev/vgroup/backup
#### 3 - Once that our logical volumes are removed we have to remove the volume group:
vgremove vgroup 
#### 4 - Once that our volume group is removed, we have to remove the physical volumes:
pvremove /dev/sdb1
pvremove /dev/sdc1
pvremove /dev/sdd1


    




    






    
