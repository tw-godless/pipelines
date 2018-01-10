# Pipelines

这个Repo用来指导我们为Order service, User service和Goods service搭建一套CI/CD系统

## Requirements

* docker
* nexus

## Steps
### Setup nexus
* start nexus
```
docker run -d -v $(pwd)/nexusdata/nexusdata -p 5000:5000 -p 8081:8081 sonatype/nexus3
```
* initialize nexus

* login nexus
```docker login 127.0.0.1:5000 -u admin```

### Setup gocd server
```
docker run -d -v $(pwd)/goserver:/godata -v $HOME:/home/go -p8153:8153 -p8154:8154 gocd/gocd-server:v17.12.0
```

### Setup gocd agent

```
chmod 666 /var/run/docker.sock
docker run -d -e GO_SERVER_URL=https://172.17.0.1:8154/go -v $(pwd)/goagent:/godata -v $HOME:/home/go gocd/gocd-agent-alpine-3.5:v17.12.0
```

Auto register agent

```
docker build -t gocd/gocd-agent-alpine-3.5:v17.12.0-docker dockerfiles/
chmod 666 /var/run/docker.sock
docker run -d -e WORKDIR=$(pwd)/goagent -e GO_SERVER_URL=https://172.17.0.1:8154/go -v $(pwd)/goagent:/godata -v $HOME:/home/go -v /var/run/docker.sock:/var/run/docker.sock:rw -v $HOME/.docker:/home/go/.docker:rw -e AGENT_AUTO_REGISTER_KEY=067c1411-e87c-485d-a70b-2abde3c599f2 -e AGENT_AUTO_REGISTER_RESOURCES=docker -e AGENT_AUTO_REGISTER_HOSTNAME=superman gocd/gocd-agent-alpine-3.5:v17.12.0-docker
```

* Create automation scripts for user service
scripts/test.sh
```
#! /usr/bin/env bash
set -x
set -e

docker run --rm -v /tmp/gradle-caches:/root/.gradle/caches -v $WORKDIR/pipelines/$GO_PIPELINE_NAME:/opt/app -w /opt/app gradle:4.4.1-jdk8 gradle clean test
```
scripts/build.sh
```
#! /usr/bin/env bash
set -x
set -e

docker run --rm -v /tmp/gradle-caches:/root/.gradle/caches -v $WORKDIR/pipelines/$GO_PIPELINE_NAME:/opt/app -w /opt/app gradle:4.4.1-jdk8 gradle clean bootRepackage
if [[ -z $DOCKER_REGISRTY ]]; then
  DOCKER_REGISRTY=127.0.0.1:5000
fi
IMAGE_NAME=${DOCKER_REGISRTY}/tw-ms-train/user-service:${GO_PIPELINE_COUNTER}
docker build -t $IMAGE_NAME .
docker push $IMAGE_NAME
docker rmi $IMAGE_NAME
```
scripts/deploy.sh
```
#! /usr/bin/env bash
set -x
set -e

if [[ -z $DOCKER_REGISRTY ]]; then
  DOCKER_REGISRTY=127.0.0.1:5000
fi
IMAGE_NAME=${DOCKER_REGISRTY}/tw-ms-train/user-service:${GO_PIPELINE_COUNTER}

docker run -d -p 8080:8080 $IMAGE_NAME
```


* Create pipeline for user service manually



* Create pipelines for user service with code 
```
<config-repos>
  <config-repo pluginId="yaml.config.plugin" id="repo1">
    <git url="https://github.com/tomzo/gocd-yaml-config-example.git" />
  </config-repo>
</config-repos>
```

* Create pipelines for other services