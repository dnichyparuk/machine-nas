# machine-nas
Scripts, how-to, articles to configure zeus, etc...

Features:
- Serviio media player (DLNA)

## Infrastructure
- OpenVPN - VPN
- mergerfs - disk consolidation

## Docker containers

### Serviio
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
