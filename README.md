# This is a custom Dockerfile of grafana docker image. Following bug has been fixed.

## permission denied to /etc/grafana/grafana.ini
## grafana         | t=2019-04-24T13:03:38+0000 lvl=eror msg="Exec failed" logger=migrator error="attempt to write a readonly database" sql="UPDATE org_user SET role = 'Viewer' WHERE role = 'Read Only Editor'"

Look for grafana storage on host:

$ docker volume ls|grep grafana 

check ownership/permission: 

ls -lah $(docker volume inspect <replace volume name you get in above command>  --format "{{.Mountpoint}}")

Example : 

ls -lah $(docker volume inspect dockprom_grafana_data  --format "{{.Mountpoint}}")
total 108K
drwxrwxrwx.  4  104  107   55 Jan 15 17:48 .
drwxr-xr-x.  3 root root   19 Jan 11 07:25 ..
-rw-r--r--.  1  104  107 107K Jan 15 17:48 grafana.db
drwxrwxrwx.  2  104  107    6 Apr 26  2018 plugins
drwx------. 12  104  107   96 Apr 24 10:41 sessions

For me I was using this : 
https://github.com/grafana/grafana-docker/blob/master/Dockerfile

and ownership is 472. They changed it as per 

https://grafana.com/docs/installation/docker/#migration-from-a-previous-version-of-the-docker-container-to-5-1-or-later

So to fix it on host :

chown 472:472 --recursive $(docker volume inspect <change grafana volume name> --format "{{.Mountpoint}}")
