# install glusterfs on all nodes
yum -y install glusterfs-server gluster-ansible-roles
systemctl enable glusterd
systemctl start glusterd
firewall-cmd --permanent --add-service=glusterfs
firewall-cmd --reload

cat >> /etc/hosts << EOF
192.168.100.61 node11
192.168.100.62 node12
EOF

ssh-copy-id node11
ssh-copy-id node12

# config glusterfs brick on all nodes 
mkfs.xfs /dev/vg100/lv100
mkdir /gluster
echo "/dev/vg100/lv100 /gluster xfs defaults 0 0" >> /etc/fstab
mount -a
mkdir -p /gluster/brick1

# config glusterfs volume only one node
gluster peer probe node2
gluster peer probe node3
gluster peer status
gluster vol create vol1 replica 3 node{1..3}:/gluster/brick1
gluster vol create vol1 disperse-data 2 redundancy 1 node{1..3}:/gluster1/brick1
gluster vol list
gluster vol start vol1
gluster vol info

# config client
mount -t glusterfs node1:/vol1 /mnt
echo "node11:/vol1 /mnt glusterfs backupvolfile-server=node2,_netdev,defaults 0 0" >> /etc/fstab

# add storage-domain on ovirt
192.168.100.61:/vol1
backup-volfile-servers=192.168.100.62:192.168.100.63

# destroy gluster
gluster volume stop vol1
gluster volume delete vol1
gluster peer detach node12
