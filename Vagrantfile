# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  config.vm.define "redis_master" do |redis_master|
    redis_master.vm.hostname = "redis-master"
    redis_master.vm.box = "bento/ubuntu-18.04"
    redis_master.vm.network "private_network", ip: "192.168.16.102"
    
    redis_master.vm.provider "virtualbox" do |vb|
      vb.name = "redis-master"
      vb.gui = false
      vb.memory = "1024"
    end

    redis_master.vm.provision "shell", path: "sh/redis_master.sh", privileged: false
  end

  config.vm.define "redis_slave_1" do |redis_slave_1|
    redis_slave_1.vm.hostname = "redis-slave-1"
    redis_slave_1.vm.box = "bento/ubuntu-18.04"
    redis_slave_1.vm.network "private_network", ip: "192.168.16.103"
    
    redis_slave_1.vm.provider "virtualbox" do |vb|
      vb.name = "redis-slave-1"
      vb.gui = false
      vb.memory = "1024"
    end

    redis_slave_1.vm.provision "shell", path: "sh/redis_slave_1.sh", privileged: false
  end

  config.vm.define "redis_slave_2" do |redis_slave_2|
    redis_slave_2.vm.hostname = "redis-slave-2"
    redis_slave_2.vm.box = "bento/ubuntu-18.04"
    redis_slave_2.vm.network "private_network", ip: "192.168.16.104"
    
    redis_slave_2.vm.provider "virtualbox" do |vb|
      vb.name = "redis-slave-2"
      vb.gui = false
      vb.memory = "1024"
    end

    redis_slave_2.vm.provision "shell", path: "sh/redis_slave_2.sh", privileged: false
  end

end
