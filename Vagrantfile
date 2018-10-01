# -*- mode: ruby -*-
# vi: set ft=ruby :
#

agents = ENV['AGENTS'] || 3


#installers = Dir.entries(File.dirname(__FILE__)).select { |f| f =~ /puppet-enterprise.*.tar.gz/ }
#if installers.empty?
#  fail("No installer found, please place a Puppet Enterprise tgz file in the same directory as Vagrantfile")
#end

# If there is more than one installer in the directory we
# use the latest one
#installer = installers.last


# Set ports for exposing Puppet services to the host
network = ENV['PE_VAGRANT_NETWORK'] || '192.168.100'
#console_port = ENV['PE_VAGRANT_CONSOLE_PORT'] || '8443'
#orchestrator_port = ENV['PE_VAGRANT_ORCHESTRATOR_PORT'] || '8143'
#rbac_port = ENV['PE_VAGRANT_RBAC_PORT'] || '4433'

VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = "centos/7"
  config.vm.define 'ansible' do |master|
    master.vm.network :private_network, ip: "#{network}.99"
 #   master.vm.network "forwarded_port", guest: 443, host: console_port
 #   master.vm.network "forwarded_port", guest: 4433, host: rbac_port
 #   master.vm.network "forwarded_port", guest: 8143, host: orchestrator_port
    master.vm.hostname = 'ansible.localdomain'
 #   master.vm.provider :virtualbox do |v|
 #     v.memory = 4096
 #   end

    master.vm.provision :hosts, :sync_hosts => true
    master.vm.provision "shell", inline: %Q{
       yum install -y epel-release
       yum clean all
       yum install -y ansible
       useradd ansible
       echo "ansible ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers
       mkdir /home/ansible/.ssh
       cp /vagrant/ssh/* /home/ansible/.ssh
       chown -R ansible.ansible /home/ansible/.ssh
       chmod 600 /home/ansible/.ssh/auth* /home/ansible/.ssh/config

    }
      
  end
  (1..agents.to_i).each do |sec|
    config.vm.define "agent#{sec}" do |agent|
      agent.vm.hostname = "agent#{sec}.localdomain"
      agent.vm.network :private_network, ip: "#{network}.1#{sec}"
      agent.vm.provision :hosts, :sync_hosts => true
      agent.vm.provision "shell", inline: %Q{
        useradd ansible
        echo "ansible ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers
        mkdir /home/ansible/.ssh
        cp /vagrant/ssh/* /home/ansible/.ssh
        chown -R ansible.ansible /home/ansible/.ssh
        chmod 600 /home/ansible/.ssh/auth* /home/ansible/.ssh/config
      }
    end
  end
  
end


