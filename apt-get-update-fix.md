# push out mtu of 1454 bytes to clients
dhcp-option=26,1454
# logging options for debugging
log-dhcp
log-facility=/var/log/quantum/dnsmasq.log

/etc/quantum/dnsmasq.conf
dnsmasq_config_file = /etc/quantum/dnsmasq.conf
/etc/quantum/dhcp_agent.ini