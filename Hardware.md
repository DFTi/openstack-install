## Physical Machines

| MARK  | IP            | Hostname             | RU  | RAM   | HDD   | Cores  | CPU Model              |
| ----- | ------------- | -------------------- | --- | ----- | ----- | ------ | ---------------------- |
| ['· ] | 192.168.0.246 | openstack-controller |  1  | 2GB   | 73GB  | 2      | Xeon 5150 @ 2.66GHz    |
| [ ·'] | 192.168.0.244 | openstack-compute-01 |  1  | 48GB  | 64GB  | 16     | Xeon E5640 @ 2.67GHz   |
| [.· ] | 192.168.0.91  | openstack-network    |  1  | 8GB   | 73GB  | 4      | Xeon E5504 @ 2.00GHz   |

Two other machines exist at our disposal, but are not in use yet. See [them here](Unused-Hardware)

---

## openstack-controller

Status: Configured and seems to be working correctly.

### /etc/network/interfaces
```
# The loopback network interface
auto lo
iface lo inet loopback

# The primary network interface
auto eth1
iface eth1 inet static
address 192.168.0.246
netmask 255.255.255.0
gateway 192.168.0.1
dns-nameservers 8.8.8.8

#Not internet connected(used for OpenStack management)
auto eth0
iface eth0 inet static
address 10.10.10.51
netmask 255.255.255.0
```

## openstack-compute-01

Status: Configured and seems to be working correctly except for VM console access through Horizon.

### /etc/network/interfaces
```
# The loopback network interface
auto lo
iface lo inet loopback

# The primary network interface
auto eth0
iface eth0 inet static
address 10.10.10.53
netmask 255.255.255.0

# VM Configuration
auto eth1
iface eth1 inet static
address 10.20.20.53
netmask 255.255.255.0
```

## openstack-network

### NICs
#### Mobo
- eth0 [unused]
- eth1 [unused]

#### Intel Quad NIC
- eth2 (Connected to Network)
- eth3 [connected to switch]
- eth4 [connected to switch]
- eth5 [unused]

### /etc/network/interfaces
```
auto lo
iface lo inet loopback

auto eth3
iface eth3 inet static
address 10.10.10.52
netmask 255.255.255.0

auto eth4
iface eth4 inet dhcp

auto eth2
iface eth2 inet manual
up ifconfig $IFACE 0.0.0.0 up
up ip link set $IFACE promisc on
down ip link set $IFACE promisc off
down ifconfig $IFACE down
```