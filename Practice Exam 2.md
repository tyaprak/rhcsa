#  RHCSA Practice Exam B

1. Create a 1-GB XFS partition on /dev/sdb. Mount it persistently on the directory /mydata, using the label **mylabel**.

```bash
gdisk /dev/sdb
<create a new 1GB partition as follow: n, +1G, w, y>
mkfs.xfs -L mylabel /dev/sdb1
mkdir /mydata
vim /etc/fstab
/dev/sdb1 /mydata xfs defaults 0 0
mount -a
```
2. Set default values for new users. Ensure that an empty file with the name NEWFILE is copied to the home directory of each new user that is created.
```bash
vim /etc/login.defs
touch /etc/skel/NEWFILE
```
3. Create a 2-GiB swap partition and mount it persistently.

```bash
gdisk /dev/sdb
<create a new 2GB partition as follow: n, +2G, w, y>
mkswap /dev/sdb2
swapon /dev/sdb2
echo "/dev/sdb2 none swap defaults 0 0" >> /etc/fstab
mount -a
```

4. Resize the LVM logical volume that contains the root file system and add 1 GiB.

```bash
gdisk /dev/sdb
<create a new 1GB partition as follow: n, +1G, w, y>
pvcreate /dev/sdb3
vgextend rhel /dev/sdb3
lvextend /dev/rhel/root /dev/sdb3
```

5. Set your server to use the recommended tuned profile.

```bash
tuned-adm profile $(tuned-adm recommend)
tuned-adm active
```

6. Create user **vicky** with the custom UID **2008**.

```bash
useradd -u 2008 vicky
```

7. Configure your server to synchronize time with myserver.example.com.

```bash
dnf install chrony -y
timedatectl set-ntp true
vim /etc/chrony.conf
pool myserver.example.com iburst
systemctl enable --now chronyd.service
```

8.  Install a web server and ensure that it is started automatically.
```bash
dnf install httpd -y
systemctl enable --now httpd.service
firewall-cmd --add-service=http --permanent
firewall-cmd --reload
```