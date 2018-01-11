FROM gocd/gocd-agent-alpine-3.5:v17.12.0

MAINTAINER jshen <jshen@thoughtworks.com>

WORKDIR /opt

RUN apk update && apk add docker

RUN curl -O -L https://github.com/rancher/rancher-compose/releases/download/v0.12.5/rancher-compose-linux-amd64-v0.12.5.tar.gz \
       && tar -xvf rancher-compose-linux-amd64-v0.12.5.tar.gz \
       && mv rancher-compose-v0.12.5/rancher-compose /usr/local/bin/