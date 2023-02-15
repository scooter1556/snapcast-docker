# Snapcast Server Docker Image

Snapcast Server Docker image. Avahi is currently disabled.

## Setup
It's recommended to create a server config file on the host for persistence and ease of modification

    curl https://raw.githubusercontent.com/badaix/snapcast/master/server/etc/snapserver.conf > /etc/snapserver.conf

Modify the configuration as needed: [https://github.com/badaix/snapcast#configuration](https://github.com/badaix/snapcast#configuration)

## AirPlay & Spotify

It's recommended to run shairport-sync for AirPlay and librespot for Spotify Connect in separate containers and use their pipe outputs to connect them to snapcast.

## Docker
    docker run -d --name snapserver -v /etc/snapserver.conf:/etc/snapserver.conf -v /tmp:/tmp --network host --device /dev/snd scootsoftware/snapserver

## Docker Compose

    version: '3'
    services:
      snapserver:
        container_name: snapserver
        image: scootsoftware/snapserver
        volumes:
          - /etc/snapserver.conf:/etc/snapserver.conf
          - /tmp:/tmp # Needed for FIFO pipes
        restart: unless-stopped
        network_mode: host
        devices:
          - /dev/snd:/dev/snd # Needed for ALSA
          
> **host** mode for networking can be omitted. In this instance you will need to expose ports **1704**, **1705** and **1780** along with any additional ports for additional TCP streams (e.g. 4900)
