# -*- mode: ruby -*-
# vi: set ft=ruby :
$script = <<SCRIPT

//echo "add common stuff here"

SCRIPT

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box_url = "https://cloud-images.ubuntu.com/vagrant/trusty/current/trusty-server-cloudimg-amd64-vagrant-disk1.box"
  config.vm.box = "trusty64"

  config.vm.provision "shell", inline: $script

  config.vm.define "consul1" do |consul1|
      consul1.vm.hostname = "consul1"
      consul1.vm.network "private_network", ip: "172.20.20.10"
  end

  config.vm.define "consul2" do |consul2|
      consul2.vm.hostname = "consul2"
      consul2.vm.network "private_network", ip: "172.20.20.11"
  end

  config.vm.define "status" do |status|
      status.vm.hostname = "status"
      status.vm.network "private_network", ip: "172.20.20.12"
  end

  config.vm.define "svc1" do |svc1|
      svc1.vm.hostname = "svc1"
      svc1.vm.network "private_network", ip: "172.20.20.13"
  end

  config.vm.define "svc2" do |svc2|
      svc2.vm.hostname = "svc2"
      svc2.vm.network "private_network", ip: "172.20.20.14"
  end

  config.vm.define "demo" do |demo|
      demo.vm.hostname = "demo"
      demo.vm.network "private_network", ip: "172.20.20.15"
  end



  config.vm.provider :virtualbox do |vb|
    vb.customize ["modifyvm", :id, "--memory", "256"]
  end

end
