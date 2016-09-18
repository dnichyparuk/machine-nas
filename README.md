# machine-nas
Scripts, how-to, articles to configure zeus, etc...

Features:
- Serviio media player (DLNA)

## Infrastructure
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
