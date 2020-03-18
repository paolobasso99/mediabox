# MediaBox
This repo provides some docker-compose.yml that contains the configurations for a complete mediabox.

## Prerequisites
1. [Docker](https://docs.docker.com/install/)
2. [docker-compose](https://docs.docker.com/compose/install/)

## Setup
1. Create a `.env` file (copy from `.env.example`)
2. Define the variables that can be found in `.env.example`
3. Create a dns record for every subdomain listed below
4. You need to setup [jwilder/nginx-proxy](https://github.com/jwilder/nginx-proxy) and [jrcs/letsencrypt-nginx-proxy-companion](https://github.com/jrcs/letsencrypt-nginx-proxy-companion) in orther to reverse proxy the containers
5. Connect `nginx-proxy` to the `mediabox-proxy` network.
6. In Deluge remember to change the default download path to `/downloads` from the UI and CHOWN the download folder!

### RClone mount
You may mount a cloud storage to `MOUNT_PATH`, inside most containers that path will be mapped to `/mnt/rclone` but some may have a different path

## Run
1. run `docker-compose up -d` in the path of `docker-compose.yml` to run the configuration

## Informations
### Containers
| App       | Container name     | Docs                                                      |
| --------- | ------------------ | --------------------------------------------------------- |
| Deluge    | mediabox-deluge    | [GitHub](https://github.com/binhex/arch-delugevpn)        |
| Sonarr    | mediabox-sonarr    | [GitHub](https://github.com/linuxserver/docker-sonarr)    |
| Radarr    | mediabox-radarr    | [GitHub](https://github.com/linuxserver/docker-radarr)    |
| Bazarr    | mediabox-bazarr    | [GitHub](https://github.com/linuxserver/docker-bazarr)    |
| Jackett   | mediabox-jackett   | [GitHub](https://github.com/linuxserver/docker-jackett)   |
| Webdav    | mediabox-webdav    | [GitHub](https://hub.docker.com/r/bytemark/webdav/)       |
| Filestash | mediabox-filestash | [GitHub](https://github.com/mickael-kerjean/filestash)    |

### Connecting services
Since the apps that need to be connected are under the same network you can access every app by it's name and its internal port.
For example to connect Deluge and Sonarr the url is: `http://mediabox-deluge:8112`.

### Flexget
Check [Flexget image docs](https://github.com/cpoppema/docker-flexget) and [flexget.com](https://flexget.com/) to setup Flexget.

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
