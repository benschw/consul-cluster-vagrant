
## repo contents
- bin/consul https://dl.bintray.com/mitchellh/consul/0.2.0_linux_amd64.zip
- bin/confd https://github.com/kelseyhightower/confd (built off master on May 16, 2014)
- bin/demo (https://github.com/benschw/consul-clb-go)
- web/ https://dl.bintray.com/mitchellh/consul/0.2.0_web_ui.zip

# Install Log
## Initialize cluster
	vagrant up


## Things to try
- goto status page: http://172.20.20.12:8500/ui
- goto demo address: http://172.20.20.15/demo
- test "my-svc": http://172.20.20.13:8045/foo
- look at the health check: http://172.20.20.13:8045/status (http://172.20.20.15/status, http://172.20.20.14:8076/status)
- cli tool
	+ consul members
	+ consul info
- REST API
	+ vagrant ssh demo; curl http://localhost:8500/v1/catalog/service/my-svc
- KV store
	+ http://172.20.20.12:8500/ui/#/dc1/kv/
	+ vagrant ssh demo; curl -X PUT -d 'bar' http://localhost:8500/v1/kv/foo
	+ check http://172.20.20.15/demo to see "foo" populated now
- DNS
	- vagrant ssh demo; dig @127.0.0.1 -p 8600 my-svc.service.consul SRV
- fail a health check
	+ vagrant ssh svc1
	+ touch /tmp/fail-healthcheck
		* status page: http://172.20.20.12:8500/ui
		* see failure: http://172.20.20.13:8045/status
		* see load balancing in action: http://172.20.20.15/demo (hit refresh)
		* see dns: dig @127.0.0.1 -p 8600 my-svc.service.consul SRV
		* rm /tmp/fail-healthcheck to fix things (it just comes back!)
- fix "bootstrap"
	+ 
	vagrant ssh consul1
	killall consul
	/vagrant/bin/consul agent -server -data-dir /tmp/consul \
		-node=consul1 -bind=172.20.20.10 -join 172.20.20.11 &
	disown
	exit
