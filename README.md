# Pipelines

这个Repo用来指导我们为Order service, User service和Goods service搭建一套CI/CD系统

### Requirements

docker

### Steps
* Setup gocd server
```
docker run -d -v $(pwd)/goserver:/godata -v $HOME:/home/go -p8153:8153 -p8154:8154 gocd/gocd-server:v17.12.0
```

* Setup gocd agent
```
chmod 666 /var/run/docker.sock
docker run -d -e GO_SERVER_URL=https://172.17.0.1:8154/go -v $(pwd)/goagent:/godata -v $HOME:/home/go gocd/gocd-agent-alpine-3.5:v17.12.0
```

Auto register agent
```
docker build -t gocd/gocd-agent-alpine-3.5:v17.12.0-docker dockerfiles/
chmod 666 /var/run/docker.sock
docker run -d -e WORKDIR=$(pwd)/goagent -e GO_SERVER_URL=https://172.17.0.1:8154/go -v $(pwd)/goagent:/godata -v $HOME:/home/go -v /var/run/docker.sock:/var/run/docker.sock:rw -v $HOME/.docker:/home/go/.docker:rw -e AGENT_AUTO_REGISTER_KEY=067c1411-e87c-485d-a70b-2abde3c599f2 -e AGENT_AUTO_REGISTER_RESOURCES=docker -e AGENT_AUTO_REGISTER_HOSTNAME=superman 127.0.0.1:5000/gocd/gocd-agent-alpine-3.5:v17.12.0
```

* Create automation scripts for user service

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