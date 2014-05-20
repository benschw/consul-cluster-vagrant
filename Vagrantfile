# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box_url = "https://cloud-images.ubuntu.com/vagrant/trusty/current/trusty-server-cloudimg-amd64-vagrant-disk1.box"
  config.vm.box = "trusty64"

  config.vm.define "consul1" do |consul1|
      $script = <<SCRIPT

apt-get install daemon

#==================================================
echo "Installing Consul"
cp /vagrant/bin/consul /usr/local/bin/
daemon -X "consul agent -server -bootstrap -data-dir /tmp/consul -node=consul1 -bind 172.20.20.10"

SCRIPT
      consul1.vm.provision "shell", inline: $script
      consul1.vm.hostname = "consul1"
      consul1.vm.network "private_network", ip: "172.20.20.10"
  end

  config.vm.define "consul2" do |consul2|
      $script = <<SCRIPT

apt-get install daemon

#==================================================
echo "Installing Consul"
cp /vagrant/bin/consul /usr/local/bin/
daemon -X "consul agent -server -data-dir /tmp/consul -node=consul2 -bind 172.20.20.11 -join 172.20.20.10" 

SCRIPT
      consul2.vm.provision "shell", inline: $script
      consul2.vm.hostname = "consul2"
      consul2.vm.network "private_network", ip: "172.20.20.11"
  end

  config.vm.define "status" do |status|
      $script = <<SCRIPT

apt-get install daemon

#==================================================
echo "Installing and Configuring Consul Status UI"
cp /vagrant/bin/consul /usr/local/bin/
daemon -X "consul agent -client=172.20.20.12 -ui-dir /vagrant/web/ -data-dir /tmp/consul -node=status -bind 172.20.20.12 -join 172.20.20.10"

SCRIPT

      status.vm.provision "shell", inline: $script
      status.vm.hostname = "status"
      status.vm.network "private_network", ip: "172.20.20.12"
  end

  config.vm.define "svc1" do |svc1|
      $script = <<SCRIPT

apt-get install daemon

#==================================================
echo "Installing and Configuring Consul"
cp /vagrant/bin/consul /usr/local/bin/
mkdir /etc/consul.d/
cp /vagrant/config/svc1.mysvc.json /etc/consul.d/mysvc.json
daemon -X "consul agent -data-dir /tmp/consul -node=service1 -config-dir /etc/consul.d -bind 172.20.20.13 -join 172.20.20.10"

#==================================================
echo "Installing and Configuring Confd"
cp /vagrant/bin/confd /usr/local/bin/
cp -r /vagrant/config/confd /etc/
daemon -X "confd -config-file /etc/confd/conf.d/config.toml"

#==================================================
echo "Installing and Configuring Demo Service"
cp /vagrant/bin/demo /usr/local/bin/
daemon -X "demo -addr=:8045"

SCRIPT
      svc1.vm.provision "shell", inline: $script
      svc1.vm.hostname = "svc1"
      svc1.vm.network "private_network", ip: "172.20.20.13"
  end

  config.vm.define "svc2" do |svc2|
      $script = <<SCRIPT
apt-get install daemon

#==================================================
echo "Installing and Configuring Consul"
cp /vagrant/bin/consul /usr/local/bin/
mkdir /etc/consul.d/
cp /vagrant/config/svc2.mysvc.json /etc/consul.d/mysvc.json
daemon -X "consul agent -data-dir /tmp/consul -node=service2 -config-dir /etc/consul.d -bind 172.20.20.14 -join 172.20.20.10"

#==================================================
echo "Installing and Configuring Confd"
cp -r /vagrant/config/confd /etc/
cp /vagrant/bin/confd /usr/local/bin/
daemon -X "confd -config-file /etc/confd/conf.d/config.toml"

#==================================================
echo "Installing and Configuring Demo Service"
cp /vagrant/bin/demo /usr/local/bin/
daemon -X "demo -addr=:8076"

SCRIPT
      svc2.vm.provision "shell", inline: $script
      svc2.vm.hostname = "svc2"
      svc2.vm.network "private_network", ip: "172.20.20.14"
  end

  config.vm.define "demo" do |demo|
      $script = <<SCRIPT
apt-get install daemon

#==================================================
echo "Installing and Configuring Consul"
cp /vagrant/bin/consul /usr/local/bin/
mkdir /etc/consul.d/
cp /vagrant/config/demo.demo.json /etc/consul.d/demo.json
daemon -X "consul agent -data-dir /tmp/consul -node=demo -config-dir /etc/consul.d -bind 172.20.20.15 -join 172.20.20.10"

#==================================================
echo "Installing and Configuring Demo App"
cp /vagrant/bin/demo /usr/local/bin/
daemon -X "demo -addr=:80"

SCRIPT
      demo.vm.provision "shell", inline: $script
      demo.vm.hostname = "demo"
      demo.vm.network "private_network", ip: "172.20.20.15"
  end



  config.vm.provider :virtualbox do |vb|
    vb.customize ["modifyvm", :id, "--memory", "256"]
  end

end
