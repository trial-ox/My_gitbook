```
docker run -d \
    --name aria2-pro \
    --restart unless-stopped \
    --log-opt max-size=1m \
    -e RPC_SECRET=eO800d6ubnQ5kboi\
    -e RPC_PORT=6800 \
    -e RPC_PORT=6800 \
    -p 6800:6800 \
    -e LISTEN_PORT=6888 \
    -p 6888:6888 \
    -p 6888:6888/udp \
    -v /root/aria2-config:/config \
    -v /root/aria2-downloads:/downloads \
    p3terx/aria2-pro
```



| Parameter                      | Function                                                     |
| ------------------------------ | ------------------------------------------------------------ |
| `-e PUID=$UID` `-e PGID=$GID`  | Bind UID and GID to the container, which means you can use a non-root user to manage downloaded files. |
| `-e UMASK_SET=022`             | For umask setting of Aria2, optional , default if left unset is `022` |
| `-e RPC_SECRET=<TOKEN>`        | Set RPC secret authorization token. Default: `P3TERX`        |
| `-e RPC_PORT=6800`             | Set RPC listen port.                                         |
| `-p 6800:6800`                 | bind RPC listen port.                                        |
| `-e LISTEN_PORT=6888`          | Set TCP/UDP port number for BitTorrent/DHT listen.           |
| `-p 6888:6888`                 | Bind BT listen port (TCP).                                   |
| `-p 6888:6888/udp`             | Bind DHT lisen port (UDP).                                   |
| `-v <PATH>:/config`            | Contains all relevant configuration files.                   |
| `-v <PATH>:/downloads`         | Location of downloads on disk.                               |
| `-e DISK_CACHE=<SIZE>`         | Set up disk cache. SIZE can include `K` or `M` (1K = 1024, 1M = 1024K), e.g `64M`. |
| `-e IPV6_MODE=<BOOLEAN>`       | Whether to enable IPv6 support for Aria2. Optional: `true` or `false`. Set the options `disable-ipv6=false` and `enable-dht6=true` in the configuration file(aria2.conf). |
| `-e UPDATE_TRACKERS=<BOOLEAN>` | Whether to update BT Trackers List automatically. Optional: `true` or `flase`, default if left unset is `true` |
| `-e CUSTOM_TRACKER_URL=<URL>`  | Custom BT Trackers List URL. If not set, it will be get from https://trackerslist.com/all_aria2.txt. |
| `-e TZ=Asia/Shanghai`          | Specify a timezone to use e.g. `Asia/Shanghai`               |