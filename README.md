### Ansible playbook for deploying DRBD storage on 2 VMs with Vagrant

Setup DRBD storage with vagrant and ansible for testing purposes.

VMs OS = Ubuntu Xenial
### Usage
```vagrant up -d```

After VMs startup and provision you will get DRBD storage with 2 nodes in secondary mode.

Promote one of them to primary.
```
vagrant ssh machine1
sudo drbdadm -- --overwrite-data-of-peer primary all
```
View syncronization status
```
cat /proc/drbd
```
DRBD will map storage to /dev/drbd0

### Tested with
```
$ vagrant --version
Vagrant 2.0.0
$ ansible --version
ansible 2.4.1.0
```
