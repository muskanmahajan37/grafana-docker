FROM marketplace.gcr.io/google/debian9
ENV GRAFANA_VERSION 6.5.3
ENV GRAFANA_SHA256 02fe78f1d6842e043ca61a64272dd7dfdb4103486f163501b08a923f0f0452be

ENV C2D_RELEASE 6.5.3

ARG GRAFANA_URL="https://dl.grafana.com/oss/release/grafana-$GRAFANA_VERSION.linux-amd64.tar.gz"
ARG GF_UID="472"
ARG GF_GID="472"

ENV PATH=/usr/share/grafana/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin \
    GF_PATHS_CONFIG="/etc/grafana/grafana.ini" \
    GF_PATHS_DATA="/var/lib/grafana" \
    GF_PATHS_HOME="/usr/share/grafana" \
    GF_PATHS_LOGS="/var/log/grafana" \
    GF_PATHS_PLUGINS="/var/lib/grafana/plugins" \
    GF_PATHS_PROVISIONING="/etc/grafana/provisioning"

RUN set -x && \
    buildDependencies=' \
      ca-certificates \
      libfontconfig \
      tar \
      wget \
    ' && \
    apt-get update && apt-get install -qq -y $buildDependencies && \
    mkdir -p "$GF_PATHS_HOME/.aws" && \
    wget -O grafana.tar.gz "$GRAFANA_URL" && \
    echo "$GRAFANA_SHA256 grafana.tar.gz" | sha256sum -c - && \
    tar xfvz grafana.tar.gz --strip-components=1 -C "$GF_PATHS_HOME" && \
    apt-get autoremove -y && \
    rm -rf /var/lib/apt/lists/* && \
    groupadd -r -g $GF_GID grafana && \
    useradd -r -u $GF_UID -g grafana grafana && \
    mkdir -p "$GF_PATHS_PROVISIONING/datasources" \
             "$GF_PATHS_PROVISIONING/dashboards" \
             "$GF_PATHS_LOGS" \
             "$GF_PATHS_PLUGINS" \
             "$GF_PATHS_DATA" && \
    cp "$GF_PATHS_HOME/conf/sample.ini" "$GF_PATHS_CONFIG" && \
    cp "$GF_PATHS_HOME/conf/ldap.toml" /etc/grafana/ldap.toml && \
    chown -R grafana:grafana "$GF_PATHS_DATA" "$GF_PATHS_HOME/.aws" "$GF_PATHS_LOGS" "$GF_PATHS_PLUGINS" && \
    chmod 777 "$GF_PATHS_DATA" "$GF_PATHS_HOME/.aws" "$GF_PATHS_LOGS" "$GF_PATHS_PLUGINS"

EXPOSE 3000

COPY ./docker-entrypoint.sh /docker-entrypoint.sh
RUN chmod 777 /docker-entrypoint.sh

USER grafana
WORKDIR /
ENTRYPOINT [ "/docker-entrypoint.sh" ]
