# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  # Default box image for all virtual machines
  config.vm.box = "ubuntu/bionic64"
  
  # Define a list of nodes with their respective IP suffixes
  nodes = {
    "master" => "10",
    "node01" => "11",
    "node02" => "12",
    "node03" => "13"
  }

  # Loop through the nodes hash to define each VM configuration
  nodes.each do |name, ip_suffix|
    config.vm.define name do |node|
      node.vm.hostname = name
      
      # Set up a private network with a static IP for inter-node communication
      node.vm.network "private_network", ip: "192.168.56.#{ip_suffix}"
      
      # Hardware customization for VirtualBox provider
      node.vm.provider "virtualbox" do |vb|
        vb.memory = "512"
        vb.cpus = 1
      end

      # Shell provisioning: Install Docker CE on all nodes
      node.vm.provision "shell", inline: <<-SHELL
        apt-get update
        # Install dependencies for Docker repository
        apt-get install -y apt-transport-https ca-certificates curl software-properties-common
        # Add Docker's official GPG key
        curl -fsSL https://download.docker.com/linux/ubuntu/gpg | apt-key add -
        # Set up the stable Docker repository
        add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu bionic stable"
        apt-get update
        # Install Docker engine
        apt-get install -y docker-ce
        # Add 'vagrant' user to the docker group to run commands without sudo
        usermod -aG docker vagrant
      SHELL

      # Specific configuration for the Master/Manager node
      if name == "master"
        node.vm.provision "shell", inline: <<-SHELL
          # Initialize Docker Swarm and set the advertising address
          docker swarm init --advertise-addr 192.168.56.10
          # Generate the worker join token and save it to a shared folder (/vagrant)
          docker swarm join-token worker -q > /vagrant/worker_token.txt
        SHELL
      # Specific configuration for the Worker nodes
      else
        node.vm.provision "shell", inline: <<-SHELL
          # Wait until the master node creates the token file
          while [ ! -f /vagrant/worker_token.txt ]; do sleep 2; done
          # Read the token from the shared file
          TOKEN=$(cat /vagrant/worker_token.txt)
          # Join the Swarm cluster using the token and Master IP
          docker swarm join --token $TOKEN 192.168.56.10:2377
        SHELL
      end
    end
  end
end