# Alternative Docker Setup for Mac
The project is to help project teams that are not ready to use the new Docker Desktop subscription. 

## Background
It wasn't that long ago that in order to run a Docker Engine on a mac one had to setup their own virtual machine. When Docker Desktop arrived it made this process really easy by offering a one click solution. This repo is not going to provide a one click solution and it may not provide as an efficent method of running docker but hopefully it will help some project teams with the transition.  

## Getting Started

The first thing you need is to install some requierments. This project is currently setup with VirtualBox in mind but I think it could be adapted to other provides.

### Install Virtual Box 6.1.x

https://www.virtualbox.org/wiki/Downloads

#### Brew

```
brew install --cask virtualbox
```

### Install Vagrant 2.2.x (optional but useful for auto provisioning the VM)

https://www.vagrantup.com/

#### Brew

```
brew install --cask vagrant
```

### Run the vagrant provisioning

```
# Clone this repo and run the following commands.
vagrant up

# This makes it easier to communicate with docker containers
# This IP address is defined in the Vagrantfile under private network
sudo echo "192.168.33.10 localdocker" >> /etc/hosts
```

### Setting up Docker Commands and Tools
```
# These commands only install the cli tools (which are open source)
brew install docker
brew install docker-compose

docker context create localdocker --docker "host=tcp://localdocker:2375"
docker context use localdocker
```

* We can improve this method and use ssh in the future. Docker supports "ssh://localdocker:22" but you need to either use the private key vagrant creates or you need to add your public key to the virtual machine. 

### All Set
You should be able to start docker containers like normal. You can use the `localdocker` to access containers as well.

#### Simple Nginx Server
```
docker run --rm -it -p 8080:80 nginx

# Open the link in your local browser and you should see the "Welcome to Nginx".
http://localdocker:8080
```

#### Mount local files as well
```
docker run --rm -it -v $(pwd)/webcode:/etc/nginx/sites -p 8080:80 nginx
```
