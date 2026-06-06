# Get the latest Homebridge-Docker image
FROM homebridge/homebridge:latest

# Relocate homebridge working directory so it can be persisted
#RUN find /etc/s6-overlay/ -type f | xargs sed -i -E 's:([ "])/homebridge:\1/config:g'

# Global LSIO modifications
COPY ha_lsio.sh /ha_lsio.sh
ARG CONFIGLOCATION="/config/addons_config/Homebridge"
RUN chmod 744 /ha_lsio.sh && if grep -qr "lsio" /etc; then /ha_lsio.sh "$CONFIGLOCATION"; fi && rm /ha_lsio.sh

# Changes config directory
ENV XDG_CONFIG_HOME="/config/addons_config"

# Global LSIO modifications
COPY ha_lsio.sh /ha_lsio.sh
ARG CONFIGLOCATION="/config/addons_config/Homebridge"
RUN chmod 744 /ha_lsio.sh && if grep -qr "lsio" /etc; then /ha_lsio.sh "$CONFIGLOCATION"; fi && rm /ha_lsio.sh

# Changes config directory
ENV \
    HOME="/config/addons_config"
    DATA="/config/addons_config"

# add bashio
ADD https://github.com/hassio-addons/bashio/archive/refs/tags/v0.16.3.tar.gz /tmp/bashio.tar.gz
RUN apt-get update \
    && apt-get install --no-install-recommends -y curl jq \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* \
    && mkdir /tmp/bashio \
    && tar zxvf \
        /tmp/bashio.tar.gz \
        --strip 1 -C /tmp/bashio \
    \
    && mv /tmp/bashio/lib /usr/lib/bashio \
    && ln -s /usr/lib/bashio/bashio /usr/bin/bashio

# Uses /bin for compatibility purposes
# hadolint ignore=DL4005
RUN if [ ! -f /bin/sh ] && [ -f /usr/bin/sh ]; then ln -s /usr/bin/sh /bin/sh; fi && \
    if [ ! -f /bin/bash ] && [ -f /usr/bin/bash ]; then ln -s /usr/bin/bash /bin/bash; fi

# Add the nice banner
ADD https://raw.githubusercontent.com/hassio-addons/addon-debian-base/v5.1.0/base/rootfs/etc/cont-init.d/00-banner.sh /etc/s6-overlay/s6-rc.d/banner/
RUN mkdir -p /etc/s6-overlay/s6-rc.d/banner && \
    echo "oneshot" > /etc/s6-overlay/s6-rc.d/banner/type && \
    echo "/etc/s6-overlay/s6-rc.d/banner/script" > /etc/s6-overlay/s6-rc.d/banner/up && \
    touch /etc/s6-overlay/s6-rc.d/user/contents.d/banner && \
    mv /etc/s6-overlay/s6-rc.d/banner/00-banner.sh /etc/s6-overlay/s6-rc.d/banner/script && \
    chmod +x /etc/s6-overlay/s6-rc.d/banner/script && \
    sed -i '1 s/^.*$/#!\/command\/with-contenv bashio/' /etc/s6-overlay/s6-rc.d/banner/script
