## Physical Machines

| MARK  | IP            | Hostname             | RU  | RAM   | HDD   | Cores  | CPU Model              |
| ----- | ------------- | -------------------- | --- | ----- | ----- | ------ | ---------------------- |
| ['· ] | 192.168.0.246 | openstack-controller |  1  | 2GB   | 73GB  | 2      | Xeon 5150 @ 2.66GHz    |
| [ ·'] | 192.168.0.244 | openstack-compute-01 |  1  | 48GB  | 64GB  | 16     | Xeon E5640 @ 2.67GHz   |
| [.· ] | 192.168.0.91  | openstack-network-01 |  1  | 8GB   | 73GB  | 4      | Xeon E5504 @ 2.00GHz   |

Two other machines exist at our disposal, but are not in use yet. See [them here](Unused-Hardware)

---

## openstack-controller
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

## openstack-network-01
### /etc/network/interfaces
Not network accessible -- misconfigured