# INSTALL POSTGRES USING A LVM  

## 1 CREATE THE LOGICAL VOLUME FOR /var/lib/postgres
    sudo parted
    sudo select /dev/sdb

### create the partition on the new disk for the partition
    mklabel > gpt
    mkpart primary 0% 60%
    set 1 lvm on 

### create the pv 
    sudo pvcreate /dev/sdb1

### create the vg 
    sudo vgcreate postgres-vg /dev/sdb1 

### create the lv
    sudo lvcreate -l 100%FREE -n postgres-lv postgres-vg

### create the filesystem - Format the Logical Volum
    sudo mkfs.ext4 /dev/postgres-vg/postgres-lv

### mount the lv
#### create the mount point 
    sudo mkdir -p /var/lib/postgres
    sudo mount /dev/postgres-vg/postgres-lv /var/lib/postgres
#### add the mount point to fstab to make it persistent across reboots
    echo '/dev/postgres-vg/postgres-lv /var/lib/postgres ext4 defaults 0 0' | sudo tee -a /etc/fstab
    sudo mount -a

## 2 INTALL POSTGRES
    sudo apt install postgresql postgresql-contrib
    sudo systemctl status postgresql

### 2.1 move the data to the new partition - move the existing data directory to /var/lib/postgres
    sudo systemctl stop postgresql
    sudo mv /var/lib/postgresql /var/lib/postgres

### 2.2 Update the Data Directory in PostgreSQL Configuration - to point the configuration file to the new data directory - modify the data_directory parameter value 
    sudo nano /etc/postgresql/16/main/postgresql.conf
    /var/lib/postgres/postgresql/16/main

### 2.3 Update the Systemd Service File (if necessary): - Ensure the Environment and ExecStart lines point to the correct data directory
    sudo nano /lib/systemd/system/postgresql.service

### 2.4 start the service
    sudo systemctl daemon-reload
    sudo systemctl start postgresql

### 2.5 verify the data directory
    sudo journalctl -u postgresql
    pg_lsclusters

## 3 Secure PostgreSQL -- set a password for postgres user
    sudo -i -u postgres
    psql -c "ALTER USER postgres WITH PASSWORD 'passwords';"


