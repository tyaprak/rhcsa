
#  RHCSA Practice Exam C
1. Create a 4-GiB volume group, using a physical extent size of 2 MiB. In this volume group, create a 1-GiB logical volume with the name **myfiles** and mount it persistently on /myfiles.

```bash
gdisk /dev/sdb
<create a new 4G partition>
vgcreate myvg -s 2M /dev/sdb1
lvcreate --name myfiles -L 1G myvg
mkfs.xfs /dev/mapper/myvg-myfiles
mkdir -p /myfiles
echo "/dev/mapper/myvg-myfiles /myfiles xfs defaults 0 0" >> /etc/fstab
mount -a
```

2. Create a group **sysadmins**. Make users edwin and santos members of this group and ensure that all members of this group can run all administrative commands using **sudo**.

```bash
visudo
%sysadmins ALL=(ALL)	ALL
```

3. Optimize your server with the appropriate profile that optimizes throughput.

```bash
tuned-adm list
tuned-adm profile network-throughput
tuned-adm active
```

4. Add a new disk to your virtual machine with a size of 10 GiB. On this disk, create a VDO volume with a size of 50 GiB and mount it persistently.

```bash
vdo create --name myvdo --device /dev/nvme0n1 --vdoLogicalSize 50G
```

5. Configure a web server to use the nondefault document root /webfiles. In this directory, create a file **index.html** that has the contents **hello world** and then test that it works.

```bash
dnf install httpd -y
vim /etc/httpd/conf/httpd.conf
edit /var/www/html -> /webfiles
semanage fcontext -a -t httpd_sys_content_t "/webfiles(/.*)?"
echo "hello world" >> /webfiles/index.html
restorecon -Rv /webfiles
firewall-cmd --add-service=http --permanent
firewall-cmd --reload
```

6. Configure your system to automatically start a mariadb container. This container should expose its services at port 3306 and use the directory /var/mariadb-container on the host for persistent storage of files it writes to the /var directory.

```bash
dnf module install container-tools
podman login
podman pull registry.redhat.io/mariadb:10.8.2-rc
mkdir /var/mariadb-container
podman run -d -n maria -p 3306:3306 -v /var/mariadb-container:/var:Z mariadb:10.8.2-rc
loginctl enable-linger
systemctl --user daemon-reload
mkdir ~/.config/systemd/user/
podman generate systemd maria --files --new
systemctl --user enable --now container-maria
```