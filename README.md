```
openstack network create private 
neutron subnet-create --name subpriv private 2.2.2.0/24 
neutron router-create router1 
neutron router-interface-add router1 subpriv 
```

```
neutron net-create public \
--router:external \
--provider:network_type flat \
--provider:physical_network physnet1
```

```
neutron subnet-create \
--name subpub \
public 192.168.122.0/24 \
--enable-dhcp --allocation-pool start=192.168.122.150,end=192.168.122.200
```

```
neutron net-update --router:external=true public 
```

```
neutron router-gateway-set router1 public 
neutron net-list
openstack security group create secgroup1
openstack security group rule create secgroup1 --protocol icmp --prefix 0.0.0.0/0 --ingress
openstack security group rule create secgroup1 --protocol icmp --prefix 0.0.0.0/0 --egress
openstack security group rule create secgroup1 --protocol tcp --prefix 0.0.0.0/0 --ingress
openstack security group rule create secgroup1 --protocol udp --prefix 0.0.0.0/0 --ingress
openstack security group rule create secgroup1 --protocol tcp --prefix 0.0.0.0/0 --egress
openstack security group rule create secgroup1 --protocol udp --prefix 0.0.0.0/0 --egress
```

```
ssh-keygen -f mykey1 -q -N "" 
openstack keypair create --public-key mykey1.pub mykey1 
```

```
sudo yum install wget -y
```

```
wget https://download.cirros-cloud.net/0.5.1/cirros-0.5.1-x86_64-disk.img
```

```
glance image-create \
--container-format bare \
--disk-format qcow2 \
--name cirros- \
--file cirros-0.5.1-x86_64-disk.img
```

```
openstack flavor create m1.tiny --ram 512 --vcpus 1 
```

```
NET=$(openstack network list -c ID -c Name -f value | grep -i private| awk {'print $1'})
GLANCE=$(openstack image list -c ID -c Name -f value | grep -i cirros | awk {'print $1'})
FLAVOR=$(openstack flavor list -c ID -c Name -f value | grep -i tiny | awk {'print $1'})
x=$(date +"%Y-%m-%d_%H-%M-%S")
```

```
openstack server create  \
--flavor $FLAVOR \
--image $GLANCE \
--nic net-id=$NET  \
--security-group secgroup1  \
cirros_A_$x --wait
``

``
openstack server list
openstack floating ip create public
FIP=$(openstack floating ip list |grep -i non |awk '{print $4" " $6}' | awk {'print $1'} | tail -n1)
INSTANCE=$(nova list |awk '{print $2}' |awk 'NR > 2')
openstack server add floating ip $INSTANCE $FIP
openstack server list
openstack volume list
```
