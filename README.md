# machine-nas
Scripts, how-to, articles to configure the machine, etc...

Features:
- [Docker UI](https://github.com/kevana/ui-for-docker)
- Serviio media player (DLNA) (not in docker now)
- [Plex](https://hub.docker.com/r/linuxserver/plex/)
- [Crashplan backup](https://github.com/JrCs/docker-crashplan)
- [Transmission](https://hub.docker.com/r/linuxserver/transmission/)
- openhab (not in docker now)
- [MariaDB as storage (tt-rss, openhab, etc...)](https://hub.docker.com/r/linuxserver/mariadb/)
- [tt-rss](https://hub.docker.com/r/linuxserver/tt-rss/)
- [muximux](https://hub.docker.com/r/linuxserver/muximux/)
- [openhab](https://hub.docker.com/r/openhab/openhab/)

## Infrastructure
### Folders
```sh
sudo -i
mkdir -p /docker/{crashplan,plex,serviio,transmission}/config
chown -R kwull:kwull /docker
```
### VPN: [OpenVPN](https://www.digitalocean.com/community/tutorials/how-to-set-up-an-openvpn-server-on-ubuntu-16-04) 
### Disk consolidation: [mergerfs](https://github.com/trapexit/mergerfs) 
```sh
sudo -i
wget https://github.com/trapexit/mergerfs/releases/download/2.15.0/mergerfs_2.15.0.ubuntu-xenial_amd64.deb
dpkg -i mergerfs_2.15.0.ubuntu-xenial_amd64.deb
rm mergerfs*.deb
```

/etc/fstab:
```sh
# NAS Disks
UUID=4382b225-86db-4401-9c5d-9040606f812d /media/disk1 ext4 defaults,noatime,nofail 0 2
UUID=59a38c9f-03de-4fcb-9b4a-bb4c1aea4b54 /media/disk2 ext4 defaults,noatime,nofail 0 2
UUID=06391d3b-e722-424e-a681-1ba9d79dd163 /media/disk4 ext4 defaults,noatime,nofail 0 2

# Disk pool
/media/disk* /media/nas fuse.mergerfs category.create=epmfs,defaults,allow_other,minfreespace=20G,fsname=mergerfsPool,func.getattr=newest,x-systemd.requires=media-disk1,x-systemd.requires=media-disk2,x-systemd.requires=media-disk4 0 00
```

## Docker containers

### docker-compose
```sh
curl -L https://github.com/docker/compose/releases/download/1.10.0/run.sh > /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose
```

To update everything:
```sh
docker-compose pull
docker-compose up -d
```

### [systemd autostart example](https://docs.docker.com/engine/admin/host_integration/)

/etc/systemd/system/serviio.service
```sh
[Unit]
Description=Serviio container
Requires=docker.service media-nas.mount
After=docker.service media-nas.mount

[Service]
Restart=always
ExecStart=/usr/bin/docker start -a serviio
ExecStop=/usr/bin/docker stop -t 2 serviio

[Install]
WantedBy=default.target
```
```sh
systemctl daemon-reload
systemctl enable serviio
systemctl start serviio
```

### [Serviio](https://github.com/Kwull/docker-serviio)

```sh
docker create --name=serviio \
-v /etc/localtime:/etc/localtime:ro \
-v /docker/serviio/config:/config \
-v /docker/serviio/transcode:/transcode \
-v /media/nas/Transmission/Completed:/media/Completed \
-v /media/nas/Video:/media/Video \
-v /media/nas/Music:/media/Music \
-v /media/nas/Album:/media/Album \
-e PGID=1000 -e PUID=1000 \
--net=host kwull/serviio
```
sudo ln -sfn /media/disk1/yandex_disk yandex_disk
sudo ln -sfn /media/disk0/video video
sudo ln -sfn /media/disk1/transmission transmission
sudo ln -sfn /media/disk1/transcode transcode
sudo ln -sfn /media/disk1/share share
