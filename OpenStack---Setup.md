## outstanding issues

- Volume creation results in STATUS:ERROR

## Physical Machines

| MARK  | IP            | Hostname             | RU  | RAM   | HDD   | Cores  | CPU Model              |
| ----- | ------------- | -------------------- | --- | ----- | ----- | ------ | ---------------------- |
| ['· ] | 192.168.0.246 | openstack-controller |  1  | 2GB   | 73GB  | 2      | Xeon 5150 @ 2.66GHz    |
| [ ·'] | 192.168.0.244 | openstack-compute-01 |  1  | 48GB  | 64GB  | 16     | Xeon E5640 @ 2.67GHz   |
| [.· ] | DHCP          | openstack-network    |  1  | 8GB   | 73GB  | 4      | Xeon E5504 @ 2.00GHz   |

Two other machines exist at our disposal, but are not in use yet. See [them here](Unused-Hardware)

[How to get temporary internet access on a restricted node](Temporary-Internet-Access)

---

## openstack-controller

Status: Rocking.

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

### /etc/nova/nova.conf
```
[DEFAULT]
logdir=/var/log/nova
state_path=/var/lib/nova
lock_path=/run/lock/nova
verbose=True
api_paste_config=/etc/nova/api-paste.ini
compute_scheduler_driver=nova.scheduler.simple.SimpleScheduler
rabbit_host=10.10.10.51
nova_url=http://10.10.10.51:8774/v1.1/
sql_connection=mysql://novaUser:novaPass@10.10.10.51/nova
root_helper=sudo nova-rootwrap /etc/nova/rootwrap.conf

# Auth
use_deprecated_auth=false
auth_strategy=keystone

# Imaging service
glance_api_servers=10.10.10.51:9292
image_service=nova.image.glance.GlanceImageService

# Vnc configuration
novnc_enabled=true
novncproxy_base_url=http://192.168.0.246:6080/vnc_auto.html
novncproxy_port=6080
vncserver_proxyclient_address=192.168.0.246
vncserver_listen=0.0.0.0

# Network settings
network_api_class=nova.network.quantumv2.api.API
quantum_url=http://10.10.10.51:9696
quantum_auth_strategy=keystone
quantum_admin_tenant_name=service
quantum_admin_username=quantum
quantum_admin_password=service_pass
quantum_admin_auth_url=http://10.10.10.51:35357/v2.0
libvirt_vif_driver=nova.virt.libvirt.vif.LibvirtHybridOVSBridgeDriver
linuxnet_interface_driver=nova.network.linux_net.LinuxOVSInterfaceDriver
#If you want Quantum + Nova Security groups
firewall_driver=nova.virt.firewall.NoopFirewallDriver
security_group_api=quantum
#If you want Nova Security groups only, comment the two lines above and uncomment line -1-.
#-1-firewall_driver=nova.virt.libvirt.firewall.IptablesFirewallDriver

#Metadata
service_quantum_metadata_proxy = True
quantum_metadata_proxy_shared_secret = helloOpenStack

# Compute #
compute_driver=libvirt.LibvirtDriver

# Cinder #
volume_api_class=nova.volume.cinder.API
osapi_volume_listen_port=5900
```

## openstack-compute-01

Status: Rocking, but VNC is not working. [working on this -k]

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

### /etc/nova/nova.conf
```
[DEFAULT]
logdir=/var/log/nova
state_path=/var/lib/nova
lock_path=/run/lock/nova
verbose=True
api_paste_config=/etc/nova/api-paste.ini
compute_scheduler_driver=nova.scheduler.simple.SimpleScheduler
rabbit_host=10.10.10.51
nova_url=http://10.10.10.51:8774/v1.1/
sql_connection=mysql://novaUser:novaPass@10.10.10.51/nova
root_helper=sudo nova-rootwrap /etc/nova/rootwrap.conf

# Auth
use_deprecated_auth=false
auth_strategy=keystone

# Imaging service
glance_api_servers=10.10.10.51:9292
image_service=nova.image.glance.GlanceImageService

# Vnc configuration
novnc_enabled=true
novncproxy_base_url=http://192.168.0.246:6080/vnc_auto.html
novncproxy_port=6080
vncserver_proxyclient_address=10.10.10.53
vncserver_listen=0.0.0.0

# Network settings
network_api_class=nova.network.quantumv2.api.API
quantum_url=http://10.10.10.51:9696
quantum_auth_strategy=keystone
quantum_admin_tenant_name=service
quantum_admin_username=quantum
quantum_admin_password=service_pass
quantum_admin_auth_url=http://10.10.10.51:35357/v2.0
libvirt_vif_driver=nova.virt.libvirt.vif.LibvirtHybridOVSBridgeDriver
linuxnet_interface_driver=nova.network.linux_net.LinuxOVSInterfaceDriver
#If you want Quantum + Nova Security groups
firewall_driver=nova.virt.firewall.NoopFirewallDriver
security_group_api=quantum
#If you want Nova Security groups only, comment the two lines above and uncomment line -1-.
#-1-firewall_driver=nova.virt.libvirt.firewall.IptablesFirewallDriver

#Metadata
service_quantum_metadata_proxy = True
quantum_metadata_proxy_shared_secret = helloOpenStack

# Compute #
compute_driver=libvirt.LibvirtDriver

# Cinder #
volume_api_class=nova.volume.cinder.API
osapi_volume_listen_port=5900
cinder_catalog_info=volume:cinder:internalURL
```

## openstack-network

Status: Rocking.

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

### Routing Table

```
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
default         andaman         0.0.0.0         UG    0      0        0 eth4
10.10.10.0      *               255.255.255.0   U     0      0        0 eth3
192.168.0.0     *               255.255.255.0   U     0      0        0 eth4
```

### /etc/quantum/api-paste.ini

```
[composite:quantum]
use = egg:Paste#urlmap
/: quantumversions
/v2.0: quantumapi_v2_0

[composite:quantumapi_v2_0]
use = call:quantum.auth:pipeline_factory
noauth = extensions quantumapiapp_v2_0
keystone = authtoken keystonecontext extensions quantumapiapp_v2_0

[filter:keystonecontext]
paste.filter_factory = quantum.auth:QuantumKeystoneContext.factory

[filter:authtoken]
paste.filter_factory = keystoneclient.middleware.auth_token:filter_factory

[filter:extensions]
paste.filter_factory = quantum.api.extensions:plugin_aware_extension_middleware_factory

[app:quantumversions]
paste.app_factory = quantum.api.versions:Versions.factory

[app:quantumapiapp_v2_0]
paste.app_factory = quantum.api.v2.router:APIRouter.factory

[filter:authtoken]
paste.filter_factory = keystoneclient.middleware.auth_token:filter_factory
auth_host = 10.10.10.51
auth_port = 35357
auth_protocol = http
admin_tenant_name = service
admin_user = quantum
admin_password = service_pass
```

### /etc/quantum/dhcp_agent.ini

```
[DEFAULT]
interface_driver = quantum.agent.linux.interface.OVSInterfaceDriver
dhcp_driver = quantum.agent.linux.dhcp.Dnsmasq
root_helper = sudo quantum-rootwrap /etc/quantum/rootwrap.conf
```

### /etc/quantum/l3_agent.ini

```
[DEFAULT]
interface_driver = quantum.agent.linux.interface.OVSInterfaceDriver
```

### /etc/quantum/metadata_agent.ini

```
[DEFAULT]
auth_url = http://10.10.10.51:35357/v2.0
auth_region = RegionOne
admin_tenant_name = service
admin_user = quantum
admin_password = service_pass
nova_metadata_ip = 10.10.10.51
nova_metadata_port = 8775
metadata_proxy_shared_secret = helloOpenStack
```

### /etc/quantum/quantum.conf

```
[DEFAULT]
lock_path = $state_path/lock
bind_host = 0.0.0.0
bind_port = 9696
core_plugin = quantum.plugins.openvswitch.ovs_quantum_plugin.OVSQuantumPluginV2
api_paste_config = /etc/quantum/api-paste.ini
control_exchange = quantum
rabbit_host = 10.10.10.51
notification_driver = quantum.openstack.common.notifier.rpc_notifier
default_notification_level = INFO
notification_topics = notifications

[QUOTAS]

[DEFAULT_SERVICETYPE]

[AGENT]
root_helper = sudo quantum-rootwrap /etc/quantum/rootwrap.conf

[keystone_authtoken]
auth_host = 10.10.10.51
auth_port = 35357
auth_protocol = http
admin_tenant_name = service
admin_user = quantum
admin_password = service_pass
signing_dir = /var/lib/quantum/keystone-signing
```

### /etc/quantum/rootwrap.conf

```
[DEFAULT]
filters_path=/etc/quantum/rootwrap.d,/usr/share/quantum/rootwrap
```

### /etc/quantum/plugins/openvswitch/ovs_quantum_plugin.ini

```
[DATABASE]
sql_connection = mysql://quantumUser:quantumPass@10.10.10.51/quantum
reconnect_interval = 2

[OVS]
tenant_network_type = gre
tunnel_id_ranges = 1:1000
integration_bridge = br-int
tunnel_bridge = br-tun
local_ip = 10.10.10.52
enable_tunneling = True

[AGENT]
polling_interval = 2

[SECURITYGROUP]
firewall_driver = quantum.agent.linux.iptables_firewall.OVSHybridIptablesFirewallDriver
```