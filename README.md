
## repo contents
- bin/consul https://dl.bintray.com/mitchellh/consul/0.2.0_linux_amd64.zip
- bin/confd https://github.com/kelseyhightower/confd (built off master on May 16, 2014)
- bin/demo (https://github.com/benschw/consul-clb-go)
- web/ https://dl.bintray.com/mitchellh/consul/0.2.0_web_ui.zip

# Install Log

## Bootstrap first server agent
	vagrant ssh consul1
	/vagrant/bin/consul agent -server -bootstrap -data-dir /tmp/consul -node=consul1 -bind 172.20.20.10 &
	disown
	exit

## Start second server agent, join server1
	vagrant ssh consul2
	/vagrant/bin/consul agent -server -data-dir /tmp/consul -node=consul2 -bind=172.20.20.11 -join 172.20.20.10 &
	disown
	exit

## Restart server1, join server2
	vagrant ssh consul1
	killall consul
	/vagrant/bin/consul agent -server -data-dir /tmp/consul -node=consul1 -bind=172.20.20.10 -join 172.20.20.11 &
	disown
	exit

## Start Status webapp
	vagrant ssh status
	/vagrant/bin/consul agent -client=172.20.20.12 -ui-dir /vagrant/web/ -data-dir /tmp/consul -node=status -bind=172.20.20.12 -join 172.20.20.10 &
	disown
	exit

## Start first instance of example service
	vagrant ssh svc1
	#/etc/consul.d/mysvc.json
	{"service": {
	    "name": "my-svc", 
	    "tags": ["microservice"], 
	    "port": 8045, 
	    "check": {
	        "script": "curl localhost:8045/status | grep OK >/dev/null 2>&1", 
	        "interval": "5s"
	    }
	}}	

	/vagrant/bin/consul agent -data-dir /tmp/consul -node=service1 -config-dir /etc/consul.d -bind=172.20.20.13 -join 172.20.20.10 &
	disown
	/vagrant/bin/demo -addr=:8045 &
	disown
	exit

## Start second instance of example service
	vagrant ssh svc2
	cat <<CONFIG >/etc/consul.d/mysvc.json
	{"service": {
	    "name": "my-svc", 
	    "tags": ["microservice"], 
	    "port": 8076, 
	    "check": {
	        "script": "curl localhost:8076/status | grep OK >/dev/null 2>&1", 
	        "interval": "5s"
	    }
	}}	
	CONFIG

	/vagrant/bin/consul agent -data-dir /tmp/consul -node=service2 -config-dir /etc/consul.d -bind=172.20.20.14 -join 172.20.20.10 &
	disown
	/vagrant/bin/demo -addr=:8076 &
	disown
	exit


	# dig @127.0.0.1 -p 8600 my-svc.service.consul SRV


## Start Demo example webapp
	vagrant ssh demo
	cat <<CONFIG >/etc/consul.d/mysvc.json
	{"service": {
	    "name": "demo", 
	    "tags": ["webapp"], 
	    "port": 80, 
	    "check": {
	        "script": "curl localhost:80/status | grep OK >/dev/null 2>&1", 
	        "interval": "5s"
	    }
	}}	
	CONFIG

	/vagrant/bin/consul agent -data-dir /tmp/consul -node=demo -config-dir /etc/consul.d -bind=172.20.20.15 -join 172.20.20.10 &
	disown
	/vagrant/bin/demo -addr=:80 &
	disown
	exit

## Configure Confd on svc1
	vagrant ssh svc1
	sudo mkdir -p /etc/confd/{conf.d,templates}
	
	cat <<CONFIG >/etc/confd/conf.d/config.toml
	[confd]
	confdir = "/etc/confd"
	interval = 10
	prefix = "/"
	consul = true
	consul-addr = "localhost"

	[template]
	src = "my-config.yaml.tpl"
	dest = "/opt/my-config.yaml"
	keys = [
	  "foo",
	]
	CONFIG

	cat <<CONFIG >/etc/confd/templates/my-config.yaml.tpl
	foo: {{ .foo }}
	CONFIG

	curl -X PUT -d 'bar' http://localhost:8500/v1/kv/foo

	confd -config-file /etc/confd/conf.d/config.toml &
	disown
	# cat /opt/my-config.yaml
	exit

## Configure Confd on svc2
	vagrant ssh svc2
	sudo mkdir -p /etc/confd/{conf.d,templates}
	
	cat <<CONFIG >/etc/confd/conf.d/config.toml
	[confd]
	confdir = "/etc/confd"
	interval = 10
	prefix = "/"
	consul = true
	consul-addr = "localhost"

	[template]
	src = "my-config.yaml.tpl"
	dest = "/opt/my-config.yaml"
	keys = [
	  "foo",
	]
	CONFIG

	cat <<CONFIG >/etc/confd/templates/my-config.yaml.tpl
	foo: {{ .foo }}
	CONFIG

	curl -X PUT -d 'bar' http://localhost:8500/v1/kv/foo

	confd -config-file /etc/confd/conf.d/config.toml &
	disown
	# cat /opt/my-config.yaml

## Test it out
- goto status page: http://172.20.20.12:8500/ui
- goto demo address: http://172.20.20.15/demo
- edit kv: http://172.20.20.12:8500/ui/#/dc1/kv/
