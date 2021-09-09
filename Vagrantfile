# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/hirsute64"
  config.vm.box_check_update = false
  config.vm.network "private_network", ip: "10.10.10.10"
  # config.vm.network "public_network"
  config.vm.synced_folder "./", "/vagrant"

  config.vm.provider "virtualbox" do |vb|
    vb.cpus = "2"
    vb.memory = "3072"
    vb.linked_clone = true
  end

  config.vm.define "k3s" do |config|
    config.vm.hostname = "k3s"
  end

  config.vm.provision "prepare installation of k3s", type: "shell", inline: <<-SHELL
    apt-get update
    apt-get install -y python3-pip tree
    pip3 install ansible==3.3
  SHELL

  config.vm.provision "install k3s with ansible", type: "ansible_local" do |a|
    a.playbook = "k3s.yml"
    a.compatibility_mode = "2.0"
    a.extra_vars = {
      "node_ip": "10.10.10.10"
    }
  end

  config.vm.provision "start or update kubernetes deployment", type: "shell", run: "always", inline: <<-SHELL
    # seed the config map, so we don't get too large config for kubectl apply
    kubectl get configmaps| grep jts-jar >/dev/null
    if [ $? != 0 ] ; then
        kubectl create configmap jts-jar --from-file=/vagrant/deployment/bases/solr/config/jts-core-1.18.2.jar
    fi
    kustomize build /vagrant/deployment/environment/vagrant/ | kubectl apply -f -
  SHELL
end
