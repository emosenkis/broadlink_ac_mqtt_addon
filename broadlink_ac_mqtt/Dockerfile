ARG BUILD_FROM=ghcr.io/hassio-addons/base-python/armhf:15.0.6
# hadolint ignore=DL3006
FROM ${BUILD_FROM}

# set version label
ARG BUILD_DATE
ARG VERSION
ARG AC2MQTT_VERSION
LABEL build_version="Version:- ${VERSION} Build-date:- ${BUILD_DATE}"
LABEL maintainer="emosenkis"

# Set shell
SHELL ["/bin/bash", "-o", "pipefail", "-c"]

RUN \
 echo "**** install packages ****" && \
 apk add --no-cache --virtual=build-dependencies \
    g++ \
    gcc \
    make \
    python3-dev \
    py3-pip \
    && \
 apk add --no-cache \
        python3 \
        py3-paho-mqtt \
        py3-yaml \
        && \
 echo "**** Install app ****" && \
 mkdir -p /app/ac2mqtt && \
 echo "Created App folder in /app/ac2mqtt" && \
 AC2MQTT_COMMIT=$(curl -sX GET "https://api.github.com/repos/liaan/broadlink_ac_mqtt/branches/master" \
 | jq -r '.commit.sha'); \
 echo "Latest commit is ${AC2MQTT_COMMIT}" && \
 echo "Downloading commit ${AC2MQTT_COMMIT}" && \
 curl -o \
 /tmp/ac2mqtt.tar.gz -L \
    "https://github.com/liaan/broadlink_ac_mqtt/archive/${AC2MQTT_COMMIT}.tar.gz" && \
 echo "Downloaded successfully, extracting to /app/ac2mqtt" && \
 tar xf \
 /tmp/ac2mqtt.tar.gz -C \
    /app/ac2mqtt --strip-components=1 && \
 echo "Extracted successfully" && \
 echo "**** install pip packages ****" && \
 pip3 install -r /app/ac2mqtt/requirements.txt --break-system-packages && \
 echo "**** Hard Coding versioning ****" && \
 echo "None" > /app/ac2mqtt/version.txt && \
 echo ${AC2MQTT_COMMIT} > /app/ac2mqtt/version.txt && \
 echo "**** cleanup ****" && \
 apk del --no-cache --purge build-dependencies && \
 rm -rf \
    /root/.cache \
    /tmp/*

# copy local files
COPY --chmod=755 root/ /
