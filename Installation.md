Server Stack
---

ALL NETWORK FIELDS MUST BE COMPLETED:
```
auto eth0
iface eth0 inet static
address 192.168.0.42
network 192.168.0.0
netmask 255.255.255.0
broadcast 192.168.0.255
gateway 192.168.0.1
```

### OpenStackNetwork

##### Networking
eth2 ip: 10.100.60.25

### OpenStackController

##### Networking
eth0 ip: 10.100.60.26

### OpenStackCompute01

##### Networking
eth0 ip: 10.100.60.27


##### Databases
- keystone
- glance
- quantum
- nova
- cinder

##### Endpoints