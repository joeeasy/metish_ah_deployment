# -*- mode: ruby -*-
# vi: set ft=ruby :
#Operating system of my guest machines
BOX_OS = "ubuntu/trusty64"

# Number of machines I want to create
NODE_COUNTS = 1

Vagrant.configure("2") do |config|

  # Box settings
  (1..NODE_COUNTS).each do |i|
    nodeName = (i == 1) ? "frontend_server" : "backend_server"
    PORT = (i == 1) ? 3000 : 5000
    config.env.enable # Enable vagrant-env(.env)
    
    config.vm.define nodeName do |subconfig|
      subconfig.ssh.username= ENV['USERNAME']
      subconfig.ssh.password= ENV['PASSWORD']
      subconfig.vm.box = BOX_OS
      subconfig.vm.provider "virtualbox" do |provider| 
        provider.name = nodeName
        provider.gui = false
        provider.memory = "1024"
        provider.cpus = 2
      end
      subconfig.vm.network "private_network", ip: "10.0.0.#{ i + 10}"
      subconfig.vm.network "forwarded_port", guest: 80, host: PORT
    end
  end

end
