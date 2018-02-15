---
title: "PiHole"
date: 2018-02-15T21:06:11+01:00
draft: true
tags: ["rpi", "docker", "pihole"]
---

## Install

* [DockerHub](https://hub.docker.com/r/diginc/pi-hole/)
* Required for RasPi: [Multiarch-Builds](https://hub.docker.com/r/diginc/pi-hole-multiarch/tags/)

Basic configuration (see also [GitHub repo](https://github.com/diginc/docker-pi-hole/blob/master/docker_run.sh))
```bash
IP_LOOKUP="$(ip route get 8.8.8.8 | awk '{ print $NF; exit }')"
IPv6_LOOKUP="$(ip -6 route get 2001:4860:4860::8888 | awk '{ print $10; exit }')"
IP="${IP:-$IP_LOOKUP}"
IPv6="${IPv6:-$IPv6_LOOKUP}"
DOCKER_CONFIGS="/whereever/the/volumes/should/go/to"
```

Spin up the container:
```bash
docker run -d \
  --name pihole \
  -p 53:53/tcp -p 53:53/udp -p 80:80 \
  -v "${DOCKER_CONFIGS}/pihole/:/etc/pihole/" \
  -v "${DOCKER_CONFIGS}/dnsmasq.d/:/etc/dnsmasq.d/" \
  -e ServerIP="${IP}" \
  -e ServerIPv6="${IPv6}" \
  --restart=unless-stopped \
  diginc/pi-hole-multiarch:debian_armhf
```

## Configuration

Since the Kabel Router (CH7466CE) does not allow for any settings regarding DNS, *each client has to be configured by hand*. For Windows, this e.g. means setting the DNS server (`Properties --> Internet Protocol Version 4 --> Preferred DNS Server`) to the IP of the RasPi, e.g. `192.168.0.20`.

Admin Panel can be reached via `http://IP/admin`. The password needs to be set beforehand: `docker exec pihole pihole -a -p PASSWORD`.