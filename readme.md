# MediaBox
This repo provides some docker-compose.yml that contains the configurations for a complete mediabox.

## Prerequisites
1. Docker
2. docker-compose

## Setup
1. Create a `.env` file (copy from `.env.example`)
2. Define the variables that can be found in `.env.example`
3. Go in `DOCKER_COMPOSE_PATH/seedbox-rtorrent/nginx` and create a password for rtorrent: `sudo htpasswd -c .htpasswd username`
4. Add in `DOCKER_COMPOSE_PATH/seedbox-rtorrent/nginx/nginx.conf`, below `Basic settings`:
```
    auth_basic "Administrators Area";
    auth_basic_user_file /config/nginx/.htpasswd;
```
5. Create an empty file: `DOCKER_COMPOSE_PATH/filebrowser/database.db`

## Run
1. run `docker-compose up` in the path of `docker-compose.yml` to run the configuration

## Informations
### Containers
| App         | Container name   | URL  |
| ----------- | ---------------- | --------- |
| rTorrent    | seedbox-rtorrent | download.domain.com   |


### Connecting services
Since the apps that need to be connected are under the same network you can access every app by it's name and its internal port.
For example to connect rTorrent in Sonarr the url is: `http://seedbox-rtorrent:80`.

#### Remoe Porth Mapping
In Sonarr, Radarr, ecc. `/data/` of `seedbox-rtorrent` has to be mapped to `/downloads`.

### PUID and PGID
You can find these parameters:
```
$ id username
    uid=1000(user) gid=1000(user) groups=1000(user)
```

### Updating
Do not update apps, to update remove images then recreate the containers.