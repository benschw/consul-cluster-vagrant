# Consul & Confd Cluster Demo

_for a full explanation & walk through, check out http://txt.fliglio.com/2014/05/encapsulated-services-with-consul-and-confd/_

Here is a 6 node example environment running in Vagrant to demo [Consul](http://www.consul.io/) and [Confd](https://github.com/kelseyhightower/confd).

Look through the [Vagrantfile](https://github.com/benschw/consul-cluster-vagrant/blob/master/Vagrantfile) to get an idea of what we're setting up, but essentially we are building:

- 2 consul server agents
- 1 consul agent hosting a "status" web gui
- 2 example service nodes
- 1 demo web-app that will interact with the service
<!-- end of list -->

		$ git clone git@github.com:benschw/consul-cluster-vagrant.git
		$ cd consul-cluster-vagrant
		$ vagrant up
	

#### Some notes
- I've included dependencies in the `/bin` folder, but if you don't trust me the sources are listed below.
- `confd` is built off of master as consul support won't be baked in until the 0.4 release.
- Each node is wired to use 256mb ram, so this cluster should run OK on most systems. My i7/8gb laptop stays under a load of 1.

## repo contents
- bin/consul https://dl.bintray.com/mitchellh/consul/0.2.0_linux_amd64.zip
- bin/confd https://github.com/kelseyhightower/confd (built off master on May 16, 2014)
- bin/demo (https://github.com/benschw/consul-clb-go)
- web/ https://dl.bintray.com/mitchellh/consul/0.2.0_web_ui.zip

