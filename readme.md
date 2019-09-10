# MediaBox
This repo provides some docker-compose.yml that contains the configurations for a complete mediabox.

## Prerequisites
1. [Docker](https://docs.docker.com/install/)
2. [docker-compose](https://docs.docker.com/compose/install/)
3. [RClone](https://rclone.org/install/)

## Setup
1. Create a `.env` file (copy from `.env.example`)
2. Define the variables that can be found in `.env.example`
3. Create a dns record for every subdomain listed below
4. You need to setup [jwilder/nginx-proxy](https://github.com/jwilder/nginx-proxy) and [jrcs/letsencrypt-nginx-proxy-companion](https://github.com/jrcs/letsencrypt-nginx-proxy-companion) in orther to reverse proxy the containers
5. Connect `nginx-proxy` to the `mediabox-proxy` network.

### RClone
* To setup rclone type `rclone config` and follow the [docs](https://rclone.org/docs/). **DO NOT** use the same `rclone.conf` for  mediabox-rclone docker container and for other purposes because mediabox-rclone need to change the ownership of that file to root, copy your `rclone.conf` to another folder and use that one.
* You may mount a cloud storage to `MOUNT_PATH/cloudname`, inside the containers that path will be mapped to `/mnt/cloudname`

## Run
1. run `docker-compose up -d` in the path of `docker-compose.yml` to run the configuration

## Informations
### Containers
| App       | Container name     | Subdomain | Ports    | Docs                                                      |
| --------- | ------------------ | --------- | -------- | --------------------------------------------------------- |
| Deluge    | mediabox-deluge    | download  | 8112     | [GitHub](https://github.com/binhex/arch-delugevpn)        |
| Sonarr    | mediabox-sonarr    | sonarr    | 8989     | [GitHub](https://github.com/linuxserver/docker-sonarr)    |
| Radarr    | mediabox-radarr    | radarr    | 7878     | [GitHub](https://github.com/linuxserver/docker-radarr)    |
| Lidarr    | mediabox-lidarr    | lidarr    | 8686     | [GitHub](https://github.com/linuxserver/docker-lidarr)    |
| Bazarr    | mediabox-bazarr    | bazarr    | 6767     | [GitHub](https://github.com/linuxserver/docker-bazarr)    |
| Jackett   | mediabox-jackett   | jackett   | 9117     | [GitHub](https://github.com/linuxserver/docker-jackett)   |
| Jellyfin  | mediabox-jellyfin  | stream    | 8096     | [GitHub](https://github.com/linuxserver/docker-jellyfin)  |
| Nextcloud | mediabox-nextcloud | cloud     | 5443:443 | [GitHub](https://github.com/linuxserver/docker-nextcloud) |
| rclone    | mediabox-rclone    |           |          | [GitHub](https://github.com/pfidr34/docker-rclone)        |

### Connecting services
Since the apps that need to be connected are under the same network you can access every app by it's name and its internal port.
For example to connect Deluge and Sonarr the url is: `http://mediabox-deluge:8112`.

### Remote Path Mapping
In Sonarr, Radarr, ecc. `/data/` of `mediabox-deluge` has to be mapped to `/downloads`.

### Cloud path
Conteiners that might use a cloud mount (with [rclone](https://rclone.org)) have a volume for the mount, check `docker-compose.yml` to find out which is the correct path for every container.

### PUID and PGID
You can find these parameters:
```
$ id username
    uid=1000(user) gid=1000(user) groups=1000(user)
```

### Updating
Do not update apps, to update remove images then recreate the containers.

### Watchtower auto updates
It is suggested to use [Watchtower](https://github.com/containrrr/watchtower) with the label `WATCHTOWER_LABEL_ENABLE=true` to auto update only the following containers:

| Container name     | Watchtower enabled |
| ------------------ | ------------------ |
| mediabox-deluge    | true               |
| mediabox-sonarr    | true               |
| mediabox-radarr    | true               |
| mediabox-lidarr    | true               |
| mediabox-bazarr    | true               |
| mediabox-jackett   | true               |
| mediabox-jellyfin  | false              |
| mediabox-nextcloud | false              |
| mediabox-rclone    | false              |