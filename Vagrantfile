# -*- mode: ruby -*-
# # vi: set ft=ruby :

# Require YAML module
require 'yaml'

env = 'public-network'

# Read YAML file with box details
environment = "inventories/vagrant.yaml"
servers = YAML.load_file(environment)

# Create boxes
Vagrant.configure("2") do |config|

  # Iterate through entries in YAML file
  servers.each do |servers|
    config.vm.define servers["name"] do |srv|
      srv.vm.box = "debian/jessie64"
#      srv.vm.box = "ubuntu/trusty64"
#      srv.vm.box = "debian/jessie64"
      srv.vm.synced_folder ".", "/vagrant", disabled: true
      srv.vm.synced_folder ".", "/home/vagrant/sync", disabled: true
      srv.vm.network "private_network", ip: servers["ip"]
#      srv.vm.network "public_network", bridge: "en0: Wi-Fi (AirPort)", ip: servers["ip"]
      unless servers["port_frwd_guest"].nil?
      srv.vm.network "forwarded_port", guest: servers["port_frwd_guest"], host: servers["port_frwd_host"]
    end
      srv.vm.provider :virtualbox do |vb|
        vb.name = servers["name"]
        vb.memory = 1024
        vb.cpus = 1
      end
    end
  end

  config.vm.provision :ansible do |ansible|
    ansible.host_key_checking = false
    ansible.playbook = "inventories/provision.yml"
    ansible.become = true
    ansible.host_vars = {}
    ansible.groups = {}

  end
end
