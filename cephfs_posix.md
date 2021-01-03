## firewall setup
```
firewall-cmd --zone=public --add-port=6789/tcp --permanent
firewall-cmd --zone=public --add-port=6800-7100/tcp --permanent
firewall-cmd --reload
firewall-cmd --list-all
```
## add Ovirt POSIX storage domain with cephfs
```
path: 192.168.100.61:/
VFS type: ceph
mountpoint option: name=admin,secret=AQDtEfFf7eEEKxAAbyL2hLnMIzIZMKtXsbWf0g==
```
## ovirt multipath.conf setup
```
blacklist{
devnode "^sdc"
}

systemctl reload multipathd
multipath -ll
```
