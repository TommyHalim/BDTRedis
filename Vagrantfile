# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  config.vm.define "master" do |node|
    node.vm.box = "bento/ubuntu-16.04"
    node.vm.hostname = "master"
    node.vm.network "private_network", ip: "192.168.33.10"

    node.vm.provider "virtualbox" do |vb|
      vb.gui = false
      vb.name = "master"
      vb.memory = "512"
    end
  end

(1..2).each do |i|
  config.vm.define "slave#{i}" do |node|
    node.vm.box = "bento/ubuntu-18.04"
    node.vm.hostname = "slave#{i}"
    node.vm.network "private_network", ip: "192.168.33.1#{i}"

    node.vm.provider "virtualbox" do |vb|
      vb.gui = false
      vb.name = "slave#{i}"
      vb.memory = "512"
    end
    end
  end
end
