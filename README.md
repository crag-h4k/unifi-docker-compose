# Unifi Controller Setup

These are the steps I used to set up my Unifi controller on a Raspberry Pi 3b+ running
Debian GNU/Linux 11 (bullseye) 5.15.84-v8+ aarch64 GNU/Linux

## Installation Pre-Reqs

```sh
# if you want to pull linuxserver's docker repo in as a submodule
git submodule update --recursive --remote --init
sudo apt update
sudo apt dist-upgrade
sudo apt install ca-certificates curl gnupg lsb-release docker-ce docker-ce-cli containerd.io docker-compose-plugin
sudo systemctl enable docker
sudo systemctl start docker
```

## Container Configuration and Parameters

### Parameters

Container images are configured using parameters passed at runtime (such as those above). These parameters are separated by a colon and indicate `<external>:<internal>` respectively. For example, `-p 8080:80` would expose port `80` from inside the container to be accessible from the host's IP on port `8080` outside the container.

| Parameter | Function |
| :----: | --- |
| `-p 8443` | Unifi web admin port |
| `-p 3478/udp` | Unifi STUN port |
| `-p 10001/udp` | Required for AP discovery |
| `-p 8080` | Required for device communication |
| `-p 1900/udp` | Required for `Make controller discoverable on L2 network` option |
| `-p 8843` | Unifi guest portal HTTPS redirect port |
| `-p 8880` | Unifi guest portal HTTP redirect port |
| `-p 6789` | For mobile throughput test |
| `-p 5514/udp` | Remote syslog port |
| `-e PUID=1000` | for UserID - see below for explanation |
| `-e PGID=1000` | for GroupID - see below for explanation |
| `-e TZ=Europe/London` | Specify a timezone to use (e.g. Europe/London) - [see list](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones) |
| `-e MEM_LIMIT=1024` | Optionally change the Java memory limit (in Megabytes). Set to `default` to reset to default |
| `-e MEM_STARTUP=1024` | Optionally change the Java initial/minimum memory (in Megabytes). Set to `default` to reset to default |
| `-v /config` | All Unifi data stored here |


### Configuration

Checkout the [docker-compose.yaml](`./docker-compose.yaml`) to make sure all of the container's mappings look OK.

*Notes on my settings*:

* container`8443` mapped to host port `443`.
* all "optional" ports are disabled
* container directory `config/log/` is mapped to host directory`logs/` - [.gitignore](./.gitignore) is setup to ignore the contents of `config/` and `logs/`, apart from a placeholder file `[.gitkeep](./config/.gitkeep)`
* timezone set to `America/Denver`


## Go Container Go!

After configuring the `[docker-compose.yaml](./docker-compose.yaml)` file with the appropriate mappings. Run the following command:
```sh
docker compose up -d
```

## More info?

See [docker-unifi-controller/README.md](linuxserver/docker-unifi-controller docs) for more details on the container itself.

