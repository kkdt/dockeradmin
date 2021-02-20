# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
    # The most common configuration options are documented and commented below.
    # For a complete reference, please see the online documentation at
    # https://docs.vagrantup.com.

    # Every Vagrant development environment requires a box. You can search for
    # boxes at https://vagrantcloud.com/search.
    config.vm.box = "kkdt/g7dev"
    config.vm.box_version = "0.1"
    config.vm.define "dockeradmin"
    config.vm.hostname = "dockeradmin"

    config.vm.provider "virtualbox" do |vb|
        vb.gui = false
        vb.name = "dockeradmin"
        vb.memory = 1024
        vb.cpus = 1
    end

    config.vm.provision "software", type: "shell", inline: <<-SHELL
        # install docker
        sudo yum install -y yum-utils
        sudo yum-config-manager \
            --add-repo \
            https://download.docker.com/linux/centos/docker-ce.repo
        sudo yum -y install docker-ce docker-ce-cli containerd.io

        # allow to run as non-root
        sudo groupadd docker
        sudo usermod -aG docker vagrant

        # start docker
        sudo systemctl start docker

        # install docker-compose
        # current release: https://github.com/docker/compose/releases
        sudo curl -L "https://github.com/docker/compose/releases/download/1.28.4/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
        sudo chmod +x /usr/local/bin/docker-compose
        docker-compose --version
    SHELL

end