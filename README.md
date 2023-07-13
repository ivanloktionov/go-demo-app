# Let's KIK!

#
## Runc and containerazed app https://developers.redhat.com/blog/2018/02/22/container-terminology-practical-introduction#containerized__components
	mkdir demo

vim Container
    
	FROM busybox
	CMD while true; do { echo -e 'HTTP/1.1 200 OK\n\nVersion: v1.0.0'; }|nc -vlp 8080;done
	EXPOSE 8080
 #   
	img build -t ivanloktionov/demo:v1.0.0 -f Container .
	img unpack ivanloktionov/demo:v1.0.0
	runc spec
	runc run demo
	vim config.json:
		terminal false
		"sh", "-c", "while true; do { echo -e 'HTTP/1.1 200 OK\n\nVersion: v1.0.0'; }|nc -vlp 8080;done"
		rootfs false
		"path": "/var/run/netns/runc"
## Network
	sudo brctl addbr runc0
	sudo ip link set runc0 up
	sudo ip addr add 192.168.10.1/24 dev runc0
	sudo ip link add name veth-host type veth peer name veth-guest
	sudo ip link set veth-host up
	sudo brctl addif runc0 veth-host
	sudo ip netns add runc
	sudo ip link set veth-guest netns runc
	sudo ip netns exec runc ip link set veth-guest name eth1
	sudo ip netns exec runc ip addr add 192.168.10.101/24 dev eth1
	sudo ip netns exec runc ip link set eth1 up
	sudo ip netns exec runc ip route add default via 192.168.10.1
	
    runc run demo
	curl 192.168.10.101:8080
	img push ivanloktionov/demo:v1.0.0
	