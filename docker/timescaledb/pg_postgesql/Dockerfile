ARG PG_VERSION_TAG
FROM timescale/timescaledb:1.2.0-${PG_VERSION_TAG}
#FROM timescale/timescaledb:1.2.0-pg10-bitnami

MAINTAINER Timescale https://www.timescale.com

COPY pg_prometheus.control Makefile /build/pg_prometheus/
COPY src/*.c src/*.h /build/pg_prometheus/src/
COPY sql/prometheus.sql /build/pg_prometheus/sql/

COPY docker-entrypoint-initdb.d/* /docker-entrypoint-initdb.d/

USER 0
RUN set -ex \
    && mkdir -p /var/lib/apt/lists/partial \
    && apt-get update \
    && apt-get -y install \
            build-essential \
            libssl-dev \
            git \
            dpkg-dev \
            gcc \
            libc-dev \
            make \
            cmake \
            wget \
    && mkdir -p /build/ \
    && make -C /build/pg_prometheus install \
    && cd ~ \
    && apt-get autoremove --purge -y \
            build-essential \
            libssl-dev \
            git \
            dpkg-dev \
            gcc \
            libc-dev \
            make \
            cmake \
            wget \
    && apt-get clean -y \
    && rm -rf \
      "${HOME}/.cache" \
        /var/lib/apt/lists/* \
        /tmp/*               \
        /var/tmp/* \
    && sed -r -i "s/[#]*\s*(shared_preload_libraries)\s*=\s*'(.*)'/\1 = 'pg_prometheus,\2'/;s/,'/'/" /opt/bitnami/postgresql/share/postgresql.conf.sample

USER 1001

