The compute node has no internet access, but I want to install VNC on it. How do I do this?

### Adding VNC

Here are some links about setting up VNC / console

http://docs.openstack.org/diablo/openstack-compute/admin/content/configuring-the-vnc-proxy.html

http://docs.openstack.org/trunk/openstack-compute/admin/content/nova-vncproxy-replaced-with-nova-novncproxy.html

In order to get the packages, we need internet access. In order to do this we have eth2 plugged into the switch as well.

#### Up the interface:
`sudo ifconfig eth2 up`

#### Add the route:
`sudo route add default gw 192.168.0.1 eth2`

#### Add the DNS settings:
`sudo sh -c 'echo "nameserver 8.8.8.8" >> /etc/resolv.conf'`

#### When finished with internet access remove the route:
`sudo route del default`

#### Reset the DNS settings back to normal:
`sudo resolvconf -u`

#### Down the interface:
`sudo ifconfig eth2 down`
