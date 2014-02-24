# -*- mode: ruby -*-
# vi: set ft=ruby :

## vagrant plugins required:
# vagrant-berkshelf, vagrant-omnibus, vagrant-hosts, vagrant-cachier

# https://github.com/everpeace/cookbook-mesos/blob/master/example/mesosphere/Vagrantfile

# Set VM_HOSTNAME, VM_MEM and ZK_ID
# Required even to run ssh/destroy
# E.g.
# VM_HOSTNAME="zk1" VM_MEM="256" ZK_ID=1 vagrant up

Vagrant.configure("2") do |config|

  # enable plugins
  config.berkshelf.enabled = true
  config.omnibus.chef_version = :latest
  config.cache.auto_detect = true

  config.vm.provider :virtualbox do |vb, override|
    #override.vm.box = "Official Ubuntu 13.04 daily Cloud Image amd64 (No Guest Additions)"
    #override.vm.box_url = "http://cloud-images.ubuntu.com/vagrant/raring/current/raring-server-cloudimg-amd64-vagrant-disk1.box"

    override.vm.box = "Official Ubuntu Precise64 Cloud Image current"
    override.vm.box_url = "http://cloud-images.ubuntu.com/vagrant/precise/current/precise-server-cloudimg-amd64-vagrant-disk1.box"

    override.vm.hostname = ENV['VM_HOSTNAME']
    override.vm.network :public_network

    vb.name = 'vagrant-mesos-' + ENV['VM_HOSTNAME']
    vb.customize ["modifyvm", :id, "--memory", ENV['VM_MEM'] ]
  end

  config.vm.provision :chef_solo do |chef|
#   chef.log_level = :debug
    chef.add_recipe "apt"
    chef.add_recipe "mesos"

    chef.json = {
      :mesos => {
        :type         => "mesosphere",
        :version      => "0.16.0",
        :mesosphere   => {
          :with_zookeeper => true
        }
      }
    }
  end

  config.vm.provision :shell, :inline => <<SCRIPT
    sudo mkdir -p /tmp/zookeeper
    sudo chmod 755 /tmp/zookeeper
    sudo chown zookeeper /tmp/zookeeper
    sudo -u zookeeper echo #{ENV['ZK_ID']} > /tmp/zookeeper/myid
    sudo -u zookeeper /opt/chef/embedded/bin/ruby /vagrant/scripts/gen_zoo_conf.rb > /etc/zookeeper/conf/zoo.cfg
    sudo restart zookeeper
SCRIPT

$script = <<SCRIPT
  wget https://dl.bintray.com/mitchellh/serf/0.4.1_linux_amd64.zip
  unzip 0.4.1_linux_amd64.zip
  mv serf /usr/local/bin
  rm 0.4.1_linux_amd64.zip
  wget https://raw.github.com/hashicorp/serf/c15b5f15dec3d735dae1a6d1f3455d4d7b5685e7/ops-misc/upstart.conf
  mv upstart.conf /etc/init/serf-agent.conf
  mkdir /etc/serf
  echo "{ \"start_join\": [\"$1\"], \"protocol\": 3 }" | tee /etc/serf/config.json
  exec start serf-agent
SCRIPT
  config.vm.provision "shell", inline: $script
end
