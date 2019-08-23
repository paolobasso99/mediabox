# MediaBox
This repo provides some docker-compose.yml that contains the configurations for a complete mediabox.

## Prerequisites
1. [Docker](https://docs.docker.com/install/)
2. [docker-compose](https://docs.docker.com/compose/install/)
3. [RClone](https://rclone.org/install/)

## Setup
1. Create a `.env` file (copy from `.env.example`)
2. Define the variables that can be found in `.env.example`
3. Create an empty file: `CONFIG_PATH/filebrowser/database.db`
4. Create a dns record for every subdomain listed below

### RClone
To setup rclone type `rclone config` and follow the [docs](https://rclone.org/docs/).

## Run
1. run `docker-compose up -d` in the path of `docker-compose.yml` to run the configuration

## Informations
### Containers
| App         | Container name       | Subdomain | Ports   | Docs                                                                       |
| ----------- | -------------------- | --------- | ------- | -------------------------------------------------------------------------- |
| Deluge      | mediabox-deluge      | download  | 8112    | [GitHub](https://github.com/binhex/arch-delugevpn)                         |
| Sonarr      | mediabox-sonarr      | sonarr    | 8989    | [GitHub](https://github.com/linuxserver/docker-sonarr)                     |
| Radarr      | mediabox-radarr      | radarr    | 7878    | [GitHub](https://github.com/linuxserver/docker-radarr)                     |
| Lidarr      | mediabox-lidarr      | lidarr    | 8686    | [GitHub](https://github.com/linuxserver/docker-lidarr)                     |
| Bazarr      | mediabox-bazarr      | bazarr    | 6767    | [GitHub](https://github.com/linuxserver/docker-bazarr)                     |
| Jackett     | mediabox-jackett     | jackett   | 9117    | [GitHub](https://github.com/linuxserver/docker-jackett)                    |
| Jellyfin    | mediabox-jellyfin    | jellyfin  | 8096    | [GitHub](https://github.com/linuxserver/docker-jellyfin)                   |
| Filebrowser | mediabox-filebrowser | cloud     | 8282:80 | [GitHub](https://github.com/filebrowser/filebrowser)                       |
| Nginx-proxy | mediabox-proxy       |           | 80, 443 | [GitHub](https://github.com/jwilder/nginx-proxy)                           |
| Letsencrypt | mediabox-letsencrypt |           |         | [GitHub](https://github.com/JrCs/docker-letsencrypt-nginx-proxy-companion) |
| RClone      | mediabox-rclone      |           |         | [GitHub](https://github.com/pfidr34/docker-rclone)                         |


### Connecting services
Since the apps that need to be connected are under the same network you can access every app by it's name and its internal port.
For example to connect Deluge and Sonarr the url is: `http://mediabox-deluge:8112`.

#### Remoe Porth Mapping
In Sonarr, Radarr, ecc. `/data/` of `mediabox-deluge` has to be mapped to `/downloads`.

### PUID and PGID
You can find these parameters:
```
$ id username
    uid=1000(user) gid=1000(user) groups=1000(user)
```

### Updating
Do not update apps, to update remove images then recreate the containers.

### Manual refresh ssl
Use `docker exec mediabox-letsencrypt /app/signal_le_service && docker logs -f mediabox-letsencrypt` to create new ssl certificates.