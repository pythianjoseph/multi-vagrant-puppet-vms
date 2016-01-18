# vi: set ft=ruby :

# Builds Puppet Master and multiple Puppet Agent Nodes using JSON config file
# Author: Gary A. Stafford

# read vm and chef configurations from JSON files
nodes_config = (JSON.parse(File.read("nodes.json")))['nodes']

VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
#  config.vm.box = "ubuntu/trusty64"
    config.vm.box = "dliappis/centos65minlibvirt"
    config.ssh.pty = true

  nodes_config.each do |node|
    node_name   = node[0] # name of node
    node_values = node[1] # content of node

    config.vm.define node_name do |config|    
      # configures all forwarding ports in JSON array
      ports = node_values['ports']
      ports.each do |port|
        config.vm.network :forwarded_port,
          host:  port[':host'],
          guest: port[':guest'],
          id:    port[':id']
      end

      config.vm.hostname = node_name
      config.vm.network :private_network, ip: node_values[':ip']

#      config.vm.provider :virtualboix do |vb|
#        vb.customize ["modifyvm", :id, "--memory", node_values[':memory']]
#        vb.customize ["modifyvm", :id, "--name", node_name]
#        
      config.vm.provider :libvirt do |libvirt|
        libvirt.driver = 'kvm'
#        libvirt.management_network_name = 'vagrant'
#        libvirt.management_network_address = '192.168.32.0/24'
        libvirt.memory = node_values[':memory']
      end

      config.vm.provision :shell, :path => node_values[':bootstrap']
    end
  end
end
