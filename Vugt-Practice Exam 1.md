#  RHCSA Practice Exam A
1.  Configure your system to automatically loop-mount the ISO of the installation disk on the directory **/repo**. Configure your system to remove this loop-mounted ISO as the only repository that is used for installation. 

```bash
mkdir -p /mnt/dvdrom
echo "/dev/sr0 /mnt/dvdrom iso9660 loop,auto 0 0" >> /etc/fstab
mount -a
```

2. Reboot your server. Assume that you don’t know the root password, and use the appropriate mode to enter a root shell that doesn’t require a password. Set the root password to **mypassword**.

```bash
(Boot menu) e
...rhgb quiet systemd.debug-shell=1
passwd

OR

...rhgb quiet rd.break
mount -o remount,rw /sysroot
chroot /sysroot
passwd
touch /.autorelabel

```

3. Set default values for new users. Set the default password validity to 90 days, and set the first UID that is used for new users to 2000.

```bash
vim /etc/login.defs
PASS_MAX_DAYS 	90
UID_MIN			2000
```

4. Create users **edwin** and **santos** and make them members of the group **livingopensource** as a secondary group membership. Also, create users **serene** and **alex** and make them members of the group **operations** as a secondary group.

```bash
useradd edwin
useradd santos
groupadd livingopensource
usermod -aG livingopensource edwin
usermod -aG livingopensource santos
useradd serene
useradd alex
groupadd operations
usermod -aG operations serene
usermod -aG operations alex
```

5.  Create shared group directories **/groups/livingopensource**  and **/groups/operations**, and make sure the groups meet the following requirements:
    -   Members of the group livingopensource have full access to their directory.
    -   Members of the group operations have full access to their directory.
    -   New files that are created in the group directory are group owned by the group owner of the parent directory.
    -   Others have no access to the group directories.
    -   Members of the group operations have read access to the directory/groups/livingopensource.
	
	```bash
	mkdir -p /group/livingopensource
	mkdir -p /group/operations
	chown -R :livingopensource /group/livingopensource
	chown -R :operations /group/operations
	chmod g=2770 /group/livingopensource
	chmod g=2770 /group/operations
	setfacl -R -m g:operations:rx /group/operations
	```
	
6. Create a 2-GiB volume group, using 8-MiB physical extents. In this volume group, create a 500-MiB logical volume with the name **mydata,** and mount it persistently on the directory /mydata.
	
	```bash
	gdisk /dev/sdb
	vgcreate myvg -s 8M /dev/sdb1
	lvcreate -n mydata -L 500M myvg
	mkfs.xfs /dev/mapper/myvg-mydata
	mkdir /mydata
	echo "/dev/mapper/myvg-mydata /mydata xfs defaults 0 0" >> /etc/fstab
	mount -a
	```

7. Find all files that are owned by user edwin and copy them to the directory/rootedwinfiles.
```bash
mkdir /rootedwinfiles
find / -user edwin -exec cp {} /rootedwinfiles \; 2>/dev/null
```

8. Schedule a task that runs the command **touch /etc/motd** every day from Monday through Friday at 2 a.m.

```bash
crontab -e
00 02 * * 1-5 touch /etc/motd
```

9. Add a new 10-GiB virtual disk to your virtual machine. On this disk, add a VDO volume with a size of 20 GiB and mount it persistently.
```bash
dnf install vdo kmod-kvdo -y
vdo create --name vdo1 --device /dev/sdb --vdoLogicalSize 20G
mkfs.xfs /dev/mapper/vdo1
echo "/dev/mapper/vdo1 /mnt/vdo1 xfs defaults 0 0" >> /etc/fstab
mount -a
```
10. Install the vsftpd service and ensure that it is started automatically at reboot.
```bash
dnf install vsftpd -y
systemctl enable --now vsftpd.service
firewall-cmd --add-service=ftp --permanent
firewall-cmd --reload
```

11. Create a container that runs the rsyslog service. This container should be configured to write log files persistently to the directory /var/log/logcontainer/ on the host operating system. Run this container with the same user account that the rsyslog service normally uses.

```bash
yum module install container-tools -y
podman login registry.redhat.io
podman search rsyslog
podman pull registry.redhat.io/library/rsyslog
podman run -d -n rsyslog1 -p 514:514 -v /var/log/logcontainer:/var/log:Z rsyslog
```
12. Configure this container such that it is automatically started on system boot as a system user service.

```bash
mkdir -p ~/.config/systemd/user
cd ~/.config/systemd/user/
podman generate systemd rsyslog --files --new
loginctl enable-linger
systemctl --user daemon-reload
systemctl --user enable --now container-rsyslog
```