# MediaBox
My Ubuntu mediabox server configurations. This repo provides a docker-compose.yml to run different services and how I configure rclone.

## Prerequisites
1. [Docker](https://docs.docker.com/install/)
2. [docker-compose](https://docs.docker.com/compose/install/)
3. [rclone](https://rclone.org/)
4. [Traefik](https://docs.traefik.io) to reverse proxy the containers

## Setup rclone
In this configuration Deluge downloads a file in the local storage and then Sonarr/Radarr move the files inside a cloud storage (with almost ulimited space). This setup is insiped by [animosity22](https://github.com/animosity22/homescripts).
To achieve this the first step is to mount rclone:
1. Install `fuse` (`sudo apt-get install fuse`)
2. Enable `user_allow_other` in `/etc/fuse.conf`
3. Download rclone for Ubuntu [here](https://rclone.org/install/)
4. Create a folder in `${MOUNT_ROOT_PATH}/cloudstorage` that will be the full mount path and chown it
5. Configure rclone with `rclone config` and chown the configuration folder (usually inside `/home/username/.config`)
6. Create a file where to write rclone logs for example in `/var/log/rclone/rclone.log` and chown it
7. Edit `rclone.service` with the right parameters (highlighted with __)
8. Move `rclone.service` to `/etc/systemd/system/rclone.service`
9. Start the service with `sudo systemctl start rclone.service`


## Setup
1. Create a `.env` file (copy from `.env.example`)
2. Define the variables that can be found in `.env.example`
3. Create a DNS record for every subdomain listed below
4. You need to setup [Traefik](https://docs.traefik.io) in orther to reverse proxy the containers
5. Copy `./config` folder to the `CONFIG_PATH`

## Run
1. Create a web_proxy network `docker network create web_proxy`
2. Connect `traefik` to the `web_proxy` network
3. Run `docker-compose up -d` in the path of `docker-compose.yml` to run the configuration
4. Wait for `traefik` to generate certificates
5. In Deluge remember to change the default download path to `/downloads` from the UI and CHOWN the download folder!

## Information
### Containers
| App             | Container name           | Active by default | Docs                                                     |
| --------------- | ------------------------ | ----------------- | -------------------------------------------------------- |
| Deluge          | mediabox-deluge          | Yes               | [GitHub](https://github.com/binhex/arch-delugevpn)       |
| Deluge Exporter | mediabox-deluge-exporter | Yes               | [GitHub](https://github.com/tobbez/deluge_exporter)      |
| Sonarr          | mediabox-sonarr          | Yes               | [GitHub](https://github.com/linuxserver/docker-sonarr)   |
| Radarr          | mediabox-radarr          | Yes               | [GitHub](https://github.com/linuxserver/docker-radarr)   |
| Lidarr          | mediabox-lidarr          | Yes               | [GitHub](https://github.com/linuxserver/docker-lidarr)   |
| Bazarr          | mediabox-bazarr          | Yes               | [GitHub](https://github.com/linuxserver/docker-bazarr)   |
| Jackett         | mediabox-jackett         | Yes               | [GitHub](https://github.com/linuxserver/docker-jackett)  |
| Jellyfin        | mediabox-jellyfin        | Yes               | [GitHub](https://github.com/linuxserver/docker-jellyfin) |
| EmbyStat        | mediabox-embystat        | No                | [GitHub](https://github.com/linuxserver/docker-embystat) |
| Ombi            | mediabox-ombi            | No                | [GitHub](https://github.com/linuxserver/docker-ombi)     |
| LazyLibrarian   | mediabox-lazylibrarian   | Yes               | [GitLab](https://gitlab.com/LazyLibrarian/LazyLibrarian) |
| Calibre         | mediabox-calibre         | Yes               | [Website](https://calibre-ebook.com/)                    |
| calibre-web     | mediabox-calibre-web     | No                | [GitHub](https://github.com/janeczku/calibre-web)        |
| ubooquity       | mediabox-ubooquity       | No                | [Website](https://vaemendis.net/ubooquity/)              |

If an app is not active by default it means that the corresponding service is commented in the `docker-compose.yml`.
### Deluge Prometheus Exporter
At `https://$DELUGE_SUBDOMAIN.$DOMAIN/metrics` there will be exposed some Prometheus metrics for Deluge.
You may want to add this Job to Prometheus:

    - job_name: 'mediabox-deluge'
      basic_auth:
        username: user
        password: password
      static_configs:
        - targets:
          - 'deluge.example.com'

### Sonarr/Radarr
Set **`Analyse video files Off `** This does full downloads to perform analysis and should be turned off as this happens frequently on library refreshes, if left on.

### Connecting services
Since the apps that need to be connected are under the same network you can access every app by it's name and its internal port.
For example to connect Deluge and Sonarr the url is: `http://mediabox-deluge:8112`.

### PUID and PGID
You can find these parameters:
```
$ id username
    uid=1000(user) gid=1000(user) groups=1000(user)
```

### Updating
Do not update apps, to update remove images then recreate the containers.
