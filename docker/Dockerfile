# Yandex.Tank
#
# VERSION 0.0.3

FROM ubuntu:bionic
MAINTAINER Alexey Lavrenuke <direvius@yandex-team.ru>
# Version for desription
ARG VERSION
# You may specify tag instead of branch to build for specific tag
ARG BRANCH=master

LABEL Description="Fresh Yandex.Tank from github master branch with phantom" \
    Vendor="Yandex" \
    maintainer="direvius@yandex-team.ru" \
    YandexTank.version="${VERSION}" \
    Telegraf.version="${TELEGRAF_VERSION}"

RUN export DEBIAN_FRONTEND=noninteractive && \
    apt-get update -q && \
    apt-get install --no-install-recommends -yq \
        sudo     \
        vim      \
        wget     \
        curl     \
        less     \
        iproute2 \
        software-properties-common \
        telnet   \
        atop     \
        openssh-client \
        git            \
        python3-pip  && \
    add-apt-repository ppa:yandex-load/main -y && \
    apt-get update -q && \
    apt-get install -yq \
        phantom \
        phantom-ssl && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* /var/cache/apt/archives/* /tmp/* /var/tmp/*

ENV TELEGRAF_VERSION=1.19.1-1
RUN apt-get update && \
    apt-get install -y dirmngr && \
    curl -sL https://repos.influxdata.com/influxdb.key | gpg --import && \
    wget https://dl.influxdata.com/telegraf/releases/telegraf_${TELEGRAF_VERSION}_amd64.deb.asc && \
    wget https://dl.influxdata.com/telegraf/releases/telegraf_${TELEGRAF_VERSION}_amd64.deb && \
    gpg --batch --verify telegraf_${TELEGRAF_VERSION}_amd64.deb.asc telegraf_${TELEGRAF_VERSION}_amd64.deb && \
    dpkg -i telegraf_${TELEGRAF_VERSION}_amd64.deb && \
    rm -f telegraf_${TELEGRAF_VERSION}_amd64.deb*

COPY . /tmp/yatank

ENV BUILD_DEPS="python3-dev build-essential gfortran libssl-dev libffi-dev"
RUN export DEBIAN_FRONTEND=noninteractive && \
    apt-get update && \
    apt-get install -yq --no-install-recommends ${BUILD_DEPS} && \
    pip3 install --upgrade setuptools && \
    pip3 install --upgrade pip==20.3.4 && \
    pip3 install /tmp/yatank && \
    apt-get autoremove -y ${BUILD_DEPS} && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* /var/cache/apt/archives/* /tmp/* /var/tmp/* /root/.cache/*

COPY docker/files/bashrc /root/.bashrc
COPY docker/files/inputrc /root/.inputrc

VOLUME ["/var/loadtest"]
WORKDIR /var/loadtest
ENTRYPOINT ["/usr/local/bin/yandex-tank"]
