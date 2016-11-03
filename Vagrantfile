# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure(2) do |config|
  config.ssh.private_key_path= "../keys/vagrant"
  # Common to all VMs
  config.vm.box = "ubuntu-16.04.1-desktop-amd64"
  config.vm.boot_timeout = 90
  config.vm.synced_folder ".", "/vagrant", disabled: true
  config.vm.synced_folder "data/", "/data"
  config.vm.provision :ansible do |ansible|
    ansible.playbook = "ansible/vagrant-vm-playbook.yml"
    ansible.groups = {
      "networking1" => ["pc-192.168.1.[100:102]","pc-172.16.0.[100:102]"],
      "networking2" => ["pc-192.168.200.100","pc-172.16.200.100"],
      "both" => ["Internet","NAT1","NAT2"]
    }
  end

  # For each of these networks...
  ["192.168.1","172.16.0"].each do |net|
    # ... configure these three nodes
    (100..102).each do |host|
      config.vm.define "pc-#{net}.#{host}" do |node|
        node.vm.network "private_network", ip: "#{net}.#{host}", virtualbox__null: true, auto_config: false
        node.vm.provider "virtualbox" do |vb|
          vb.name = "pc-#{net}.#{host}"
          vb.customize ["modifyvm", :id, "--cableconnected1", "on"]
          vb.customize ["modifyvm", :id, "--nic2", "null"]
        end
      end
    end
  end

  # PC on the first network
  config.vm.define "pc-192.168.200.100" do |node|
    node.vm.hostname = "pc-192-168-200-100"
    node.vm.network "private_network", ip: "192.168.200.100", virtualbox__null: true, auto_config: false
    node.vm.provider "virtualbox" do |vb|
      vb.name = "pc-192.168.200.100"
      vb.customize ["modifyvm", :id, "--cableconnected1", "on"]
      vb.customize ["modifyvm", :id, "--nic2", "null"]
    end
  end

  # PC on the second network
  config.vm.define "pc-172.16.200.100" do |node|
    node.vm.hostname = "pc-172-16-200-100"
    node.vm.network "private_network", ip: "172.16.200.100", virtualbox__null: true, auto_config: false
    node.vm.provider "virtualbox" do |vb|
      vb.name = "pc-172.16.200.100"
      vb.customize ["modifyvm", :id, "--cableconnected1", "on"]
      vb.customize ["modifyvm", :id, "--nic2", "null"]
    end
  end

  # First NAT gateway
  config.vm.define "NAT1" do |node|
    node.vm.hostname = "NAT1"
    node.vm.box = "ubuntu-16.04.1-server-amd64"
    node.vm.network "private_network", ip: "10.0.0.254", virtualbox__null: true, auto_config: false
    node.vm.provider "virtualbox" do |vb|
      vb.name = "NAT1"
      vb.customize ["modifyvm", :id, "--cableconnected1", "on"]
      vb.customize ["modifyvm", :id, "--nic2", "null"]
      vb.customize ["modifyvm", :id, "--nic3", "null"]
    end
  end

  # Second NAT gateway
  config.vm.define "NAT2" do |node|
    node.vm.hostname = "NAT2"
    node.vm.box = "ubuntu-16.04.1-server-amd64"
    node.vm.network "private_network", ip: "10.0.0.254", virtualbox__null: true, auto_config: false
    node.vm.provider "virtualbox" do |vb|
      vb.name = "NAT2"
      vb.customize ["modifyvm", :id, "--cableconnected1", "on"]
      vb.customize ["modifyvm", :id, "--nic2", "null"]
      vb.customize ["modifyvm", :id, "--nic3", "null"]
    end
  end

  # "Internet" (Linux VM routing and doing NAT)
  config.vm.define "Internet" do |node|
    node.vm.hostname = "Internet"
    node.vm.box = "ubuntu-16.04.1-server-amd64"
    node.vm.network "private_network", ip: "1.1.1.254", virtualbox__null: true, auto_config: false
    node.vm.network "private_network", ip: "2.2.2.254", virtualbox__null: true, auto_config: false
    node.vm.provider "virtualbox" do |vb|
      vb.name = "Internet"
      vb.customize ["modifyvm", :id, "--cableconnected1", "on"]
      vb.customize ["modifyvm", :id, "--nic2", "null"]
      vb.customize ["modifyvm", :id, "--nic3", "null"]
    end
  end

end
