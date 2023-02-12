ARG ARCH= 
FROM ${ARCH}ubuntu:22.04 as build

ARG SNAPCAST_VERSION="0.27.0"

ENV DEBIAN_FRONTEND=noninteractive

RUN apt update
RUN apt install -y --no-install-recommends build-essential ca-certificates cmake git libasound2-dev libboost-dev libvorbisidec-dev libvorbis-dev libopus-dev libflac-dev libsoxr-dev libavahi-client-dev
RUN rm -rf /var/lib/apt/lists/*

# Snapclient
RUN git clone https://github.com/badaix/snapcast.git -b v${SNAPCAST_VERSION}
WORKDIR /snapcast
RUN mkdir build && cd build && cmake -DBUILD_SERVER=OFF -DBUILD_WITH_PULSE=OFF -DBUILD_WITH_AVAHI=OFF .. && make -j$(nproc)

ARG ARCH= 
FROM ${ARCH}ubuntu:22.04

COPY --from=build /snapcast/bin/snapclient /usr/local/bin/snapclient

ENV DEBIAN_FRONTEND=noninteractive

RUN apt update && apt install --no-install-recommends -y libavahi-client3 libsoxr0 libvorbisidec1 libflac8 libopus0 libasound2 && rm -rf /var/lib/apt/lists/*

ENTRYPOINT ["snapclient"]
