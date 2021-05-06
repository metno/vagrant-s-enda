# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/focal64"
  config.vm.box_check_update = false
  config.vm.network "private_network", ip: "10.10.10.10"
  # config.vm.network "public_network"
  config.vm.synced_folder "./", "/vagrant"

  config.vm.provider "virtualbox" do |vb|
    vb.cpus = "2"
    vb.memory = "2048"
    vb.linked_clone = true
  end

  config.vm.define "k3s" do |config|
    config.vm.hostname = "k3s"
  end

  config.vm.provision "shell", inline: <<-SHELL
    PROXY="#{ENV['PROXY']}"
    [ ! -z "${PROXY}" ] || echo 'Acquire::http::proxy "http://'${PROXY}':3142/";' > /etc/apt/apt.conf.d/80proxy

    apt-get update
    apt-get install -y python3-pip
    pip3 install ansible
  SHELL

  config.vm.provision "k3s", type: "ansible_local" do |a|
    a.playbook = "k3s.yml"
    a.extra_vars = {
      "proxy_address": "#{ENV['PROXY']}",
      "node_ip": "10.10.10.10"
    }
  end
end
