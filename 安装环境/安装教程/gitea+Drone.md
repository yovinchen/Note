version: "3"
networks:
  dronenet:

services:
  server:
    image: gitea/gitea:1.20.5
    container_name: gitea
    environment:
      - USER_UID=1000
      - USER_GID=1000
    restart: always
    networks:
      - dronenet
    volumes:
      - ./gitea:/data
      - /etc/timezone:/etc/timezone:ro
      - /etc/localtime:/etc/localtime:ro
    ports:
      - "2222:22"
      - "10800:3000"

  drone-server:
    image: drone/drone:latest
    container_name: drone-server
    ports:
      - "1380:80"
      - "8000:8000"
      - "9000:9000"
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - /dnmp/drone/:/var/lib/drone/:rw
    restart: always
    environment:
      - DRONE_GITEA_SERVER=http://10.211.55.12:10800
      - DRONE_DEBUG=true
      - DRONE_GIT_ALWAYS_AUTH=false
      - DRONE_GITEA_CLIENT_ID=30503c87-93d0-4678-81d0-d8ea2aef8f25
      - DRONE_GITEA_CLIENT_SECRET=gto_hdtxrgkyhpuzsggn5xeoujnub7q4hdsbafd56zl23rx2ppknxska
      - DRONE_RUNNER_CAPACITY=2
      - DRONE_SERVER_HOST=10.211.55.12
      - DRONE_SERVER_PROTO=http
      - DRONE_RPC_SECRET=9c3921e3e748aff725d2e16ef31fbc42
      - DRONE_TLS_AUTOCERT=false
      - DRONE_USER_CREATE=username:yovinchen,admin:true
      - TZ=Asia/Shanghai
    networks:
      - dronenet

  drone-agent:
    image: drone/agent:latest
    container_name: drone-agent
    command: agent
    restart: always
    depends_on:
      - drone-server
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
    environment:
      - DRONE_RPC_SERVER=http://10.211.55.12:9000
      - DRONE_RPC_SECRET=9c3921e3e748aff725d2e16ef31fbc42
      - DRONE_DEBUG=true
      - DRONE_LOGS_DEBUG=true
      - DRONE_LOGS_PRETTY=true
      - DRONE_LOGS_NOCOLOR=false
      - TZ=Asia/Shanghai
    networks:
      - dronenet



docker pull drone/drone:2
docker stop drone
docker rm drone
docker run -d --name=drone \
	--env=DRONE_GITEA_SERVER=http://10.211.55.12:10800 \
	--env=DRONE_GITEA_CLIENT_ID=52c774fd-f670-400f-9e7b-8bcd68cfa75a \
	--env=DRONE_GITEA_CLIENT_SECRET=gto_ozlilshmhxn2atjz5po2gn6tickcwijt4hleobwnqhhbh5vxovva \
	--env=DRONE_RPC_SECRET=aaf5eab277a8d9236d822025aeaa4530 \
	--env=DRONE_SERVER_HOST=10.211.55.12:1080 \
	--env=DRONE_SERVER_PROTO=http \
	--publish=1080:80 \
	--publish=4443:443 \
	--restart=always \
	--detach=true \
	drone/drone:2
docker pull drone/drone-runner-docker:1
docker stop drone-runner
docker rm drone-runner
docker run -d --name=drone-runner \
	--link drone:drone \
	-p 9030:3000 \
	--env=DRONE_RPC_PROTO=http \
	--env=DRONE_RPC_HOST=10.211.55.12:1080 \
	--env=DRONE_RPC_SECRET=aaf5eab277a8d9236d822025aeaa4530 \
	--env=DRONE_RUNNER_CAPACITY=2 \
	--env=DRONE_RUNNER_NAME=my-first-runner \
	-v /var/run/docker.sock:/var/run/docker.sock \
	drone/drone-runner-docker:1