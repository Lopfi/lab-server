# Docker

```
sudo apt-get update && sudo apt-get upgrade -y
sudo apt-get install \
 ca-certificates \
 curl \
 gnupg \
 lsb-release
sudo mkdir -m 0755 -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
echo \
 "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
 $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
sudo apt-get install gh nano

sudo groupadd docker
sudo usermod -aG docker $USER
newgrp docker
```

## calibre

Add this under path to calibre in settings
`/usr/bin/ebook-convert`

## prometheus

copy the prometheus.yml to /etc/prometheus/prometheus.yml


# TODOs

- [ ] Add labels for autoupdates through dockhand
- [ ] Add docker caddy for proxy through labels
- [ ] Add healthchecks for all containers
- [ ] check restart policies for all containers
- [ ] Add monitoring for all containers through prometheus and caddy exporter
    - https://github.com/Checkmk/checkmk
- [ ] Add donetick config to git without the token
- [ ] Setup cloudflare tunnels again
- [ ] switch to different dashboard
- [ ] add authentic for all supported applications
- [ ] https://github.com/ChrispyBacon-dev/DockFlare
- [ ] https://pegaprox.com/
https://github.com/IT-BAER/proxmorph?tab=readme-ov-file

## Services to add

- [ ] uptime-kuma
- [ ] patchmon
- [ ] rustdesk
- [ ] rackpeek
- [ ] termix ssh
- [ ] SparkyFitness
- [ ] Calibre-web automated
- [ ] Kurrier
- [ ] dashy
- [ ] zabbix

## Arr
- [ ] MyDia