FROM docker.io/bitnami/minideb:bullseye

ARG TARGETARCH

LABEL org.opencontainers.image.base.name="docker.io/bitnami/minideb:bullseye" \
      org.opencontainers.image.created="2023-04-16T23:03:28Z" \
      org.opencontainers.image.description="Application packaged by VMware, Inc" \
      org.opencontainers.image.licenses="Apache-2.0" \
      org.opencontainers.image.ref.name="4.4.2-debian-11-r24" \
      org.opencontainers.image.title="pgpool" \
      org.opencontainers.image.vendor="VMware, Inc." \
      org.opencontainers.image.version="4.4.2"

ENV HOME="/" \
    OS_ARCH="${TARGETARCH:-amd64}" \
    OS_FLAVOUR="debian-11" \
    OS_NAME="linux"

COPY prebuildfs /
SHELL ["/bin/bash", "-o", "pipefail", "-c"]
# Install required system packages and dependencies
RUN install_packages ca-certificates curl ldap-utils libaudit1 libbsd0 libcap-ng0 libcrypt1 libedit2 libffi7 libgcc-s1 libgmp10 libgnutls30 libhogweed6 libicu67 libidn2-0 libldap-2.4-2 libldap-common liblz4-1 liblzma5 libmd0 libnettle8 libp11-kit0 libpam-ldapd libpam0g libsasl2-2 libssl1.1 libstdc++6 libtasn1-6 libtinfo6 libunistring2 libuuid1 libxml2 libxslt1.1 nslcd procps zlib1g
RUN mkdir -p /tmp/bitnami/pkg/cache/ && cd /tmp/bitnami/pkg/cache/ && \
    COMPONENTS=( \
      "postgresql-client-14.7.0-4-linux-${OS_ARCH}-debian-11" \
      "wait-for-port-1.0.6-4-linux-${OS_ARCH}-debian-11" \
      "pgpool-4.4.2-5-linux-${OS_ARCH}-debian-11" \
      "gosu-1.16.0-5-linux-${OS_ARCH}-debian-11" \
    ) && \
    for COMPONENT in "${COMPONENTS[@]}"; do \
      if [ ! -f "${COMPONENT}.tar.gz" ]; then \
        curl -SsLf "https://downloads.bitnami.com/files/stacksmith/${COMPONENT}.tar.gz" -O ; \
        curl -SsLf "https://downloads.bitnami.com/files/stacksmith/${COMPONENT}.tar.gz.sha256" -O ; \
      fi && \
      sha256sum -c "${COMPONENT}.tar.gz.sha256" && \
      tar -zxf "${COMPONENT}.tar.gz" -C /opt/bitnami --strip-components=2 --no-same-owner --wildcards '*/files' && \
      rm -rf "${COMPONENT}".tar.gz{,.sha256} ; \
    done
RUN apt-get autoremove --purge -y curl && \
    apt-get update && apt-get upgrade -y && \
    apt-get clean && rm -rf /var/lib/apt/lists /var/cache/apt/archives
RUN chmod g+rwX /opt/bitnami

COPY rootfs /
RUN /opt/bitnami/scripts/pgpool/postunpack.sh
ENV APP_VERSION="4.4.2" \
    BITNAMI_APP_NAME="pgpool" \
    LD_LIBRARY_PATH="/opt/bitnami/common/lib:/opt/bitnami/common/lib64:$LD_LIBRARY_PATH" \
    PATH="/opt/bitnami/postgresql/bin:/opt/bitnami/common/bin:/opt/bitnami/common/sbin:/opt/bitnami/pgpool/bin:$PATH"

EXPOSE 5432

USER 1001
ENTRYPOINT [ "/opt/bitnami/scripts/pgpool/entrypoint.sh" ]
CMD [ "/opt/bitnami/scripts/pgpool/run.sh" ]
