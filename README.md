Ubuntu ZooKeeper with Serf
==========================

Vagrant build for Ubuntu with Serf

Requires Vagrant v2 - http://www.vagrantup.com/

Also requires Vagrant plugins:
  - vagrant-berkshelf
  - vagrant-omnibus
  - vagrant-hosts
  - vagrant-cachier

Installs:
  - Apache ZooKeeper
  - Serf

The following environment variables are required for every vagrant command:

  - VM_HOSTNAME - the hostname to configure the VM with
  - VM_MEM - VM ram (in mb)
  - ZK_ID - the ZooKeeper ID

Example:

    VM_HOSTNAME="zk1" VM_MEM="1024" ZK_ID=1 vagrant up