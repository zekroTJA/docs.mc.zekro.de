# Setup

The minecraft server as well as other sattelite services like Prometheus and Grafana are set up using [Docker](https://www.docker.com/) and [Docker Compose](https://docs.docker.com/compose/).

## Docker Image

The Docker image for the Minecraft server consists of a JDK environment and several utilities inside the image. It is based on the [minebase](https://github.com/zekroTJA/minebase) image. This image is a framework foundation to build specific Minecraft servers on top of it. It contains tools like

- [rconcli](https://github.com/zekroTJA/rconcli) to access the server console directly from inside the container via RCON,
  - [healthcheck](https://github.com/evolvedpacks/healthcheck) to be able monitor the service health of the Minecraft server via Docker,
- a full backup system for your worlds based on [rcon](https://rclone.org/),
- as well as a framework of scripts to manage lifetime routines and startup of the server

For the Minecraft server, we are using [PaperMC](https://papermc.io/). [Here](https://github.com/zekroTJA/papermc-docker) you can find the repository of the PaperMC Docker image.

## Docker Compose Stack

To orchestrate the Minecraft server and related services, we are using [Docker Compose](https://docs.docker.com/compose/), which allows defining configurations for Docker containers, volumes and networks with a single or multiple YAML files.

The stack comprises the following services:

- the main PaperMC Minecraft server
- a second "staging" instance of PaperMC used to test plugin compatibility for new versions
- Prometheus as backend and database for monitoring
- Grafana as frontend for monitoring
- Portainer to have an easy web interface to manage and interact with containers on the server
- Caddy which serves a fallback website to [mc.zekro.de](https://mc.zekro.de) when the server is down for restart or maintenance

Below, you can see the Docker Compose definition for the stack. This can be outdated though, [here](https://git.zekro.de/zekro/minecraft-server-configs/src/branch/main/docker-compose.yml) you can find the file from the repository which is synced with the server, so it will be up to date.

As you can see, I am using [traefik proxy](https://doc.traefik.io/traefik/) as HTTP proxy, which is managed in another Docker Compose stack. Thats why the external `core_public` network is used to make the web services in this stack available to the internet.

```yaml
networks:
  core_public:
    external: true
  minecraft:

volumes:
  mysql-data:

secrets:
  minecraftrclone:
    file: rclone.conf
  netrc:
    file: .netrc

services:
  spigot:
    image: "ghcr.io/zekrotja/papermc-docker:zulu-23"
    restart: unless-stopped
    environment:
      VERSION: "1.21.10"
      BUILD: "latest"
      CACHE_DOWNLOAD: "true"
      XMS: 4G
      XMX: 8G
      MAX_AGE_BACKUP_FILES: 12d
      PRE_START_BACKUP: "true"
      POST_START_BACKUP: "false"
      BACKUP_SUCCESS_SCRIPT: 'curl -X PUT --netrc-file /run/secrets/.netrc -H "Title: ✅ Minecraft Backup Successfull" "https://ntfy.zekro.de/minecraft_backups"'
      BACKUP_FAILED_SCRIPT: 'curl -X PUT --netrc-file /run/secrets/.netrc -H "Title: ❌ Minecraft Backup Failed" -d "$$MESSAGE" "https://ntfy.zekro.de/minecraft_backups"'
      DEBUG_MODE: "true"
    ports:
      - "24693:25565"
      - "25575:25575" # rcon
      - "24454:24454/udp" # voice chat
    networks:
      - "core_public"
      - "minecraft"
    volumes:
      - "./spigot/config:/etc/mcserver/config"
      - "./spigot/plugins:/etc/mcserver/plugins"
      - "./spigot/worlds:/etc/mcserver/worlds"
      - "./spigot/locals:/etc/mcserver/locals"
    secrets:
      - source: minecraftrclone
        target: rcloneconfig
      - source: netrc
        target: .netrc
    healthcheck:
      test: >
        /usr/bin/healthcheck -addr localhost:25565 -validateResponse
        && test "$(curl -o /dev/null -sLw "%{response_code}\n" http://127.0.0.1:8100)" -eq 200
      start_period: 1m
      start_interval: 5s
      interval: 30s
      timeout: 10s
      retries: "10"
    labels:
      traefik.enable: "true"
      traefik.http.routers.spigot.entrypoints: "https"
      traefik.http.routers.spigot.tls: "true"
      traefik.http.routers.spigot.tls.certresolver: "le"
      traefik.http.routers.spigot.rule: "Host(`mc.zekro.de`)"
      traefik.http.routers.spigot.priority: "100"
      traefik.http.services.spigot.loadbalancer.server.port: "8100"

  caddy:
    image: "caddy:latest"
    restart: unless-stopped
    volumes:
      - "./caddy/www:/usr/share/caddy"
    networks:
      - "core_public"
      - "minecraft"
    labels:
      com.centurylinklabs.watchtower.enable: "true"
      traefik.enable: "true"
      traefik.http.routers.caddy.entrypoints: "https"
      traefik.http.routers.caddy.tls: "true"
      traefik.http.routers.caddy.tls.certresolver: "le"
      traefik.http.routers.caddy.rule: "Host(`mc.zekro.de`)"
      traefik.http.routers.caddy.priority: "10"

  spigot_staging:
    image: "ghcr.io/zekrotja/papermc-docker:zulu-23"
    restart: unless-stopped
    environment:
      VERSION: "1.21.11"
      BUILD: "latest"
      CACHE_DOWNLOAD: "true"
      DEBUG_MODE: "true"
      XMS: 2G
      XMX: 4G
      MAX_AGE_BACKUP_FILES: 90d
      BACKUP_SUCCESS_SCRIPT: 'curl -X PUT --netrc-file /run/secrets/.netrc -H "Title: ✅ STAGING Minecraft Backup Successfull" "https://ntfy.zekro.de/minecraft_backups"'
      BACKUP_FAILED_SCRIPT: 'curl -X PUT --netrc-file /run/secrets/.netrc -H "Title: ❌ STAGING Minecraft Backup Failed" -d "$$MESSAGE" "https://ntfy.zekro.de/minecraft_backups"'
    ports:
      - "24694:25565"
    networks:
      - "core_public"
      - "minecraft"
    volumes:
      - "./spigot_staging/config:/etc/mcserver/config"
      - "./spigot_staging/plugins:/etc/mcserver/plugins"
      - "./spigot_staging/worlds:/etc/mcserver/worlds"
      - "./spigot_staging/locals:/etc/mcserver/locals"
    secrets:
      - source: minecraftrclone
        target: rcloneconfig
      - source: netrc
        target: .netrc
    healthcheck:
      test: >
        /usr/bin/healthcheck -addr localhost:25565 -validateResponse
        && test "$(curl -o /dev/null -sLw "%{response_code}\n" http://127.0.0.1:8100)" -eq 200
      start_period: 1m
      start_interval: 5s
      interval: 30s
      timeout: 10s
      retries: "10"
    labels:
      com.centurylinklabs.watchtower.enable: "true"
      traefik.enable: "true"
      traefik.http.routers.spigot_staging.entrypoints: "https"
      traefik.http.routers.spigot_staging.tls: "true"
      traefik.http.routers.spigot_staging.tls.certresolver: "le"
      traefik.http.routers.spigot_staging.rule: "Host(`staging.mc.zekro.de`)"
      traefik.http.routers.spigot_staging.priority: "100"
      traefik.http.services.spigot_staging.loadbalancer.server.port: "8100"

  prometheus:
    image: "prom/prometheus:latest"
    command: "--web.enable-admin-api --config.file=/etc/prometheus/prometheus.yml"
    expose:
      - "9090"
    volumes:
      - "./prometheus/config:/etc/prometheus"
    restart: always
    networks:
      - "minecraft"
    labels:
      com.centurylinklabs.watchtower.enable: "true"

  portainer:
    image: "portainer/portainer-ce:latest"
    volumes:
      - "./portainer:/data"
      - "/var/run/docker.sock:/var/run/docker.sock"
    restart: always
    networks:
      - "core_public"
    labels:
      com.centurylinklabs.watchtower.enable: "true"
      traefik.enable: "true"
      traefik.http.routers.portainer.entrypoints: "https"
      traefik.http.routers.portainer.tls: "true"
      traefik.http.routers.portainer.tls.certresolver: "le"
      traefik.http.routers.portainer.rule: "Host(`docker.mc.zekro.de`)"
      traefik.http.services.portainer.loadbalancer.server.port: "9000"

  grafana:
    image: "grafana/grafana"
    restart: unless-stopped
    user: "root"
    environment:
      GF_SECURITY_ALLOW_EMBEDDING: "true"
    volumes:
      - "./grafana:/var/lib/grafana"
    networks:
      - "core_public"
      - "minecraft"
    labels:
      com.centurylinklabs.watchtower.enable: "true"
      traefik.enable: "true"
      traefik.http.routers.grafana.entrypoints: "https"
      traefik.http.routers.grafana.tls.certresolver: "le"
      traefik.http.routers.grafana.tls: "true"
      traefik.http.routers.grafana.rule: "Host(`grafana.mc.zekro.de`)"
```
