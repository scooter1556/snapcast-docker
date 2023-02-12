# Snapcast Client Docker Image

Snapcast Client Docker image with support for ALSA. Avahi and Pulse Audio are not currently supported.

## Docker
    docker run -d --name snapclient --device /dev/snd scootsoftware/snapclient -h {SERVER_IP} -s {ALSA_DEVICE}

## Docker Compose

    version: '3'
    services:
      snapclient:
        container_name: snapclient
        image: scootsoftware/snapclient
        restart: unless-stopped
        devices:
          - /dev/snd:/dev/snd
        command: -h {SERVER_IP} -s {ALSA_DEVICE}
          
> For additional documentation see here: [https://github.com/badaix/snapcast#client](https://github.com/badaix/snapcast#client)
