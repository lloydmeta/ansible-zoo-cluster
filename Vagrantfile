# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  config.vm.box = 'hfm4/centos7'

  memory_mb = 256

  cluster = {
    'node-1' => "192.168.5.100",
    'node-2' => "192.168.5.101",
    'node-3' => "192.168.5.102",
  }

  cluster.each_with_index do |(short_name, ip), idx|

    config.vm.define short_name do |host|
      host.vm.network :forwarded_port, guest: 2181, host: 2181 + idx
      host.vm.network :private_network, ip: ip
      host.vm.hostname = short_name
      host.vm.provider :virtualbox do |vb|
        vb.customize ["modifyvm", :id, "--memory", memory_mb]
      end

      host.vm.provision :ansible do |ansible|
        ansible.playbook = "site.yml"
        ansible.verbose = 'vv'
        ansible.sudo = true
        ansible.extra_vars = {
          accept_oracle_licence: true, # set to false if you don't agree (will not install Java8 for you)
          cluster_node_seq: idx + 1,
          cluster_ip_addresses: cluster.values
        }
      end
    end

  end

end
