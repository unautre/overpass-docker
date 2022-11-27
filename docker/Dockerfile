FROM debian:bullseye AS builder

ARG OSM3S_VERSION=0.7.59

RUN apt-get update && apt-get install -y \
  wget g++ make expat libexpat1-dev zlib1g-dev liblz4-dev \
  && rm -rf /var/lib/apt/lists/*

ARG EXEC_DIR=/opt/osm3s/

RUN wget http://dev.overpass-api.de/releases/osm-3s_v${OSM3S_VERSION}.tar.gz && \
	tar -zxvf osm-3s_v${OSM3S_VERSION}.tar.gz && \
  rm -rf osm-3s_v${OSM3S_VERSION}.tar.gz && \
	cd osm-3s_v${OSM3S_VERSION} && \
  ./configure CXXFLAGS="-O2" --enable-lz4 --prefix=${EXEC_DIR} && \
  make install

FROM nginxinc/nginx-unprivileged:stable

USER root

RUN apt-get update && apt-get install -y \
  spawn-fcgi fcgiwrap \
  && rm -rf /var/lib/apt/lists/*

USER nginx

COPY --from=builder /opt/osm3s/ /opt/osm3s/

COPY 40-start-fcgi-wrapper.sh /docker-entrypoint.d/
COPY nginx-overpass.conf /etc/nginx/conf.d/