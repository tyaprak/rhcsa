# Managing Logical Volumes
- Create a 2GiB LVM volume group with the name vgdata
- In this volume group, create a 1GiB logical volume with the name lvdata
- Format this logical volume with the XFS file system and mount it persistently on the directory /lvdata
- Restart your computer and after a restart add another 500MiB to the XFS file system that was created on top of this logical volume
```bash
# Check the disk type with parted.
# If it is msdos then continue with fdisk.
# Otherwise gdisk is also an alternative.
parted /dev/nvme0n1
fdisk /dev/nvme0n1
#Create a primary or extended partition, select the one suitable for you
change partition type to Linux LVM
vgcreate vgdata /dev/nvme0n1p5
lvcreate -n lvdata -L 1G vgdata
mkfs.xfs /dev/vgdata/lvdata
vim /etc/fstab
/dev/vgdata/lvdata /lvdata xfs defaults 0 0
lvextend -r -L +500M /dev/vgdata/lvdata #'-r' flag is very important
```