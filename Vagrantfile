# -*- mode: ruby -*-
# # vi: set ft=ruby :

$dstack_node_script = <<SCRIPT
curl -i http://192.168.50.10:8081/v1/authorized_keys | update-ssh-keys -A dstack
curl -X POST http://192.168.50.10:8081/v1/agents -F user=core
SCRIPT

Vagrant.configure("2") do |config|
  config.vm.define "dstack-master" do |dstack|
    dstack.vm.box = "dstack-master"
    dstack.vm.box_url = "http://storage.core-os.net/coreos/amd64-generic/dev-channel/coreos_production_vagrant.box"
    dstack.vm.network "forwarded_port", guest: 8081, host: 8080
    dstack.vm.network "private_network", ip: "192.168.50.10"
    dstack.vm.provision "shell",
        inline: "sudo docker run -d -p 8081:8080 ibuildthecloud/dstack"
  end

  config.vm.define "dstack-node" do |dstack_node|
    dstack_node.vm.box = "dstack-node0"
    dstack_node.vm.box_url = "http://storage.core-os.net/coreos/amd64-generic/dev-channel/coreos_production_vagrant.box"
    dstack_node.vm.network "private_network", ip: "192.168.50.11"
    dstack_node.vm.provision "shell",
        inline: $dstack_node_script
  end

  # Uncomment below to enable NFS for sharing the host machine into the coreos-vagrant VM.
  # config.vm.network "private_network", ip: "172.12.8.150"
  # config.vm.synced_folder ".", "/home/core/share", id: "core", :nfs => true,  :mount_options   => ['nolock,vers=3,udp']

  # Fix docker not being able to resolve private registry in VirtualBox
  config.vm.provider :virtualbox do |vb, override|
    vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
    vb.customize ["modifyvm", :id, "--natdnsproxy1", "on"]
  end

  config.vm.provider :vmware_fusion do |vb, override|
    override.vm.box_url = "http://storage.core-os.net/coreos/amd64-generic/dev-channel/coreos_production_vagrant_vmware_fusion.box"
  end

  # plugin conflict
  if Vagrant.has_plugin?("vagrant-vbguest") then
    config.vbguest.auto_update = false
  end
end
