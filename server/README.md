# Snapcast Server Docker Image

Snapcast Server Docker image. Avahi is currently disabled.

## Setup
It's recommended to create a server config file on the host and a data directory for persistence and ease of modification

    mkdir /etc/snapserver
    curl https://raw.githubusercontent.com/badaix/snapcast/master/server/etc/snapserver.conf > /etc/snapserver/snapserver.conf

Modify the configuration as needed: [https://github.com/badaix/snapcast#configuration](https://github.com/badaix/snapcast#configuration)

## Docker
    docker run -d --name snapserver -v /etc/snapserver/snapserver.conf:/etc/snapserver.conf -v /etc/snapserver:/root/.config/snapserver -v /tmp:/tmp --network host --device /dev/snd scootsoftware/snapserver

## Docker Compose

    version: '3'
    services:
      snapserver:
        container_name: snapserver
        image: scootsoftware/snapserver
        volumes:
          - /etc/snapserver/snapserver.conf:/etc/snapserver.conf
          - /etc/snapserver:/root/.config/snapserver 
          - /tmp:/tmp # Needed for FIFO pipes
        restart: unless-stopped
        network_mode: host
        devices:
          - /dev/snd:/dev/snd # Needed for ALSA
          
> **host** mode for networking can be omitted to improve security. In this instance you will need to expose ports **1704**, **1705** and **1780** along with any additional ports for additional TCP streams (e.g. 4900)

## AirPlay & Spotify

It's recommended to run shairport-sync for AirPlay and librespot for Spotify in separate containers and use their pipe outputs to connect them to snapcast.

### AirPlay

Below is an example docker compose service entry for shairport-sync

        airplay:
            image: mikebrady/shairport-sync:latest
            network_mode: host
            restart: unless-stopped
            volumes:
              - /tmp:/tmp
            command: -o pipe

Add a **source** entry to your snapserver config similar to this

    source = pipe:///tmp/shairport-sync-audio?name=AirPlay

### Spotify

For Spotify use one of the many images available on Docker Hub such as [this](https://hub.docker.com/r/giof71/librespot). Make sure librespot is run with  **--backend pipe** and **--device /tmp/spotifyfifo** so that audio output can be piped to your snapserver instance.

Add a **source** entry to your snapserver config similar to this

    source = pipe:///tmp/spotifyfifo?name=Spotify

> If you only care about Spotify support with Snapcast there are alternative images available which make this simpler such as [this](https://hub.docker.com/r/bunchofbricks/librespot-snapcast)

### Additional Notes

The audio output of both shairport-sync for AirPlay and librespot for Spotify is in the format 44100:16:2 which means your snapserver config needs to be updated accordingly. Add or update the following line in your snapserver config

    sampleformat = 44100:16:2
