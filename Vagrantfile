# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.define "box1" do |box1|
    box1.vm.box = "hashicorp/bionic64"
    box1.vm.network "private_network", ip: "192.168.56.15"
    box1.vm.hostname = "ansbox"
    
    box1.vm.synced_folder '.', '/vagrant', disabled: true
    box1.vm.synced_folder "/home/viktorng/vagrant-vms/data", "/opt/data"

    box1.vm.provider "virtualbox" do |vb|
      vb.name = "Box 1"
      vb.memory = "2048"
      vb.cpus = "1"
    end

    box1.vm.provision "ansible" do |ansible|
      ansible.playbook = "snort.yaml"
      ansible.inventory_path = "inventory"
      ansible.limit = "all"
    end
  end
end