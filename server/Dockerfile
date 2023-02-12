ARG ARCH= 
FROM ${ARCH}ubuntu:22.04 as build

ARG SNAPCAST_VERSION="0.27.0"
ARG SHAIRPORT_VERSION="4.1.1"
ARG LIBRESPOT_VERSION="0.4.2"

ENV DEBIAN_FRONTEND=noninteractive

RUN apt update
RUN apt install -y --no-install-recommends autoconf automake build-essential ca-certificates cargo libtool wget git libasound2-dev libboost-dev libvorbisidec-dev libvorbis-dev libopus-dev libflac-dev libsoxr-dev libconfig-dev libssl-dev libavahi-client-dev libpopt-dev libexpat1-dev libmosquitto-dev
RUN rm -rf /var/lib/apt/lists/*

# Snapserver
RUN git clone https://github.com/badaix/snapcast.git -b v${SNAPCAST_VERSION}
WORKDIR /snapcast/server
RUN make -j$(nproc)

WORKDIR /

# ALAC
RUN git clone https://github.com/mikebrady/alac
WORKDIR /alac
RUN autoreconf -i && ./configure && make -j$(nproc) && make install

WORKDIR /

# shairport-sync
RUN git clone https://github.com/mikebrady/shairport-sync.git -b ${SHAIRPORT_VERSION}
WORKDIR /shairport-sync
RUN autoreconf -fi && ./configure --with-soxr --with-avahi --with-ssl=openssl --with-metadata --with-stdout --with-mqtt-client --with-apple-alac && make -j$(nproc) && make install

WORKDIR /

# librespot
RUN git clone https://github.com/librespot-org/librespot.git -b v${LIBRESPOT_VERSION}
WORKDIR /librespot
RUN cargo build -Z unstable-options --config net.git-fetch-with-cli=true --no-default-features --release

ARG ARCH= 
FROM ${ARCH}ubuntu:22.04

COPY --from=build /snapcast/server/snapserver /usr/local/bin/snapserver
COPY --from=build /snapcast/server/etc/snapserver.conf /etc/snapserver.conf
COPY --from=build /snapcast/server/etc/index.html /usr/share/snapserver/index.html
COPY --from=build /snapcast/server/etc/snapweb /usr/share/snapserver/snapweb/
COPY --from=build /usr/local/bin/shairport-sync /usr/local/bin/shairport-sync
COPY --from=build /usr/local/lib/libalac.* /usr/local/lib/
COPY --from=build /librespot/target/release/librespot /usr/local/bin/librespot

ENV DEBIAN_FRONTEND=noninteractive

RUN apt update && apt install --no-install-recommends -y libmosquitto1 libavahi-client3 libsoxr0 libconfig9 libpopt0 libvorbisenc2 libflac8 libopus0 libasound2 && rm -rf /var/lib/apt/lists/*

ENTRYPOINT ["snapserver"]
