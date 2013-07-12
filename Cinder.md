Today we experienced problems with Cinder getting stuck "Deleting" and "Error" status

We're getting closer to stability with Cinder, here is our /etc/cinder/cinder.conf

We had to add iscsi helper

```
[DEFAULT]
rootwrap_config=/etc/cinder/rootwrap.conf
sql_connection = mysql://cinderUser:cinderPass@10.10.10.51/cinder
api_paste_config = /etc/cinder/api-paste.ini
iscsi_helper=ietadm
volume_name_template = volume-%s
volume_group = cinder-volumes
verbose = True
auth_strategy = keystone
iscsi_ip_address=10.10.10.51
iscsihelper=tgtadm
```