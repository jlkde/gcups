# SPDX-FileCopyrightText: 2024-2025 Damian Fajfer <damian@fajfer.org>
#
# SPDX-License-Identifier: GPL-3.0-or-later

FROM docker.io/ubuntu:24.04

ARG DEBIAN_FRONTEND=noninteractive

RUN set -eux; \
    apt-get update; \
    apt-get install --no-install-recommends -y ca-certificates wget gpg; \
    wget -qO /tmp/csgsa-keyring.gpg https://gcups-static.greencell.global/csgsa-keyring.gpg; \
    gpg --dearmor -o /usr/share/keyrings/csgsa-keyring.gpg /tmp/csgsa-keyring.gpg; \
    echo "deb [arch=amd64 signed-by=/usr/share/keyrings/csgsa-keyring.gpg] https://gcups-static.greencell.global/deb stable non-free" > /etc/apt/sources.list.d/gcups.list; \
    apt-get update; \
    apt-get install --no-install-recommends -y dbus dbus-user-session gcups libasound2t64 libgbm1 python3-plyvel xvfb; \
    apt-get purge --auto-remove -y wget gpg; \
    rm -rf /var/lib/apt/lists/*; \
    mkdir -m777 -p /opt/gcups/db && \
    mkdir -m777 -p /opt/gcups/log && \
    mkdir -m775 -p /opt/gcups/db/gcups-rxdb-1-settings && \
    rm -rf /opt/gcups/license /opt/gcups/manuals && rm -rf /tmp/* && \
    useradd -m gcups && chown -R gcups:gcups /opt/gcups && \
    mkdir -p /var/run/dbus && chown -R gcups:gcups /var/run/dbus

COPY --chown=gcups:gcups init.sh populate-db.py db.txt /opt/
WORKDIR /opt
RUN python3 populate-db.py

ENV GCUPS_HTTP_PORT=8080
ENV GCUPS_PASSWORD=gcups123
EXPOSE $GCUPS_HTTP_PORT

USER gcups
ENTRYPOINT ["/opt/init.sh"]
