# Container - httpd
```bash
yum module install container-tools -y

podman login 'http://registry.example.com'

skopeo search httpd
skopeo inspect docker://registry.redhat.io/rhel8/httpd-24

podman pull 'http://registry.example.com/httpd:ver'
podman run -d -n mysite1 -p 80:80 -v /my/data:/var/www/html:Z httpd:ver

loginctl enable-linger
systemctl --user daemon-reload

cd ~/.config/systemd/user/

podman generate systemd --name mysite1 --files --new
podman generate systemd --name <container-name> --files --new

systemctl --user enable --now container-mysite1 

```