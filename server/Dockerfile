ARG ARCH= 
FROM ${ARCH}ubuntu:22.04 as build

ARG SNAPCAST_VERSION="0.27.0"

ENV DEBIAN_FRONTEND=noninteractive

RUN apt update
RUN apt install -y --no-install-recommends build-essential ca-certificates cmake git libasound2-dev libboost-dev libexpat1-dev libvorbisidec-dev libvorbis-dev libopus-dev libflac-dev libsoxr-dev libssl-dev pkg-config
RUN rm -rf /var/lib/apt/lists/*

# Snapserver
RUN git clone https://github.com/badaix/snapcast.git -b v${SNAPCAST_VERSION}
WORKDIR /snapcast
RUN mkdir build && cd build && cmake -DBUILD_CLIENT=OFF -DBUILD_WITH_PULSE=OFF -DBUILD_WITH_AVAHI=OFF .. && make -j$(nproc)

ARG ARCH= 
FROM ${ARCH}ubuntu:22.04

COPY --from=build /snapcast/bin/snapserver /usr/local/bin/snapserver
COPY --from=build /snapcast/server/etc/snapserver.conf /etc/snapserver.conf
COPY --from=build /snapcast/server/etc/index.html /usr/share/snapserver/index.html
COPY --from=build /snapcast/server/etc/snapweb /usr/share/snapserver/snapweb/

ENV DEBIAN_FRONTEND=noninteractive

RUN apt update && apt install --no-install-recommends -y libsoxr0 libvorbisenc2 libflac8 libopus0 libasound2 libexpat1 && rm -rf /var/lib/apt/lists/*

ENTRYPOINT ["snapserver"]
