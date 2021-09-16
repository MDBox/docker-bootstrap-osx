# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "bento/ubuntu-20.04"

  # config.vm.box_check_update = false
  config.vm.network "forwarded_port", guest: 2375, host: 2375, host_ip: "127.0.0.1"
  config.vm.network "private_network", ip: "192.168.33.10"
  # config.vm.network "public_network"

  # We currently mount /Users/homedir
  config.vm.synced_folder ENV["HOME"], ENV["HOME"]


  config.vm.provider "virtualbox" do |vb|
  #    vb.gui = false
      vb.memory = "2048"
  end

  config.vm.provision "file", source: "./docker.service", destination: "/tmp/"
  config.vm.provision "file", source: "./daemon.json", destination: "/tmp/"

  config.vm.provision "shell", inline: <<-SHELL
  	apt-get update
  	apt-get install -y apt-transport-https     ca-certificates     curl     gnupg     lsb-release
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg     
    echo   "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

    apt-get update
    apt-get install -y docker-ce docker-ce-cli containerd.io

    mv /tmp/docker.service /lib/systemd/system/docker.service && chown root:root /lib/systemd/system/docker.service
    mv /tmp/daemon.json /etc/docker/daemon.json && chown root:root /etc/docker/daemon.json

    systemctl daemon-reload
    systemctl restart docker
    usermod -aG docker vagrant

  SHELL
end
