# Container - httpd
```bash
#root user
yum module install container-tools -y
mkdir /webfiles
chown username /webfiles
loginctl enable-linger <username>
loginctl show-user <username> # Check Linger=yes

#<regular user username>
podman login 'http://registry.access.redhat.com'
skopeo search nginx
skopeo inspect docker://registry.redhat.io/rhel8/nginx:latest
podman pull 'http://registry.example.com/nginx:latest'
podman run -d -p 8080:80 -v /webfiles:/var/lib/nginx:Z nginx:latest
mkdir -p ~/.config/systemd/user/ 
cd ~/.config/systemd/user/
podman generate systemd --name mysite1 --files 

#<ssh needed to use --user flag>
systemctl --user daemon-reload
systemctl --user enable --now container-mysite1 

firewall-cmd --add-port 8080/tcp --permanent
OR
semanage port -a -t http_port_t -p tcp 8080
firewall-cmd --add-service=http --permanent

firewall-cmd --reload
```