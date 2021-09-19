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

### Vagrant Plugin
```
vagrant plugin install vagrant-sshfs (optional)
vagrant plugin install vagrant-notify-forwarder (optional but very useful for inotify to work)
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

#### Docker Compose also works
```
docker-compose up
docker-compose build
```

### Shared Folders

#### Default Virtual Box Shared Folders
The default way to share folders with docker is via Virtual Box shared folders. This method is not very efficiant and can be slow.

#### SSHFS
With an additional vagrant plugin you can use sshfs to share folders. This works better than the default shared folders but maybe not as well as SMB.  

https://github.com/dustymabe/vagrant-sshfs


#### SMB
This might provide the most efficent experience between host and docker VM. This requries additional settings and you will be prompted for your password and SMB settings when first starting up or rebooting the VM.

https://www.vagrantup.com/docs/synced-folders/smb

```
  # config.vm.synced_folder ENV["HOME"], ENV["HOME"]
  # config.vm.synced_folder ENV["HOME"], ENV["HOME"], type: "sshfs"
  config.vm.synced_folder ENV["HOME"], ENV["HOME"], type: "smb"
```

#### File Watcher (inotify)
Many development tools use auto reload when source code is modified on the host. This is very useful for development so I recommend installing the additional plugin to forward host file notifications to the VM.  

```
vagrant plugin install vagrant-notify-forwarder
```

https://github.com/mhallin/vagrant-notify-forwarder

