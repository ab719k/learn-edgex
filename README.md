# edgex on Raspberry Pi

Steps to install EdgeX on Raspberry Pi

## Build EdgeX
Instructions to build EdgeX are available at EdgeXFoundry Github page [link](https://github.com/edgexfoundry/edgex-go) <br>
Prerequisites for building EdgeX, require instllation of following tools 

### Install go
Latest version  [go1.17.7.linux-armv6l.tar.gz](https://go.dev/dl/go1.17.7.linux-armv6l.tar.gz) from the [downloads Page](https://go.dev/dl/). Refer to [go install information](https://go.dev/doc/install). The current targeted version of the Go language runtime for release artifacts is v1.17.x
The minimum supported version of the Go language runtime is v1.17.x

### ZeroMQ
Several EdgeX Foundry services depend on ZeroMQ for communications by default. The easiest way to get and install ZeroMQ on Linux is to use this setup script. 
  Another option to build from source
```  
sudo apt-get update && \
sudo apt-get install -y libtool pkg-config build-essential autoconf automake uuid-dev cmake
git clone https://github.com/zeromq/libzmq.git
cd libzmq/
git checkout v4.3.4
mkdir _build && cd _build/
cmake ../.
make
sudo make install
ldconfig -p | grep zmq
```
another shortcut to install zeromq is to run the apt-get install command

```
apt-get install libzmq3-dev

```

### Install Redis
```
wget http://download.redis.io/redis-stable.tar.gz
tar xvzf redis-stable.tar.gz
cd redis-stable
make
```
  
### Install Consul
```
apt-get install consul
```

  
### Install docker
```
curl -fsSL https://get.docker.com -o get-docker.sh
DRY_RUN=1 sh ./get-docker.sh
bash get_docker.sh
```

### install docker-compose

```
apt-get install -y libffi-dev libssl-dev python3-dev
apt-get install -y python3 python3-pip
pip3 install docker-compose

```
  
  
### Installation and Execution
EdgeX is organized as Go Modules; there is no requirement to set the GOPATH or GO111MODULE envrionment variables nor is there a requirement to root all the components under ~/go (or $GOPATH) and use the go get command. In other words,

```
git clone git@github.com:edgexfoundry/edgex-go.git
cd edgex-go
make build
```  
If you do want to root everthing under $GOPATH, you're free to use that pattern as well
```
GO111MODULE=on && export GO111MODULE
go get github.com/edgexfoundry/edgex-go
cd $GOPATH/src/github.com/edgexfoundry/edgex-go
make build
```

To build the docker images, we need some modifications and follow the instructions at the [link](https://github.com/noobtechie/edgex-go/blob/main/RD-DeployingEdgeXthroughDockeronBeagleboneBlack-280122-1304-35.pdf). The most important instructions for compiling is to change the file edgex-go/cmd/sys-mgmt-agent/Dockerfile line#30
-FROM docker:20.10.10
+FROM mazzolino/docker:20
```
cd edgex-go
make docker
```

After the build is successfull doing docker image ls show the following
```
docker image ls
REPOSITORY                                TAG                                        IMAGE ID       CREATED             SIZE
edgexfoundry/security-bootstrapper        0.0.0-dev                                  d710ebe4c102   8 minutes ago       15.5MB
edgexfoundry/security-bootstrapper        c1fb6d23c38d8e4135415aa828943646775f2bb3   d710ebe4c102   8 minutes ago       15.5MB
<none>                                    <none>                                     f6c167b8d3a2   8 minutes ago       1.17GB
edgexfoundry/security-secretstore-setup   0.0.0-dev                                  e98ce80aa206   9 minutes ago       23.7MB
edgexfoundry/security-secretstore-setup   c1fb6d23c38d8e4135415aa828943646775f2bb3   e98ce80aa206   9 minutes ago       23.7MB
<none>                                    <none>                                     500162741d3c   10 minutes ago      1.18GB
edgexfoundry/security-proxy-setup         0.0.0-dev                                  0673ecb7588e   10 minutes ago      22.2MB
edgexfoundry/security-proxy-setup         c1fb6d23c38d8e4135415aa828943646775f2bb3   0673ecb7588e   10 minutes ago      22.2MB
<none>                                    <none>                                     428956dccdf7   11 minutes ago      1.17GB
edgexfoundry/support-scheduler            0.0.0-dev                                  cbb0262afd60   12 minutes ago      13.1MB
edgexfoundry/support-scheduler            c1fb6d23c38d8e4135415aa828943646775f2bb3   cbb0262afd60   12 minutes ago      13.1MB
<none>                                    <none>                                     93576791a3c0   12 minutes ago      1.17GB
edgexfoundry/sys-mgmt-agent               0.0.0-dev                                  9ba3ae623077   13 minutes ago      268MB
edgexfoundry/sys-mgmt-agent               c1fb6d23c38d8e4135415aa828943646775f2bb3   9ba3ae623077   13 minutes ago      268MB
<none>                                    <none>                                     c29135b3fb3d   14 minutes ago      1.17GB
edgexfoundry/support-notifications        0.0.0-dev                                  c3029202ce6f   11 hours ago        13.7MB
edgexfoundry/support-notifications        c1fb6d23c38d8e4135415aa828943646775f2bb3   c3029202ce6f   11 hours ago        13.7MB
<none>                                    <none>                                     7084ff5e20a2   11 hours ago        1.17GB
edgexfoundry/core-command                 0.0.0-dev                                  d0f56c4ea970   11 hours ago        12.9MB
edgexfoundry/core-command                 c1fb6d23c38d8e4135415aa828943646775f2bb3   d0f56c4ea970   11 hours ago        12.9MB
<none>                                    <none>                                     6161aa5fbba1   11 hours ago        1.17GB
edgexfoundry/core-metadata                0.0.0-dev                                  4affba18b84d   11 hours ago        13.6MB
edgexfoundry/core-metadata                c1fb6d23c38d8e4135415aa828943646775f2bb3   4affba18b84d   11 hours ago        13.6MB
edgexfoundry/core-data                    0.0.0-dev                                  dd2e50b1eabf   11 hours ago        15.8MB
edgexfoundry/core-data                    c1fb6d23c38d8e4135415aa828943646775f2bb3   dd2e50b1eabf   11 hours ago        15.8MB
golang                                    1.17-alpine3.15                            25233bb43442   6 days ago          296MB
mazzolino/docker                          20                                         3c4003cbd4b0   6 weeks ago         193MB
alpine                                    3.14                                       30a002f9ce29   3 months ago        3.83MB
```

You can remove all the big 1+GB files to save space
```
docker images -f "dangling=true" -q
docker image rmi $(docker images -f "dangling=true" -q)
```

# Steps to configrue docker-compose.yml
Go to compose-builder folder 
```
cd edgex-compose/compose-builder
sudo make gen no-secty dev
```

This will generate a docker-compose.yml file in the current directory. The docker file generated is already in the github with the modifications. Remove the lines containing ":z" flags for the volume bindings. This is only for non debian Linux distros. Additionally if you want to expose your microservices to the network other than the host change the IP address of the microservices from "127.0.0.1" to "0.0.0.0". Use the sed command for this purpose.
```
sed -i -e 's/127.0.0.1/0.0.0.0/g' docker-compose.yml
```


# Add  device-random
This Random Device Service need to run with other EdgeX Core Services, such as Core Metadata, Core Data, and Core Command. After starting the service, the random integer value will be generated by executing the Get commands, and it will create Event and Reading data into Core Data service. app-mqtt can subscribe to this service and publish to a MQTT broker later.

```
cd device-random
make docker
edgexfoundry/device-random                0.0.0-dev                                  cb3a9e683630   2 days ago     16.4MB
edgexfoundry/device-random                b4b7d92ebf9d87d8d84b804aa68b575b4f0789af   cb3a9e683630   2 days ago     16.4MB
```

Add to the docker-compose.yml
```
device-random:
    container_name: edgex-device-random
    depends_on:
    - consul
    - data
    - metadata
    - command
    env_file:
    - common.env
    environment:
      REGISTRY_HOST: edgex-core-consul
      SERVICE_HOST: edgex-device-random
    hostname: edgex-device-random
    image: edgexfoundry/device-random:0.0.0-dev
    networks:
      edgex-network: {}
    ports:
    - 0.0.0.0:59988:59988/tcp
    - 0.0.0.0:49988:49988
    read_only: true
    restart: always
    security_opt:
    - no-new-privileges:true
    user: 2002:2001
```


# Add app-service-configurable
Add the add-asc-mqtt-export.yml and asc-mqtt-export.env for the MQTT forwarding to the broker.
```
app-servi ce-mqtt:
    container_name: edgex-app-mqtt
    depends_on:
    - consul
    - data
    env_file:
    - common.env
    - as-common.env
    - asc-mqtt-export.env
    environment:
      MESSAGEQUEUE_HOST: edgex-redis
      REGISTRY_HOST: edgex-core-consul
      SERVICE_HOST: edgex-app-mqtt
      WRITABLE_PIPELINE_FUNCTIONS_MQTTEXPORT_PARAMETERS_BROKERADDRESS: tcp://broker.mqttdashboard.com:1883
      WRITABLE_PIPELINE_FUNCTIONS_MQTTEXPORT_PARAMETERS_TOPIC: EdgeXEvents
    hostname: edgex-app-mqtt
    image: edgexfoundry/app-service-configurable:0.0.0-dev
    networks:
      edgex-network: {}
    ports:
    - 0.0.0.0:59702:59702/tcp
    read_only: true
    security_opt:
    - no-new-privileges:true
    user: 2002:2001
```

# Running the EdgeX docker
Now run the docker-compose.yml file using the command 
```
sudo docker-compose -p edgex up -d
```
This might take a few minutes on the first run.

Start and stop docker edgex commands
```
sudo docker-compose -p edgex up -d
sudo docker-compose -p edgex down
```




To see the demo running go on 
http://www.hivemq.com/demos/websocket-client/
Press Connect and subscribe to IPSEdgeXEventsB


  
