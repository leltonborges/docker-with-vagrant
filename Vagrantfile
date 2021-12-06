$script_docker = <<-SCRIPT
  apt-get update && \
  curl -fsSL https://get.docker.com | sh  && \
  usermod -aG docker vagrant 
SCRIPT

$script_swarm = <<-SCRIPT
  docker swarm init --advertise-addr $(hostname -I | cut -d ' ' -f1)
SCRIPT

Vagrant.configure("2") do |config|
    config.vm.box = "generic/ubuntu1804"
    config.vm.provider :libvirt do |vb|
        vb.memory = "1024"
    end

    config.vm.define "master" do |master|
        master.vm.define "master"
        master.vm.synced_folder "./config", "/vagrant"
        master.vm.network "public_network", bridge: "enp3s0"
        master.vm.provision "shell", inline: $script_docker
        master.vm.hostname = "master"
        master.vm.provision "shell", inline: $script_swarm
    end

    (1..3).each do |i|
        config.vm.define "node#{i}" do |node|
            node.vm.hostname = "node#{i}"
            node.vm.network "public_network", bridge: "enp3s0"
            node.vm.provision "shell", inline: $script_docker
		end
    end
end